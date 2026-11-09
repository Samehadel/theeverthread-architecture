# 0007 — Dedicated self-hosted monitoring EC2

- Status: Proposed for deployment; implementation prepared, server activation pending.
- Requirements: Project owner requested uptime, errors, performance, server health,
  and server logs; selected self-hosted Grafana/Prometheus with dashboard access
  only and no notifications; on 2026-11-09 specified a dedicated monitoring EC2.
- Baseline: `851bb25cf3fd9c09724a3af74dc419479bbbc229` from
  `theeverthread-architecture/main`, reverified on 2026-11-09. Unrelated local
  uncommitted architecture documents were not treated as accepted decisions.

## Decision

Run Grafana, Prometheus, Loki, Blackbox Exporter, a private HTTPS ingestion
Nginx gateway, and Node Exporter on the dedicated Ubuntu monitoring EC2. Run
Alloy and Node Exporter beside the application on its separate EC2. Use independent
Compose projects and storage. App releases manage only the app collector overlay;
monitoring startup, storage, and dashboards have no dependency on app readiness.
The monitoring host requires no application images or database credentials.

Alloy scrapes the backend and app Node Exporter locally, then pushes metrics
through Prometheus remote write and logs through the Loki push API. Both cross-host
connections use HTTPS to the monitoring private IP on TCP 9443, with a dedicated
random ingestion bearer token and certificate verification against a private CA.
The gateway binds only the monitoring EC2 private IPv4 address. Restrict its
security-group ingress to the app EC2 security group. Do not publish collector
ports on the app host. The two hosts require private routing, normally in the same
VPC. This change defines configuration and does not provision EC2 or alter live
security-group rules.

The gateway exposes only authenticated write endpoints and a token-protected
health endpoint. It does not expose Prometheus queries, Loki queries, or their
administration APIs. Grafana remains on monitoring-host loopback, accessed through
an authenticated SSH/SSM tunnel, with separate local credentials and anonymous
access/signup disabled. No notification integration or alert rules are created.

Only GET `/actuator/prometheus` accepts the app-local collector token, of at least
32 characters. Empty or short configuration denies access. Application sessions
and roles grant no collector access, and the token grants no other management or
application access. Public app Nginx returns 404 for this endpoint. The backend
token never leaves the app host; a different ingestion token authorizes cross-host
writes. Enable HTTP latency histograms only through the collector overlay.

## Logs, credentials, and retention

Alloy reads backend/frontend Docker JSON logs and persistent/volatile app-host
system journals from read-only mounts. It has no Docker API socket or privileged
mode. It uses root to read host-owned files, with capabilities dropped. Docker
Compose attributes identify application streams and avoid telemetry feedback
loops. Group backend stack traces. Nginx access logs omit query strings, client
IPs, headers, cookies, and bodies. Other logs can contain sensitive operational
information; limit Grafana access.

Generate ingestion and Grafana credentials plus the private CA on monitoring.
Transfer only the ingestion token and CA certificate to the app host through a
trusted administrative channel. Generate the app-local metrics token there.
Protect runtime directories with mode 0700 and exclude them from Git. The CA
private key stays mode 0600 on monitoring and is never mounted into a container.
IP-specific server certificates last 397 days, with explicit renewal retaining
the CA/token. The management check warns by failing within 30 days of expiry.

Persist metrics, logs, and Grafana state on monitoring volumes; persist app Alloy
reading positions and the metrics write-ahead log on the app host. Retain Prometheus
history for at most 15 days or 2 GB of historical blocks, plus WAL/head overhead.
Loki applies seven-day asynchronous retention without a hard byte quota. Bound
Docker log rotation separately. Size memory and EBS for observed ingestion volume,
and back up monitoring data and protected runtime state. Pending log batches use
memory and finite retries; prolonged ingestion outages or collector restarts can
lose unsent logs. Local Docker rotation is the fallback source. Metrics buffers
are finite too and cannot guarantee delivery through arbitrary outages or disk loss.

## Availability and operation

Run public HTTPS and database-readiness probes from monitoring. An app EC2 outage
remains visible from Grafana while historical telemetry stays available. Treat
backend metrics older than 120 seconds as down rather than showing stale success.
Monitor both hosts' resource metrics. Received remote-write jobs are not local
Prometheus scrape targets; verify fresh backend, server, and Alloy samples in
addition to local collector/probe health and actual Loki log arrival.

App collector activation shares the app deployment lock and recreates app
containers to mount their token, enable histograms, and configure Docker log
labels. Its own enablement marker preserves collectors across future app releases.
Manage or suspend monitoring independently without deleting its volumes. See the
application repository's `deploy/aws/monitoring/README.md` for the two-host setup,
certificate renewal, recovery, and any migration from a previous single-host stack.

## Limitations and verification

The monitoring EC2 remains a single point of monitoring availability; shared VPC
or regional failures may affect both hosts. This is not a highly available
monitoring deployment. Browser JavaScript exceptions, full tracing, Neon provider
internals, monitoring-host journal ingestion, and notification channels are outside
this delivery. Monitoring-host container logs remain available operationally.

Require backend security regressions, role/bootstrap/certificate tests, both
Compose validations, native component configuration checks, and a local cross-host
transport test. The transport check must exercise CA/IP verification, token denial,
metric/log delivery, private query isolation, and continued monitoring availability
after stopping the application backend. Verify live Ubuntu journal ingestion and
actual VPC/security-group connectivity when activating the servers.
