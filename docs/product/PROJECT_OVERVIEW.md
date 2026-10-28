# CRM Product Overview

## What we are building

A web-based customer relationship management product for small teams to organize prospects, customer relationships, sales opportunities, and follow-up work. Its core purpose is to help a team answer: **Who are we talking to, where does the relationship stand, and what should happen next?**

The intended product journey is to capture a lead, qualify and convert it, progress a deal through a pipeline, record interactions and tasks, and give the team useful visibility into its sales work.

## Product outcomes

- Reduce lost leads through timely, visible follow-up.
- Give sales teams a shared, dependable view of customers and opportunities.
- Help managers understand pipeline health and expected outcomes.
- Replace disconnected spreadsheets with a practical source of truth.

## Intended users

- **Sales representatives** need a clear daily work list and fast record updates.
- **Sales managers** need team and pipeline visibility.
- **Business owners and administrators** need control over setup, access, and cost.
- **Customer contacts** are represented in the CRM but do not use the internal application.

## Product direction

Start with a focused MVP for small teams, then expand based on validated customer needs. A well-defined target market or niche should guide which workflows are built and how much configuration the product needs. Competing feature-for-feature with broad enterprise CRMs is not the initial goal.

The recommended technical direction is an ASP.NET Core API and Vue.js frontend, starting as a modular monolith. Multi-tenancy and server-side authorization are foundational product constraints, not later add-ons.

## Scope at a glance

| Stage | Product focus |
| --- | --- |
| Phase 1 | Secure organization access, core CRM records, deals and pipelines, daily follow-up, and basic visibility |
| Phase 2 | Team customization, integrations, automation, and richer sales operations |
| Phase 3 | Enterprise-scale governance, ecosystem, service, and analytics capabilities |

These are planning stages, not delivery commitments. Prioritize each capability against user needs and operational readiness.

## Product principles

- Keep tenant data isolated and access control enforced by the server.
- Make the most common sales actions quick and understandable.
- Capture useful history so teams can see what has happened and what needs attention.
- Build extensibility where it supports a real product need; avoid premature complexity.
- Treat privacy, security, accessibility, and data integrity as part of the product.

## Documentation map

- [Feature roadmap](./features/PHASES.md): a summary and navigation for all phases.
- [Phase 1](./features/PHASE1.md): MVP feature scope and quality bar.
- [Phase 2](./features/PHASE2.md): growing-team capabilities and integrations.
- [Phase 3](./features/PHASE3.md): advanced and large-scale capabilities.
- [Architecture](./architecture.md): tenancy, authorization, technical direction, and quality attributes.

Keep detailed requirements in the relevant feature or architecture document rather than expanding this overview into a complete specification.
