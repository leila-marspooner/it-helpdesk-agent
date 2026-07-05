# Screenshot Gallery

This folder contains redacted public demo evidence for the IT Helpdesk Agent portfolio project. All ticket content shown is fictional demo data.

IT Helpdesk Agent is the public portfolio project name. The Copilot Studio agent shown in these screenshots uses the display name `IT Support Assistant`.

Screenshots must never show tenant URLs, personal emails, environment IDs, secrets, API keys, tokens, private user data, real ticket content, endpoint URLs, subscription IDs, directory IDs, vault URIs, or personal account details. Azure screenshots (including any Key Vault evidence) are published only after redacting vault URIs, subscription IDs, directory IDs, tenant and resource group names, endpoint URLs, and account details.

## User Experience

- [Chat log ticket](it-support-copilot-chat-log-ticket.png) - Shows the user journey for logging a ticket through `IT Support Assistant`.
- [Ticket created](it-support-copilot-chat-ticket-created.png) - Shows the agent returning a `TKT####` reference after ticket creation.
- [Check status](it-support-copilot-chat-check-status.png) - Shows the signed-in user checking ticket status.
- [Escalation confirmed](it-support-copilot-chat-escalate-confirmed.png) - Shows the user-facing confirmation after an urgent escalation.
- [Already escalated](escalate-ticket-already-escalated.png) - Shows duplicate escalation prevention with the `AlreadyEscalated` outcome.
- [Chat overview](it-support-copilot-chat.webp) - Shows the broader Copilot chat experience.

## Copilot Studio

- [Agent overview](copilot-studio-agent-overview.png) - Shows the `IT Support Assistant` agent in Copilot Studio.
- [Custom topics](copilot-studio-agent-custom-topics.png) - Shows the custom topic set for the agent.
- [Authentication settings](copilot-studio-authentication-settings.png) - Shows the authenticated Entra ID / organizational sign-in configuration with sensitive values redacted.
- [Log ticket topic](copilot-topic-log-a-ticket-full-flow.png) - Shows the full structured ticket intake topic.
- [Urgent escalation topic](copilot-topic-urgent-escalation-full-flow.png) - Shows the full urgent escalation topic.

## Power Automate

- [Helper - Escalate Ticket flow](power-automate-escalate-ticket-flow-full.png) - Shows the escalation helper flow structure.
- [Escalation watcher overview](escalation-watcher-flow-overview.png) - Shows the watcher pattern for escalated ticket processing.
- [Escalation watcher switch branches](escalation-watcher-flow-switch-branches.png) - Shows branch handling in the escalation watcher.
- [Escalation watcher switch default](escalation-watcher-flow-switch-default.png) - Shows the default branch used for controlled watcher behavior.
- [SummariseTicket_BYOM child flow](summarise-ticket-byom-child-flow-overview.png) - Shows the BYOM summarisation child flow with secret values excluded.

## Dataverse

- [IT Ticket table](dataverse-it-ticket-table.webp) - Shows the Dataverse `IT Ticket` table with private identity and ticket content redacted.
- [Model-driven ticket form](model-driven-app-ticket-form.png) - Shows the technician-facing ticket form with private fields redacted.

## Teams

- [Adaptive Card urgent escalation](teams-adaptive-card-urgent-escalation.webp) - Shows the Teams notification card used for urgent escalation acknowledgement.

## Security And Governance

- [Security roles](it-security-roles.png) - Shows role configuration evidence for least-privilege planning.
- [Column security profile permissions](column-security-profile-managers-permissions.png) - Shows Field-Level Security evidence for protected fields.
- [Authentication settings](copilot-studio-authentication-settings.png) - Shows sign-in configuration evidence with client and tenant identifiers redacted.

## Technician Console

- [Dashboard impact chart](it-technician-console-dashboard-impact-chart.png) - Shows technician dashboard reporting by ticket impact.
- [Urgent escalations view](it-technician-console-urgent-escalations-view.png) - Shows the urgent escalation view with ticket subjects redacted.
