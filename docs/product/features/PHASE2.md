# Phase 2: Growing CRM

**Goal:** Let teams tailor the CRM to their sales process and automate routine work after the MVP's core workflows are reliable.

## Configuration and collaboration

- Tenant-specific custom fields for CRM entities.
- Multiple pipelines, saved filters, and custom views.
- Teams, hierarchy, and explicit record ownership and sharing rules.
- Duplicate detection and record merge.
- Notifications in the application and by email, including reminders.

## Connected workflows

- Email sending and synchronization with providers such as Gmail or Outlook, with messages logged to relevant records.
- Calendar synchronization and meeting scheduling.
- Workflow automation, such as creating a task or notifying a teammate when a deal changes stage.
- Web forms for lead capture, a public API with API keys, and outbound webhooks.
- File attachments using object storage.

## Sales operations

- A basic product catalog and quote/invoice workflows, including quote documents.
- Reports for funnel conversion, sales-cycle length, team performance, and forecasts.
- Email templates and, where appropriate, bulk email or campaign workflows.
- Billing and subscription management for CRM tenants, with plan entitlements and usage limits.

## Readiness criteria

Prioritize these additions based on validated customer needs. Before launch, define permission behavior for each capability, protect integrations and credentials, and provide monitoring and failure handling for asynchronous or provider-dependent workflows.

See [Phase 1](./PHASE1.md) for the MVP foundation and [Phase 3](./PHASE3.md) for later-scale capabilities.
