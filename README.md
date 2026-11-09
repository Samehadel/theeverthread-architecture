# Project architecture

This repository holds the version-controlled architecture for `betterf`. Backend and frontend documentation describe their respective responsibilities; shared decisions explain choices that affect the system as a whole.

## Documentation status

This repository currently contains a reusable architecture baseline. BetterF-specific domains, schema, endpoints, routes, authentication behavior, deployment topology remain to be designed. The inherited Spring Boot and Angular technology stack is retained for BetterF.

- The strategic documents define the retained technology stack and reusable engineering rules.
- Backend and frontend guides describe the selected stack. They are implementation guidance, not evidence that the application or build configuration already exists.
- Examples do not define BetterF product requirements. Validate framework-specific APIs against selected versions before implementation.
- Record significant project choices in `decisions/` with **Proposed**, **Accepted**, or **Superseded** status. Accepted decisions govern the applicable implementation.

The accepted [technology baseline](decisions/0001-technology-baseline.md) records the stack and its scope.

Start with the [context index](context-index.md), [backend architecture](backend-architecture.md), [frontend architecture](frontend-architecture.md), [API conventions](api-specification.md), and [development guide](development-guide.md).

## Workspace

```text
betterf/
├── AGENTS.md
├── architecture/       # This repository
│   ├── README.md
│   ├── backend/
│   ├── frontend/
│   └── decisions/
└── app/                
    ├── backend/
    └── frontend/
```

Paths in this README are relative to the architecture repository unless stated otherwise. The directories above describe the intended organization; create them when adding their first document.

## System overview

Refer to `betterf/project-context.md` to understand the context and the scope of the project and the business behind it as well.

## Backend architecture

Start backend documentation in `backend/README.md`. Cover the topics relevant to the application:

- Services, modules, domain boundaries, and responsibilities.
- Business rule enforcement and authorization boundaries.
- API contracts, errors, and compatibility expectations.
- Data models, ownership, migrations, and transactions.
- External integrations, background work, and failure handling.
- Runtime configuration, deployment, observability, and testing strategy.

Keep implementation in the sibling application's `backend/` directory. Link to code or contracts where they clarify the design.

## Frontend architecture

Start frontend documentation in `frontend/README.md`. Cover the topics relevant to the application:

- Application structure, routes, and principal user journeys.
- Component boundaries and shared design conventions.
- State ownership, data fetching, caching, and API integration.
- Authentication flows and how permissions appear in the UI.
- Loading, empty, and error states.
- Accessibility, responsive behavior, testing, and deployment.

Keep implementation in the sibling application's `frontend/` directory. Document how the UI consumes backend contracts; backend authorization remains responsible for protecting server-side operations.

## Shared decisions

- [Proposed dedicated monitoring EC2](decisions/0007-self-hosted-monitoring.md)

- [Accepted application versioning](decisions/0003-application-versioning.md)

- [Proposed development deployment](decisions/0002-development-deployment.md)

Store significant architecture decisions in `decisions/` using descriptive names such as `0001-api-contract-strategy.md`. Each decision should include its status, context, choice, consequences, and relevant business or technical references.

Use explicit statuses such as **Proposed**, **Accepted**, and **Superseded**. Preserve historical decisions and link superseded decisions to their replacements. Document shared contracts once and reference them from both backend and frontend documentation.

## Maintaining this repository

- Keep documentation aligned with approved changes; clearly label proposals and known implementation gaps.
- Explain why a design exists, including relevant constraints and tradeoffs.
- Add document links to this index as documents are created.
- Reference related application changes when a change spans both repositories.
- Use the branch, tag, or commit configured for the task or workspace. Record the commit consulted when reporting an architecture review.
- Keep credentials, generated logs, and private runtime data out of the repository.

Agent working instructions live in the workspace's `AGENTS.md`. This README is the entry point to the architecture itself.
