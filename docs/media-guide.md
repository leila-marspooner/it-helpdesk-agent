# Media Guide

Use this guide when adding screenshots, diagrams, or demo assets to the public repository.

## Screenshot Checklist

### Essential Screenshots

- Copilot Studio authentication setting.
- `IT Support (Portfolio)` agent overview.
- Log a Ticket topic canvas.
- Check Ticket Status topic canvas.
- Urgent Escalation topic canvas.
- `Helper - Create Ticket` trigger inputs.
- `Helper - Check Status` ownership check.
- `Helper - Escalate Ticket` validation, lookup, idempotency, authorization, and update sections.
- `Automation - Notify Manager` showing `NotificationSent` logic, once verified.
- Dataverse `IT Ticket` / `IT Tickets` columns.
- Example Dataverse ticket row with identity fields redacted.
- Status response or adaptive card.
- `IT Technician Console` escalated tickets view, once built.

### Nice-To-Have Screenshots

- Power Platform solution view for `ITHelpdeskPortfolio`.
- Sanitized flow run history showing successful outcomes.
- Sanitized `AlreadyEscalated` response.
- Sanitized `NotAuthorized` response.
- Safe manager or Teams notification proof.
- Architecture diagram.
- GitHub repo structure after public files are added.

### Screenshots To Avoid

Do not publish screenshots that show:

- Personal email addresses.
- Tenant URLs.
- Real user details.
- Real ticket descriptions.
- Private environment names if they reveal sensitive details.
- Secrets, API keys, passwords, tokens, or connection strings.
- Full browser address bars with tenant-specific URLs.
- Raw private source notes.
- Unredacted Dataverse row IDs if they should not be public.

## Redaction Checklist

Before adding media to `screenshots/`, `assets/`, `architecture/`, or `demo/`, check for:

- Email addresses.
- Tenant names and tenant URLs.
- Environment URLs.
- Real names that should not be public.
- Access tokens or hidden query parameters.
- Connection reference details.
- Private ticket content.
- Internal team names that should not be public.

## Architecture Diagram Recommendation

Recommended title:

```text
IT Helpdesk Agent - Authenticated Ticket And Escalation Flow
```

Recommended blocks:

- Signed-in employee
- `IT Support (Portfolio)`
- Entra ID / organizational sign-in
- `Helper - Create Ticket`
- `Helper - Check Status`
- `Helper - Escalate Ticket`
- Dataverse `IT Ticket` / `IT Tickets`
- `Automation - Notify Manager`
- Manager or Teams notification
- `IT Technician Console`

Recommended flow direction:

```text
User -> Copilot Studio -> Power Automate helper flows -> Dataverse
Dataverse -> Notification watcher -> Manager / Teams
Dataverse -> IT Technician Console -> Technician review
```

Labels to preserve:

- `IT Support (Portfolio)`
- `ITHelpdeskPortfolio`
- `IT Technician Console`
- `Helper - Create Ticket`
- `Helper - Check Status`
- `Helper - Escalate Ticket`
- `Automation - Notify Manager`
- `TKT####`
- `RequestorAadObjectId`
- `CurrentUserAadObjectId`
- `AlreadyEscalated`
- `NotAuthorized`
- `NotFound`
- `InvalidFormat`
- `Success`

Do not show:

- Tenant URLs.
- Personal accounts.
- Private environment names.
- Connection details.
- Secrets or tokens.

## File Naming Suggestions

Use clear, lowercase filenames:

- `agent-authentication.png`
- `agent-overview.png`
- `log-ticket-topic.png`
- `check-status-topic.png`
- `urgent-escalation-topic.png`
- `create-ticket-flow.png`
- `check-status-authorization.png`
- `escalate-ticket-idempotency.png`
- `dataverse-ticket-columns.png`
- `technician-console-escalated-view.png`
- `already-escalated-response.png`

## Public Media Status

No public screenshots are included yet. Add screenshots only after reviewing and redacting them.
