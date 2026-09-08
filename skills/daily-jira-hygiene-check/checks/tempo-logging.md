Daily Tempo Logging Check

Task

Check every ticket not of type Epic, in a progressing status (\"In
Progress\", \"Ready for Code Review\", \"QA In Progress\"), in the
current open sprint(s) of the Jira project specified in the Jira Hygiene
Tool Config File (see "Jira Project Space Name" and "Jira Project Space
ID (URL)" for the site), to confirm the assigned user has logged their
hours --- and flag the ones that haven\'t, both in Slack and as a
comment on the ticket.

Scope (JQL)

project = \<project key specified in the Jira Hygiene Tool Config
File\'s "Jira Project Key" field\> AND sprint in openSprints() AND
assignee is not EMPTY AND issuetype != Epic AND status in (\"In
Progress\", \"Ready for Code Review\", \"QA In Progress\")

Rules:

DO check all matching tickets regardless of issue type, except Epics
(Story, Task, Sub-task, Bug are all in scope).

DON\'T include tickets outside \"In Progress\", \"Ready for Code
Review\", or \"QA In Progress\"; only these three are in scope here per
your instruction.

DON\'T include tickets outside the currently open sprint(s).

DON\'T include unassigned tickets.

The check

Check --- Time logged. Pull the ticket\'s Worklog data (Jira\'s native
Worklogs/History --- Tempo entries write directly into this, e.g. shown
as \"Timesheets by Tempo - Jira Time Tracking\" activity with the real
logging user and time spent, so no separate Tempo API access is needed).
For the current assignee, check whether they have a worklog entry with a
\"started\"/work date within the last 24 hours.

If they have not logged any hours within the last 24 hours, this check
fails --- unless the current assignee has held the ticket for 2 hours or
less as of the time of this run (same grace period logic as the
commenting-guardrail routine: check the changelog for when the assignee
field was last set to them; if within the last 2 hours, skip this ticket
for this run).

Action for each ticket that fails the check

Do both --- comment on Jira and post to Slack.

1\. Comment on the Jira ticket

Add a comment tagging the current assignee via a real Jira mention,
\[\~accountid:{accountId}\] --- not plain text:

\[\~accountid:XXXX\] no hours have been logged against this ticket in
the last 24 hours. Please log your time to keep the sprint\'s tracking
accurate.

2\. Post a consolidated Slack message

Post to the Slack channel specified in the Jira Hygiene Tool Config File
("Slack Channel Name" / "Slack Channel ID"). Post directly without
asking for approval. Tag people using real Slack \@mentions --- resolve
each person\'s Slack member ID via their Jira account email before
posting.

Group by person --- if the same person has multiple flagged tickets,
combine all their ticket links onto a single line, comma-separated,
followed by their mention once.

Format:

:stopwatch: \<Jira Project Space Name from Config File\> Time Logging
Check --- \<date\>

:red_circle: No hours logged in the last 24 hours

\- \<Jira ticket URL\|PROJ-XXX\>, \<Jira ticket URL\|PROJ-YYY\> ---
\<@SlackUserID\> (PROJ = the Jira Hygiene Tool Config File\'s "Jira
Project Key")

\...

Tickets checked: \<N\> \| Flagged: \<N\>

If nothing is flagged, skip the Slack message entirely.

De-duplication

Before flagging a ticket for a given person, check the ticket\'s
existing comments for one already left by this same automated check
within the last 24 hours. If already flagged in that window, skip adding
a duplicate.

Output

At the end of the run, report: how many tickets were checked, how many
were flagged, and confirmation that both the Jira comments and the Slack
message posted successfully (or note any that failed and why).
