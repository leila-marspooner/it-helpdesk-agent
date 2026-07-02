# Build Notes

These notes summarize known build decisions and follow-up items from the private project context. They are written for public portfolio documentation, so they avoid private environment details.

## Build Log

| Area | Notes |
| --- | --- |
| Project direction | V2 authenticated design defined for an internal helpdesk agent |
| Agent | `IT Support (Portfolio)` used as the Copilot Studio agent name |
| Authentication | Entra ID / organizational sign-in configured and tested |
| Sign-in requirement | Built and tested |
| Identity-based user context | Built and tested |
| Solution | `ITHelpdeskPortfolio` used as the Power Platform solution name |
| Ticket storage | Dataverse `IT Ticket` / `IT Tickets` used as the system of record |
| Ticket format | User-facing ticket reference standardized as `TKT####` |
| Authorization | User-typed email removed as the trusted authorization mechanism |
| Ownership check | `RequestorAadObjectId` compared with `CurrentUserAadObjectId` |
| Escalation | `Helper - Escalate Ticket` structure implemented and debugged in context |
| Connections | Run-only connection issue identified and fixed by using the maker-owned connection |
| Technician handoff | `IT Technician Console` planned for escalated ticket visibility |

## Decisions Made

### Use AAD Object ID For Authorization

The project uses AAD Object ID as the trusted ownership field. This is stronger than asking users to type or confirm an email address.

Relevant fields:

- `RequestorAadObjectId`
- `CurrentUserAadObjectId`

### Standardize Ticket References

The final user-facing ticket format is `TKT####`, for example `TKT1001`.

Older notes may mention `TKT-####`, but the no-dash format is the current standard.

### Keep Business Logic In Power Automate

The agent calls helper flows rather than trying to hold all process logic in conversation text. This makes the behavior easier to test, update, and govern.

### Use Dataverse As The System Of Record

Tickets, identity fields, escalation flags, and notification tracking are stored in Dataverse.

### Make Escalation Idempotent

The escalation process checks `AlreadyEscalated` before updating the ticket. This supports duplicate escalation prevention and helps avoid repeated notifications.

### Separate User Self-Service From Technician Handoff

The agent is for employee self-service. The planned `IT Technician Console` is for technician review and triage.

## Issues Encountered

### User-Typed Email Is Not Reliable For Authorization

Using typed email addresses would allow weak ownership checks. The design was changed to use signed-in identity values.

### Dataverse Update Row Needs The Primary Key GUID

The Dataverse Update row action must use the Dataverse row primary key, not the user-facing `TicketID`.

### Ticket ID Format Needed Standardization

The project context included older references to `TKT-####`. The current standard is `TKT####`.

### Run-Only Connection Binding Can Stop Flow Calls

If a Copilot-triggered flow does not run, the issue may be with tool binding, connection settings, environment selection, or run-only user connection configuration.

### Logical Names Need Verification

Dataverse display names and logical names may differ. Some columns may also have mixed prefixes. Flow expressions should use confirmed logical names.

### Disabled Topic Redirects Can Break Conversation Flow

If default or system escalation topics are disabled, fallback and end-of-conversation topics should be checked for redirects to disabled topics.

## Fixes Applied

- Configured and tested Entra ID / organizational sign-in.
- Confirmed the sign-in requirement is built and tested.
- Confirmed identity-based user context is built and tested.
- Removed user email as the authorization mechanism.
- Established AAD Object ID as the trusted ticket ownership field.
- Standardized ticket references to `TKT####`.
- Updated escalation logic to include validation, lookup, idempotency, authorization, update, and return flags.
- Identified and fixed a run-only connection issue by using the maker-owned connection.
- Clarified that Dataverse updates must use the primary key GUID.

## Lessons Learned

- Authentication and authorization are different. Signing in identifies the user, but flows still need to check whether the user is allowed to access a specific ticket.
- Dataverse is useful not only for storage, but also for auditability and role-based access design.
- Idempotency matters in escalation workflows because duplicate alerts can reduce trust in the process.
- Public portfolio documentation should explain design decisions without exposing private tenant details.
- Power Platform GUI changes should be tested before exporting and versioning artifacts.
- GitHub should be used for version history and public documentation, not as a place for private source notes.

## Next Steps

- Confirm final Dataverse logical names for all fields used in expressions.
- Confirm the table display name is consistent across assets.
- Verify `Automation - Notify Manager` trigger conditions.
- Confirm role permissions for helpdesk users and technicians.
- Confirm all flows are included in `ITHelpdeskPortfolio`.
- Add connection references and environment variables where appropriate.
- Export and unpack the solution for source review.
- Add sanitized screenshots.
- Add a simple architecture diagram.
- Complete or document the `IT Technician Console`.
- Add safe sample data and a demo checklist.
