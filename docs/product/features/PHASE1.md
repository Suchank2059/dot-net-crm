# Phase 1: MVP CRM

**Goal:** Give a small sales team one place to manage prospects, customer relationships, opportunities, and follow-up work.

## Organization and access

- Create a tenant through organization signup and onboarding.
- Support email/password authentication, email verification, password reset, and secure session renewal.
- Let tenant administrators invite, manage, and deactivate users.
- Start with a small set of built-in roles, implemented through permissions so access rules can evolve.

## CRM records and sales process

- **Contacts:** people with basic identity and contact details, owner, tags, and source.
- **Companies/accounts:** organizations with basic profile details; link contacts to their companies.
- **Leads:** prospects with a status and source; support conversion into the appropriate customer and sales records.
- **Deals:** opportunities with value, currency, expected close date, owner, stage, and win/loss reason.
- **Pipelines:** stages configurable for a tenant, with a board view for moving deals between stages.

## Follow-up and daily work

- Log calls, meetings, emails, and notes against CRM records.
- Create assigned tasks with due dates and provide a focused view of tasks due today.
- Show a chronological record timeline, including relevant activity and changes.

## Finding and managing information

- Provide searchable, filterable, sortable, paginated list views.
- Support CSV import and export.
- Allow users to organize records with tags.
- Include a basic dashboard for open deals, pipeline value by stage, recently won deals, and overdue tasks.
- Record who changed important data and when.

## MVP quality bar

- Tenant and record-access rules are enforced server-side and covered by cross-tenant tests.
- List endpoints paginate results; imports handle retries safely and report invalid rows.
- Core workflows have automated tests and clear validation feedback.

## Out of scope for this phase

Email/calendar synchronization, workflow automation, advanced reporting, custom fields, billing, and large-scale integrations are later-phase candidates. See [Phase 2](./PHASE2.md) and [Phase 3](./PHASE3.md).