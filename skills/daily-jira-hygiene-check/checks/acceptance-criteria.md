Daily Acceptance Criteria Checker

Task

Check every active ticket in the current open sprint(s) of the Jira
project specified in the Jira Hygiene Tool Config File (see "Jira
Project Space Name" and "Jira Project Space ID (URL)" for the site) for
a properly defined Acceptance Criteria, and flag the ones that don\'t
have one --- both in Slack and as a comment on the ticket.

Scope (JQL)

project = \<project key specified in the Jira Hygiene Tool Config
File\'s "Jira Project Key" field\> AND type not in (Epic) AND sprint in
openSprints() AND status not in (\"Done\", \"Won\'t Fix\", \"To Do\",
\"To Groom\", \"To Estimate\")

Rules:

DO check all tickets matching the JQL above, regardless of issue type
(Story, Task, Sub-task, Epic, Bug).

DON\'T include tickets in \"Done\" or \"Won\'t Fix\" status, or in a
not-yet-progressing status such as \"To Do\", \"To Groom\", or \"To
Estimate\" --- the check only applies once work has actually started.

DON\'T include tickets outside the currently open sprint(s).

How to judge whether a ticket has Acceptance Criteria

\-- Don\'t rely on a simple keyword match. Read the full description and
use judgment:

A ticket passes (has valid AC) if either of these is true:

It has an explicit \"Acceptance Criteria\" (or similarly named)
section/heading with actual non-empty content underneath it.

OR

It clearly states a goal and an expected/success outcome in prose ---
even without a labeled heading --- such that someone could tell what
\"done\" looks like (e.g., specific expected behaviors, a defined
before/after, concrete deliverables, or a \"Goals\" section with a clear
intended result).

OR

The ticket's summary/title alone already makes the objective and
expected outcome self-evident (e.g., a narrowly-scoped, unambiguous task
where anyone could tell what \"done\" looks like just from the title)
--- in that case, a separate Acceptance Criteria section in the
description is not required.

A ticket fails (missing AC) if:

\- The description is empty, or

\- It only states a vague intent/problem statement with no defined
outcome (e.g., \"this would be nice to have\" with no specifics), or

\- It\'s just attachments/screenshots/links with no explanatory text on
what success looks like.

When in doubt, lean toward flagging it --- better to prompt a quick
human review than let a genuinely underspecified ticket slide.

Actions for each ticket that fails the check

\- Add a comment on the Jira ticket tagging the assignee (fallback to
the reporter if there\'s no assignee) using a real Jira mention --- not
plain text. Use the mention syntax
\[\~accountid:{issue.assignee.accountId}\] or
\[\~accountid:{issue.reporter.accountId}\] so it renders as an actual
tag and notifies them. Comment should be short, e.g.:

\[\~accountid:XXXX\] this ticket is missing clear Acceptance Criteria in
the description. Please add before moving forward --- flagged by daily
AC check.

\- Include it in a single consolidated Slack message posted to the Slack
channel specified in the Jira Hygiene Tool Config File ( "Slack Channel
Name" / "Slack Channel ID"). Do not send one Slack message per ticket
--- batch all flagged tickets from that run into one message. Tag each
relevant assignee with a real Slack \@mention (not their name as plain
text) --- resolve their Slack member ID via their Jira account email
before sending. Format like:

:warning: Daily Acceptance Criteria check --- \<Jira Project Space Name
from Config File\> open sprint

The following tickets are missing clear Acceptance Criteria:

• \<Jira ticket URL\|PROJ-XXX\> --- \<@SlackUserID\> (PROJ = the Jira
Hygiene Tool Config File\'s "Jira Project Key")

• \<Jira ticket URL\|PROJ-XXX\> --- \<@SlackUserID\> (PROJ = the Jira
Hygiene Tool Config File\'s "Jira Project Key")

\...

Please add AC or a clear expected outcome to each. Share a 👍 once it\'s
done.

If no tickets are flagged in a given run, skip the Slack message
entirely (no need to post a \"nothing to report\" message unless asked
otherwise).

De-duplication

Before commenting on a ticket, check its existing comments for one
already left by this same automated check. If the ticket was already
flagged in a prior run and still has no AC, you may still include it in
today\'s Slack summary, and add a duplicate Jira comment for it.

Output

At the end of the run, report: how many tickets were checked, how many
were flagged, and confirmation that the Jira comments and Slack message
were posted successfully (or note any that failed and why).
