# CRM Project Blueprint: Features, Multi-Tenancy and Architecture (.NET + Vue.js)

A guide to what a CRM is, how multi-tenancy and roles work, and which functional, business and non-functional features to target, from a small MVP up to a large-scale system.

## What a CRM actually is

CRM stands for Customer Relationship Management. It is a system where a business keeps everything about the people and companies it sells to, and tracks the process of turning them into paying customers and keeping them.

Think of it as a database plus workflow around one question: **"Who are we talking to, what's the status, and what should happen next?"**

The core loop:

1. A **lead** (a possible customer) comes in from a web form, a call, or an import.
2. A salesperson **qualifies** it and converts it into a **contact** and **company**.
3. They open a **deal** (opportunity) and move it through **pipeline stages** (New → Contacted → Proposal → Negotiation → Won/Lost).
4. Along the way they log **activities** (calls, emails, meetings), create **tasks** and **reminders**, and attach **notes** and **files**.
5. Managers look at **reports**: pipeline value, conversion rate, rep performance.

Examples to study: HubSpot (friendly), Pipedrive (pipeline-focused, the best model for an MVP), Zoho CRM, Salesforce (the enterprise end of the scale).

## How multi-tenancy and roles work

These are two separate ideas that are often confused.

### Multi-tenancy

One running application serves many independent customer companies (tenants), and their data is isolated. Company A must never see Company B's data. There are three common strategies:

| Strategy | How | Trade-off |
| --- | --- | --- |
| Shared DB, shared schema | Every table has a `TenantId` column, and every query filters on it | Cheapest and simplest, scales well. Risk: one missed filter leaks data |
| Schema per tenant | One DB, separate schema for each tenant | Better isolation, migrations get harder |
| Database per tenant | Separate DB per tenant | Strongest isolation, per-tenant backups and regions. Costly to operate |

For your start, use **shared DB + `TenantId` column**. In EF Core, a **global query filter** applies the tenant filter automatically to every query, which removes most of the leak risk. Design so you can later move large tenants to their own DB (a tenant-to-connection-string lookup).

### RBAC (role-based access control)

RBAC operates *inside* a tenant. Users belong to a tenant and have a role, and a role is a set of permissions.

- Role examples: Owner, Admin, Manager, Sales Rep, Read-only.
- Permission examples: `leads.read`, `deals.update`, `reports.view`, `users.manage`.
- Build it as **permissions-based**, with roles as bundles of permissions. Check permissions in code, never role names. That is what lets you add custom roles later.
- Add **data scoping** too: a Sales Rep sees only records they own, a Manager sees their team's, an Admin sees all. This is where most real CRM complexity lives.

## Functional features, by phase

### Phase 1: MVP (small CRM)

**Platform and access**

- Tenant (organization) signup and onboarding
- Authentication: email and password, email verification, password reset, refresh tokens
- User invitations, user management, deactivation
- Roles and permissions (start with 3 to 4 fixed roles, permission-based internally)

**Core CRM entities**

- **Contacts** (people): name, email, phone, owner, tags, source
- **Companies/Accounts**: name, domain, industry, size, address; contacts linked to companies
- **Leads**: with status and source, plus a "convert to contact/company/deal" action
- **Deals**: value, currency, expected close date, owner, stage, win/loss reason
- **Pipelines and stages**: configurable stages, kanban board view with drag and drop

**Work management**

- **Activities**: log calls, meetings, emails, notes against any record
- **Tasks** with due dates and assignee, plus a "my tasks today" view
- **Timeline** on each record showing all history

**Usability**

- List views with search, filter, sort, pagination
- CSV import and export (the first thing every real customer asks for)
- Custom tags
- Basic dashboard: open deals, pipeline value by stage, won this month, overdue tasks
- Audit trail: who changed what, when (cheap to add early, painful later)

### Phase 2: mid-level CRM

- **Custom fields** per entity, per tenant (a major architectural decision, see below)
- Multiple pipelines, custom views and saved filters
- **Email integration**: send from the CRM, sync with Gmail or Outlook, log automatically
- **Calendar sync** and meeting scheduling
- **Notifications**: in-app, email, and reminders
- **Workflow automation**: "when a deal reaches stage X, create task Y, notify Z"
- **Teams and hierarchy**, with record ownership rules and sharing
- **Products, quotes and invoices** (a basic catalog and a quote PDF)
- **Reporting**: funnel conversion, sales cycle length, rep leaderboards, forecasts
- **Web forms and lead capture**, plus a public API with API keys
- **Webhooks** (outbound events)
- Duplicate detection and merge
- File attachments (object storage like S3)
- Email templates and bulk email or campaigns
- Billing and subscription for your own tenants (Stripe)

