# AI Client Intake and Routing Assistant for a Career Advice Project
## Project Overview

This project automates the initial review and routing of potential clients for a career advice project.

When a potential client submits an intake, the team needs to review information about their professional situation, experience, goals, and main challenges in order to understand what type of support may be most relevant.

The workflow combines information received through a Webhook with service information stored in Google Sheets. An AI Agent analyzes both sources and decides whether it can recommend a next step or whether the case requires human review.

The objective is not to fully automate client decisions, but to reduce repetitive manual work while keeping human intervention for ambiguous or out-of-scope cases.

## Business Case

The process being automated is the first review and classification of potential client intakes.

Each intake includes information such as:

- current professional situation;
- role or area of interest;
- years of experience;
- professional goal;
- main challenge or support needed.

Today, this information needs to be reviewed manually before deciding which service may be relevant and what the next step should be.

The automated workflow will:

1. Receive the client intake through a Webhook.
2. Read service information from Google Sheets.
3. Combine the client data with the available service information.
4. Use an AI Agent to analyze the case.
5. Decide whether the case can be routed automatically or requires human review.
6. Record the result and notify the team when human intervention is needed.

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

The automation would stop making sense if intake volume were extremely low, implementation and maintenance costs became higher than the time saved, or the majority of cases required human judgment and could not be meaningfully classified automatically.
