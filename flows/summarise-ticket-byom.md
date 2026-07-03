# SummariseTicket_BYOM

## Purpose

`SummariseTicket_BYOM` is a child flow that generates ticket summaries using Azure OpenAI / Azure AI Foundry and writes the result back to Dataverse.

Azure OpenAI / Azure AI Foundry provides the BYOM model deployment and API key used by the summarisation flow. The API key value is never documented or committed.

## Architecture

The flow uses:

- A stable child-flow input contract.
- Azure OpenAI HTTP call.
- Environment-variable driven configuration.
- Secure API key handling.
- Expression-based AI response parsing.
- TRY/CATCH fallback summary if the model call fails.
- Dataverse summary write-back.

Evidence: [SummariseTicket_BYOM child flow screenshot](../screenshots/summarise-ticket-byom-child-flow-overview.png).

## Environment Variables

The repo may safely list variable names, but it must not contain values.

- `lai_AOAI_ApiKey`
- `lai_AOAI_ApiVersion`
- `lai_AOAI_DeploymentName`
- `lai_AOAI_Endpoint`
- `lai_ITSD_SummarisationPrompt`

Azure OpenAI configuration is externalised through environment variables, including endpoint, deployment name, API version, and summarisation prompt.

The Azure OpenAI API key is handled through secure secret configuration using Azure Key Vault / Power Platform secret environment variables. The key is retrieved at runtime and is not hardcoded in Power Automate flows, documentation, screenshots, or source files.

## Dataverse Write-Back

The flow writes summary results to fields such as:

- `TicketSummary`
- `ConversationalSummary`
- `SummaryGeneratedOn`
- `SummarySource`
- `SummaryError`

## Fallback Behavior

If the Azure OpenAI call fails or the response cannot be parsed, the flow writes a fallback summary and records error context in a public-safe operational field such as `SummaryError`.

## Public Safety

Do not publish endpoint values, API keys, vault URIs, tenant URLs, subscription IDs, directory IDs, request headers, raw HTTP action secrets, or screenshots showing secret configuration.
