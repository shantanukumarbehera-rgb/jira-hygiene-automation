---
name: daily-jira-hygiene-check
description: Runs four daily Jira sprint hygiene checks (Commenting Guardrail, Ticket Hygiene, Acceptance Criteria, Tempo Logging) against the project defined in the Jira Hygiene Tool Config File, posts results to Jira/Slack per each check's own rules, and shares a consolidated response with the connected application. Use when asked to run the daily hygiene check, ticket hygiene check, AC checker, commenting guardrail check, or tempo logging check.
---

# Daily Jira Hygiene Check

Runs four independent checks against the current open sprint(s) of the Jira project
defined in the **Jira Hygiene Tool Config File** (fields: Jira Project Space Name,
Jira Project Space ID/URL, Jira Project Key, Slack Channel Name, Slack Channel ID,
Confluence Project Space Name). Run all four checks every time this skill is invoked,
regardless of trigger source (manual Submit click or the Mon–Fri 09:00 schedule).

Run the checks in this order:

1. **Commenting Guardrail Check** — see `checks/commenting-guardrail.md`
2. **Ticket Hygiene Check** (Due Date / Original Estimate / Priority) — see `checks/ticket-hygiene.md`
3. **Acceptance Criteria Checker** — see `checks/acceptance-criteria.md`
4. **Tempo Logging Check** — see `checks/tempo-logging.md`

Each check file contains its own JQL scope, pass/fail rules, comment templates, Slack
message format, and de-duplication logic — follow each one exactly as written; do not
merge or reinterpret their rules against each other.

## Step 5 — Share the consolidated response with the application

After all four checks have completed (posted their own Jira comments and Slack
messages per their individual instructions), send one consolidated summary to the
connected application.

**Application endpoint:** `<TO BE PROVIDED — application URL/API endpoint>`

Payload should include, at minimum, for each of the four checks:
- Check name
- Tickets checked
- Tickets flagged (broken out by sub-check, where applicable)
- Link to the Slack message posted (if any)
- Any failures/errors encountered while posting to Jira or Slack

Suggested JSON shape:

```json
{
  "run_date": "<ISO date>",
  "trigger": "manual | scheduled",
  "results": [
    {
      "check": "Commenting Guardrail Check",
      "tickets_checked": 0,
      "tickets_flagged": 0,
      "breakdown": { "missing_daily_update": 0, "status_change_not_explained": 0, "assignee_change_not_explained": 0 },
      "slack_permalink": "",
      "errors": []
    },
    {
      "check": "Ticket Hygiene Check",
      "tickets_checked": 0,
      "tickets_flagged": 0,
      "breakdown": { "missing_priority": 0, "missing_original_estimate": 0, "old_or_no_due_date": 0 },
      "slack_permalink": "",
      "errors": []
    },
    {
      "check": "Acceptance Criteria Checker",
      "tickets_checked": 0,
      "tickets_flagged": 0,
      "slack_permalink": "",
      "errors": []
    },
    {
      "check": "Tempo Logging Check",
      "tickets_checked": 0,
      "tickets_flagged": 0,
      "slack_permalink": "",
      "errors": []
    }
  ]
}
```

Send this payload to the application endpoint once per run, after all four checks
finish — not incrementally after each check. If the endpoint call fails, report the
failure in the final output (see below) rather than retrying silently.

## Final output

At the end of the run, report:
- A one-line summary per check (tickets checked / flagged)
- Confirmation that Jira comments and Slack messages posted successfully for each check (or note failures)
- Confirmation that the consolidated response was sent to the application (or note the failure/reason)
