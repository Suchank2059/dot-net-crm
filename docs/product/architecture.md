# Product Architecture

This document records the current architectural direction and the constraints that should guide implementation. It is a starting point, not a commitment to adopt every technology listed.

## Application shape

- **Backend:** ASP.NET Core Web API with EF Core.
- **Database:** MySQL 8.4, accessed through Oracle's `MySql.EntityFrameworkCore` provider. Configure the connection through `ConnectionStrings__DefaultConnection`; do not commit credentials.
- **Frontend:** Vue 3, TypeScript, and Vite.
- **Deployment:** containerized application, initially operated as a modular monolith.
- **Modules:** identity and tenancy, CRM records, sales workflows, work management, and reporting.

Keep module boundaries clear so the application can evolve without taking on the operational cost of microservices too early.

## Tenancy and data isolation

One application serves multiple customer organizations (tenants). A tenant must never be able to read or change another tenant's data.

Start with a shared database and shared schema, with a `TenantId` on tenant-owned records and EF Core global query filters. Tenant identity must be resolved from authenticated server-side context; do not trust a tenant identifier supplied only by the client. Enforce tenant scoping in writes and authorization as well as reads, and add tests that attempt cross-tenant access.

Other options, if isolation or scale requirements change:

| Strategy | Trade-off |
| --- | --- |
| Shared database and schema | Lowest operating cost; requires disciplined tenant scoping |
| Schema per tenant | More isolation; more complex migrations |
| Database per tenant | Strongest isolation and independent operations; highest cost |

Keep persistence and tenant-connection resolution flexible enough to consider dedicated databases for selected tenants later, if there is a real requirement.

## Authorization

Use permissions as the authorization primitive; roles are bundles of permissions. Example permissions include `leads.read`, `deals.update`, and `users.manage`. Enforce authorization on the server for every protected operation.

Access is scoped within a tenant too. For example, a sales representative may access owned records, a manager their team's records, and an administrator all tenant records. Treat record ownership and team visibility as explicit policies rather than relying on UI filtering or role names.

## Data and extensibility

- Put `TenantId` first in indexes for common tenant-scoped queries where appropriate.
- Use pagination for list endpoints and avoid N+1 database queries.
- Keep audit history, soft-delete behavior, optimistic concurrency, and idempotent imports in mind for data integrity.
- Decide on a custom-field representation only when that feature is prioritized. JSON-backed fields are a simpler starting point; entity-attribute-value models offer flexibility with more query complexity.
- Prefer versioned APIs, internal events where useful, and feature entitlements for plan-gated capabilities.

## Operational quality attributes

- **Security:** validate input, protect secrets, use secure password storage and transport, rate-limit sensitive endpoints, and verify tenant isolation with automated tests.
- **Performance:** measure representative list and detail requests; paginate and index before optimizing based on assumptions.
- **Reliability:** add health checks, backups with restore tests, and graceful deployment behavior.
- **Observability:** use structured logs, metrics, and tracing, with tenant context handled carefully and without leaking sensitive data.
- **Maintainability:** keep module boundaries understandable and cover core business and access-control behavior with automated tests.
- **Usability:** support responsive layouts, accessible interactions, and timezone-aware handling (store timestamps in UTC).

## Candidate implementation patterns

Use ASP.NET Core dependency injection, middleware for request context, and authorization policies/handlers. Add validation and background processing when the relevant workflows need them. On the frontend, use Vue Router for navigation and permission-aware UX, and a server-state approach and component library that fit the implementation.

Technology choices such as a job scheduler, UI component library, cache, queue, and infrastructure-as-code tool remain implementation decisions; adopt them when justified by product needs rather than by this blueprint alone.

## Related product documentation

- [Product overview](./CRM%20Project%20Blueprint%20Features,%20Multi-Tenancy%20and%20Architecture%20%28.NET%20%2B%20Vue.js%29.md)
- [Feature phases](./features/)
