# Demo Script

This script is designed for a 3-5 minute portfolio walkthrough. Use safe demo data only. Do not show personal emails, tenant URLs, real user details, private ticket content, secrets, API keys, or tokens.

## Demo Goal

Show that `IT Support (Portfolio)` is more than a basic chatbot. It uses configured and tested Entra ID / organizational sign-in, Dataverse ticket storage, secure ownership checks, idempotent escalation logic, notification tracking, and a planned technician handoff path.

## Demo Setup Checklist

- Use a demo account or redact signed-in user details.
- Prepare one new ticket scenario.
- Prepare one already escalated ticket for the `AlreadyEscalated` example.
- Make sure ticket references use `TKT####`, for example `TKT1001`.
- Open Copilot Studio, Power Automate, Dataverse, and the planned `IT Technician Console` if available.
- Confirm screenshots or live views do not expose private data.

## 1. User Sign-In / Authenticated Experience

Show:

- `IT Support (Portfolio)` in Copilot Studio.
- The authentication setting or sign-in experience.

Say:

```text
This agent is designed for an internal helpdesk scenario. The Entra ID / organizational sign-in experience has been configured and tested, so protected actions can use trusted identity instead of asking the user to type an email address.
```

Status wording:

- Use configured and tested for the authenticated Copilot Studio experience.
- Use built and tested for the sign-in requirement and identity-based user context.

## 2. Log A Ticket

Show:

- The user asking to log an IT issue.
- The agent collecting subject, description, category, impact, urgency, and priority.

Say:

```text
The agent collects structured ticket details and passes the signed-in user's identity into Power Automate. Identity-based user context is built and tested, so the user does not need to provide their email for authorization.
```

## 3. Dataverse Ticket Row Created

Show:

- `Helper - Create Ticket` run history or flow.
- The new Dataverse `IT Ticket` row.
- Redacted identity fields if visible.

Say:

```text
The ticket is stored in Dataverse as the system of record. The row includes requestor identity fields such as RequestorAadObjectId, RequestorUPN, and RequestorDisplayName.
```

Do not show:

- Real user email addresses.
- Tenant URLs.
- Private environment details.

## 4. Check Ticket Status

Show:

- The user entering a `TKT####` ticket reference.
- The status response or adaptive card.

Say:

```text
For status checks, the user only provides the ticket reference. The flow checks Dataverse and verifies that the signed-in user owns the ticket before returning details.
```

## 5. Ownership Check Using Signed-In Identity

Show:

- `Helper - Check Status` logic or a sanitized flow screenshot.
- The comparison between `RequestorAadObjectId` and `CurrentUserAadObjectId`.

Say:

```text
This is the main security pattern in the project. The flow compares the ticket's RequestorAadObjectId with the CurrentUserAadObjectId from the signed-in session. If they do not match, the agent returns NotAuthorized.
```

Optional:

- Show a safe `NotAuthorized` demo using test data only.

## 6. Escalate Urgent Ticket

Show:

- The user asks to escalate a ticket.
- The agent asks for the escalation reason.
- `Helper - Escalate Ticket` runs.

Say:

```text
The escalation flow validates the ticket reference, checks whether the ticket exists, checks whether it is already escalated, verifies ownership, and only then updates Dataverse.
```

## 7. Escalation Fields Updated

Show:

- Dataverse row after escalation.
- Redacted view of escalation fields.

Fields to highlight:

- `Escalated`
- `EscalatedOn`
- `EscalatedByAadObjectId`
- `EscalationReason`
- `EscalationCount`
- `NotificationSent`

Say:

```text
The escalation update records who escalated the ticket, when it happened, why it happened, and increments EscalationCount. NotificationSent is used to control downstream notification behavior.
```

## 8. Notification Or Escalation Proof

Show:

- `Automation - Notify Manager` if ready.
- Safe Teams/email notification proof if available.
- Or Dataverse notification tracking fields if notification proof is not ready to publish.

Say:

```text
The notification design uses NotificationSent to avoid duplicate alerts. If notification fails, the intended pattern is to store NotificationError and leave NotificationSent false so it can be retried.
```

Status wording:

- Use work in progress if watcher trigger conditions still need final verification.
- Use built only when the flow has been tested and safe proof is available.

## 9. Technician Console / Escalated Tickets View

Show:

- `IT Technician Console` if available.
- Escalated tickets view if available.
- If not built yet, show the documented design and clearly label it planned.

Say:

```text
The technician console is the planned human handoff point. It gives support staff a model-driven app view of escalated tickets so the agent does not expose all records directly to end users.
```

Status wording:

- Use planned / work in progress unless the model-driven app is visible and safe to show.

## 10. Duplicate Escalation Prevention

Show:

- The same ticket being escalated again.
- The agent returning `AlreadyEscalated`.

Say:

```text
If the user tries to escalate the same ticket again, the flow returns AlreadyEscalated. This prevents repeated escalation updates and supports duplicate notification prevention.
```

## Closing Summary

Say:

```text
This project demonstrates how Copilot Studio can be used as part of a governed helpdesk workflow with authenticated identity, Dataverse authorization checks, Power Automate orchestration, escalation audit fields, and a technician handoff path.
```

## Demo Notes

- Keep the demo focused on the business process and security pattern.
- Avoid showing private configuration values.
- Avoid claiming production deployment.
- Clearly separate built features from planned improvements.
