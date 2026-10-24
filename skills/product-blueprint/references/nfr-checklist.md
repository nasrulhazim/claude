# NFR Checklist

Requirements a BR document implies but almost never states. Write each one as testable, and trace it back to
the BR IDs it protects.

| Area | Write it as | Typical traces |
|---|---|---|
| Access | SSO via the organisation's IdP (OIDC). Roles granted per brand/tenant. Every write checked by a server-side policy | multi-tenant BR, approval BR |
| Isolation | No query returns data outside the caller's workspace. Enforced by a Pest arch test that fails when a model lacks the workspace scope | multi-tenant BR |
| Timing | Scheduled jobs start within N seconds of the planned time. Stored in UTC with the tenant's IANA timezone (default `Asia/Kuala_Lumpur`) | scheduling BR |
| Exactly-once intent | Never execute the same external action twice for the same version. Idempotency key per target-version; retry only after verification | retry BR |
| Secrets | Third-party tokens encrypted at rest and never returned to the browser or AI clients. Expiry checked daily, owners warned 7 days ahead | integration BR |
| Audit | Append-only, covers human and AI actors, 24-month retention, CSV export | audit BR, AI BR |
| Media | S3-compatible storage. Short-lived signed URLs where a platform pulls media. Size limits checked on upload | asset BR, validation BR |
| Privacy (PDPA) | Personal data stores its consent source and time. Delete-on-request. Never sent to AI unless the tenant opts in | lead BR, AI context BR |
| Observability | Queue depth, failed jobs and per-integration error rates reported to the error tracker. Alert on failure rate > 10%/h | monitoring BR |
| Mobile | Approvals, status and proof upload work at 390 px | approval BR, manual-action BR |

## Risks Table Seeds

| Risk | Usual likelihood | Mitigation pattern |
|---|---|---|
| Third-party API access refused or delayed | High | Apply in Phase 0. Ship the target as a manual step until approved |
| Platform review asks for changes | Medium | Submit early with a screencast; test in dev mode on your own accounts |
| Duplicate action on retry | Medium | Verify-then-retry, per-target lock, fake-driver tests |
| Tokens expire silently | Medium | Daily health check, pre-flight check 1 h before scheduled work |
| Scope creep from planning/AI | Medium | Lock later-phase BRs out of Phase 1 |
| AI cost overrun | Low | Hard caps per tenant, visible usage screen |
