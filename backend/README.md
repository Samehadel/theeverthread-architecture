# Backend Guides

Start with [Backend Architecture](../backend-architecture.md) for the selected technology stack and reusable design rules. These guides apply to that stack; examples do not define BetterF business requirements. See [documentation status](../README.md#documentation-status).

| Topic | Scope |
|---|---|
| [Module structure](module-structure/module-structure.md) | Public/private boundaries and layering |
| [Persistence & JPA](database/persistence-jpa.md) | entity, repository, mapping, and transaction patterns |
| [Database schema](database/database-schema.md) | Outline for future project schema documentation |
| [Database migrations](database/database-migrations.md) | Versioning discipline and Liquibase examples |
| [Security](security/security.md) | Authentication decisions and authorization boundaries |
| [Exception handling](exception-handling/exception-handling.md) | Typed failures, safe messages, and error translation |
| [Response handling](response-handling/response-handling.md) | Response consistency and centralized wrapping |
| [Testing](testing/testing.md) | Unit, integration, and architecture checks |

Product behavior and accepted decisions belong in the strategic documents and `decisions/`. Keep implementation examples aligned with those decisions without duplicating the authoritative rule.

See the [transactional email delivery proposal](../decisions/0007-transactional-email.md) for queue ownership, provider integration and recovery.
