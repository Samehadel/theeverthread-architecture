# Transactional email delivery

Status: Proposed for the reliable email delivery branch.

Identity owns invitation eligibility, verification credentials and workspace access. It records each production email request in PostgreSQL in the same transaction as the business change. A background sender makes the provider call after commit. Each deliberate message gets one unique key and one encrypted request body across retries. Expired worker claims can be recovered while the provider still honors the key. Uncertain submissions beyond that window require investigation.

The provider adapter prepares the wire format, submits messages, retrieves outcomes and authenticates incoming notifications. The application records submission separately from downstream delivery. Verified events are stored before acknowledgement, deduplicated by their provider reference, and associated with an individual message. A periodic lookup recovers current delivery status after a missed notification. A delayed or repeated event must preserve stronger delivery evidence. Email acceptance and delivery never grant workspace access.

Resend is the production provider. Development can use local SMTP capture. Legacy SMTP submissions with ambiguous outcomes require operator investigation at cutover.
