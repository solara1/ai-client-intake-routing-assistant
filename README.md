# AI Client Intake and Routing Assistant for a Career Advice Project

## Project Overview

This project automates the initial review and routing of potential clients for a career advice project.

When a potential client submits an intake, the team needs to review information about their professional situation, experience, goals, and main challenges in order to understand what type of support may be most relevant.

The workflow combines information received through a Webhook with service information stored in Google Sheets. An AI Agent analyzes both sources and decides whether it can recommend a next step or whether the case requires human review.

The objective is not to fully automate client decisions, but to reduce repetitive manual work while keeping human intervention for ambiguous or out-of-scope cases.

## Setup

1. Import `workflow-ai-client-routing-public.json` into n8n.
2. Connect your own credentials for:
   - Google Sheets
   - Gmail
   - OpenAI
3. Replace `YOUR_GOOGLE_SHEET_ID` with the ID of your own Google Sheet.
4. Make sure the spreadsheet contains:
   - a `Services` sheet with the service reference data;
   - a `Routing Results` sheet with the expected output columns.
5. Replace `your-email@example.com` with the email address that should receive human-review notifications.
6. Activate the workflow and test the Webhook using a POST request with the expected intake fields.

No credentials, API keys, or personal data are included in the public workflow file.

### Required configuration

The public JSON uses placeholders instead of real credentials or personal identifiers. These values must be configured after import:

- `YOUR_GOOGLE_SHEET_ID`
- `your-email@example.com`
- n8n credentials for Google Sheets
- n8n credentials for Gmail
- AI model access in n8n (OpenAI or the configured AI provider)

## Business Case

The process being automated is the first review and classification of potential client intakes.

Each intake includes information such as:

- current professional situation;
- role or area of interest;
- years of experience;
- professional goal;
- main challenge or support needed.

The automated workflow:

1. Receives the client intake through a Webhook.
2. Validates that the minimum required information is available.
3. Reads service information from Google Sheets.
4. Combines the client data with the available service information.
5. Uses an AI Agent to analyze the case.
6. Decides whether the case can be routed automatically or requires human review.
7. Records the result and notifies the team when human intervention is needed.

## ROI Estimate

Reviewing one intake manually takes approximately 10–15 minutes.

With an estimated 20 intakes per week, the current manual workload is:

- 200–300 minutes per week;
- approximately 3 hours 20 minutes to 5 hours per week;
- approximately 13–20 hours per month.

If the workflow automates the first review for around 80% of cases, while leaving ambiguous cases for human review, the estimated time saved would be approximately:

- 2.7–4 hours per week;
- 11–16 hours per month.

The main benefit is not only time saved, but also scalability. As intake volume increases, the automated first review can process additional cases without increasing manual effort at the same rate.

The automation would stop making sense if intake volume were extremely low, implementation and maintenance costs became higher than the time saved, or the majority of cases required human judgment.

### Implementation and Payback

The initial implementation effort was approximately 4–6 hours, including workflow design, prompt development, integration setup, testing and documentation.

At the current expected volume, AI model and automation costs should remain relatively low because the workflow performs one short routing decision per intake.

With an estimated 11–16 hours saved per month, the initial implementation effort could be recovered within the first month of regular use.

The exact financial payback depends on the value assigned to the team's time and the actual volume of incoming requests.

## Workflow

`Webhook → Input Validation → Google Sheets → AI Agent → Routing Decision → Google Sheets / Gmail`

The workflow uses two different input sources:

- **Webhook:** receives the potential client's intake data.
- **Google Sheets:** provides reference information about the available career advice services.

The AI Agent analyzes both sources and decides whether the case can be routed automatically or requires human review.

## Why an AI Agent?

An AI Agent is used because this step requires more than generating text.

The agent needs to evaluate the client's context together with the available service information and make a routing decision between different possible outcomes.

A simple LLM chain would be appropriate for a fixed transformation or text-generation task, but in this workflow the model needs to apply decision rules, handle ambiguity, and determine when human intervention is required.

## Model Choice: Cloud vs Local

This workflow uses **OpenAI gpt-5-mini** through n8n.

A cloud model was selected because the expected volume is low to moderate and the routing task does not require local infrastructure or specialized hardware.

For this use case, gpt-5-mini provides enough reasoning capability to compare client needs against the available services and decide between automatic routing and human review.

The main advantages are:
- simple integration with n8n;
- low infrastructure overhead;
- good availability and response time;
- usage-based cost suitable for the expected volume.

The main limitation is dependency on an external provider. If volume increased significantly, a hybrid or local approach could be evaluated for simpler classification tasks.

## System Prompt

```text
You are an internal routing assistant for a career advice project.

Your role is to review a potential client's intake together with the available service information and determine the most appropriate next step.

Use only the client information and service information provided in the workflow input.

Your responsibilities:
- identify the client's main need;
- compare that need with the available services;
- recommend the most relevant service when there is a clear match;
- route the case to human review when the information is ambiguous, incomplete, outside the available services, or requires human judgment.

Rules:
- Do not invent information about the client.
- Do not invent services, prices, availability, guarantees, or outcomes.
- Do not recommend a service that is not included in the provided service information.
- Do not make a confident recommendation if more than one option appears equally plausible.
- Do not make decisions that require a human commitment or exception.

If the case requires human review, begin the response exactly with:

[HUMAN_REVIEW]

If the case can be routed automatically, begin the response exactly with:

[ROUTE]

After the tag, include the recommended service and a brief reason.
```

## Error Handling and Edge Cases

### 1. Missing or incomplete intake data

An IF validation step checks whether the minimum required client information is available before the AI Agent processes the case.

If key information is missing, the workflow stops the normal routing process and records the case as `invalid_intake` in Google Sheets. This prevents incomplete requests from reaching the AI Agent.

### 2. Ambiguous or out-of-scope cases

The System Prompt instructs the AI Agent to use the `[HUMAN_REVIEW]` tag when the case cannot be handled safely or confidently.

An IF node checks the agent output for this tag and sends those cases through the human-review path.

### 3. Integration failures

External services such as Google Sheets, Gmail or the AI provider may fail because of connection, permission or authentication issues.

The current version does not include a dedicated API-failure recovery branch. A future improvement would be to add n8n `Error Trigger` or `onError` handling to log failed executions and notify an operator.

## Outputs

The workflow produces three possible outcomes:

- **Automatic routing:** the recommendation is recorded in Google Sheets with the status `automatic`.
- **Human review:** the case is flagged, the team is notified by email, and the result is recorded with the status `human_review`.
- **Invalid intake:** requests missing required information are recorded with the status `invalid_intake` without being sent to the AI Agent.

## Testing

The workflow was tested with three scenarios:

1. A complete intake that can be routed automatically.
2. A case that explicitly requires human review.
3. An intake with missing or invalid data.

The screenshots below show the workflow canvas and successful test executions.

## Screenshots

### Workflow Overview

![Workflow Overview](Workflow.png)

### Successful Automatic Routing

![Successful Automatic Routing](Log%20Automatic.png)

### Human Review Path

![Human Review Path](Log%20Human%20Review.png)

### Error Handling

![Invalid Intake Handling](Log%20Invalid.png)

## Repository Contents

```text

client-intake-routing-assistant/
├── README.md
├── workflow-ai-client-routing-public.json
├── Workflow.png
├── Log Automatic.png
├── Log Human Review.png
└── Log Invalid.png

```

## Security Note

No credentials or API keys are included in the exported workflow.