### Phase 3: large-scale CRM

- Territory management, lead scoring and routing, SLA rules
- Approval workflows, advanced permissions (field-level security)
- Marketplace and integrations (Slack, WhatsApp, telephony, accounting)
- Customer service module: ticketing, knowledge base
- Marketing automation and segmentation
- AI features: lead scoring, email drafting, summarization, next-best-action
- Data warehouse and BI exports, advanced analytics
- SSO (SAML/OIDC), SCIM provisioning, data residency, per-tenant encryption keys
- Sandbox environments, a metadata-driven platform (custom objects)

## Business-level view

**Personas**

- *Sales rep*: wants speed, minimal clicks, a clear "what do I do today".
- *Sales manager*: wants pipeline visibility and forecast accuracy.
- *Business owner/admin*: wants setup control, user management, cost.
- *Your customer's customer*: never logs in, but their data is what matters. Privacy rules apply.

**What drives value:** fewer lost leads, faster follow-up, forecast visibility, and one source of truth replacing spreadsheets.

**Target market choice (decide early, it shapes features):** a niche is easier than "general CRM". A CRM for real estate agents, agencies, or clinics lets you hard-code a pipeline and skip heavy customization. Competing with HubSpot head-on is not realistic, so a niche or a very simple, cheap product is the usual path.

**Monetization:** per-seat monthly pricing, tiered plans gated by feature flags (so build a plan/entitlement check early), and usage limits (contacts, emails).

**Compliance basics:** GDPR/privacy (consent, export, delete-on-request), data retention, audit logs.

## Non-functional requirements

| Area | What to aim for |
| --- | --- |
| **Security** | Strict tenant isolation (and tests proving it), OWASP basics, hashed passwords (ASP.NET Identity), JWT or cookie auth with refresh, rate limiting, input validation, secrets management, encryption in transit and at rest |
| **Authorization** | Permission checks server-side on every endpoint, never trust the UI |
| **Performance** | List endpoints under \~300 ms p95, pagination everywhere, indexes starting with `TenantId`, no N+1 queries |
| **Scalability** | Stateless API (horizontal scaling), background jobs for heavy work, cache (Redis), queue for async |
| **Availability** | Health checks, graceful deploys, backups with tested restore, target 99.5%+ early |
| **Observability** | Structured logging (Serilog), metrics and tracing (OpenTelemetry), per-tenant context in every log line, error tracking |
| **Maintainability** | Clean module boundaries, automated tests, CI/CD, migrations in pipeline |
| **Data integrity** | Soft delete, audit trail, optimistic concurrency, idempotent imports |
| **Usability** | Responsive UI, keyboard-friendly tables, accessibility basics, i18n-ready and timezone-aware (store UTC) |
| **Extensibility** | Versioned API, event-driven internal design, feature flags |

## Architecture suggestions for .NET + Vue.js

- **Backend:** ASP.NET Core Web API, EF Core, Mysql. Start as a **modular monolith**: modules like Identity, Tenancy, Contacts, Deals, Activities, Reporting, each with its own boundaries. It is the right shape for "small now, large later", and modules can be split into services if you ever need to.
- **Patterns to learn in .NET:** dependency injection, middleware (resolve tenant per request), authorization policies (permission-based handlers), MediatR or plain handlers for CQRS-lite, FluentValidation, background jobs (Hangfire or Quartz), a tenant provider backed by `IHttpContextAccessor` for the current tenant.
- **Frontend:** Vue 3 with Composition API, TypeScript, Vite, Pinia (state), Vue Router (with permission-based route guards), TanStack Query or similar for server state, and a component library (PrimeVue or Vuetify) so you aren't building data tables from scratch.
- **Custom fields strategy:** the hardest scaling decision. Options are a JSONB column per entity (simple, Postgres handles it well) versus EAV tables (flexible but slow). JSONB is a good middle-level choice.
- **DevOps:** containerize from day one, add CI/CD, and use Terraform for infra. Don't adopt Kubernetes until you need it.

## Suggested build order

1. Auth, tenants, users, roles/permissions (get this right first, it is the foundation)
2. Contacts and companies, with tenant isolation tests
3. Pipelines and deals (kanban)
4. Activities, tasks, timeline
5. Import/export, dashboard, audit log
6. Then Phase 2 features, one at a time

A good practice: write an automated test that logs in as Tenant A and tries to read Tenant B's records on every endpoint. That single test suite protects you as the system grows.

**Possible next steps:** the MVP database schema, the tenant and permission design in ASP.NET Core, or a project folder structure for the modular monolith.
