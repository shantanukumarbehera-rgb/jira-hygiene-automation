Daily Ticket Hygiene Check

Task

Check every active, assigned ticket in a progressing status in the
current open sprint(s) of the Jira project specified in the Jira Hygiene
Tool Config File (see "Jira Project Space Name" and "Jira Project Space
ID (URL)" for the site) for three hygiene fields --- Priority, Original
Estimate, and Due Date --- and flag any gaps both in Slack and as a
comment on the ticket.

This enforces the existing team guardrail: \"Once an item is moved to
\'In Progress,\' please ensure the Original Estimate, Priority, and Due
Date are populated.\"

Scope (JQL)

project = \<project key specified in the Jira Hygiene Tool Config
File\'s "Jira Project Key" field\> AND type not in (Epic) AND sprint in
openSprints() AND assignee is not EMPTY AND status not in (\"To Groom\",
\"Grooming - In Progress\", \"Grooming - Tech Detailing\", \"To
Estimate\", \"To Do\", \"Done\", \"Won\'t Fix\")

Rules:

DO check all tickets matching the JQL above, regardless of issue type
(Story, Task, Sub-task, Epic, Bug).

DON\'T include tickets in a non-progressing status: \"To Groom\",
\"Grooming - In Progress\", \"Grooming - Tech Detailing\", \"To
Estimate\", \"To Do\" --- these fields aren\'t expected to be finalized
until work actually starts.

DON\'T include tickets in a completed status: \"Done\", \"Won\'t Fix\".

DON\'T include tickets outside the currently open sprint(s).

DON\'T include unassigned tickets.

Pull the actual workflow status list for the project specified in the
Jira Hygiene Tool Config File, same as the sibling routines. Treat the
explicit non-progressing and completed statuses above as the exclusion
list; treat every other status as a progressing status in scope.

The three checks

Independent checks --- a ticket can fail none, one, two, or all three.

Check 1 --- Missing Priority. Fails if the ticket\'s Priority field is
empty/unset.

Check 2 --- Missing Original Estimate. Fails if the ticket\'s Original
Estimate field is empty/unset (zero counts as unset only if the field
was never actually populated --- don\'t flag a deliberately-set 0 the
same way as a blank field, if that distinction is visible).

Check 3 --- Old/No Due Date. Fails if either:

The Due Date field is empty/unset, or

The Due Date is set but has already passed (earlier than today) while
the ticket is still open in a progressing status.

Action for each ticket that fails one or more checks

Do both of the following --- this routine posts to Slack and comments on
the ticket, unlike the commenting-guardrail routine which is Jira-only.

1\. Comment on the Jira ticket

Add a comment tagging the current assignee (this check is about the
ticket\'s own field state, not about who performed an action, so tagging
the assignee --- the person responsible for keeping the ticket\'s fields
current --- is correct here, unlike the changelog-based tagging used in
the commenting-guardrail routine). Use a real Jira mention via
\[\~accountid:{accountId}\], not plain text. Combine all failing checks
for that ticket into one comment, e.g.:

\[\~accountid:XXXX\] this ticket is missing: Priority, Original
Estimate. Please update these fields per our hygiene guardrails.

List only the fields that are actually missing for that specific ticket
--- don\'t list all three if only one or two are failing.

2\. Post a consolidated Slack message

Post to the Slack channel specified in the Jira Hygiene Tool Config File
("Slack Channel Name" / "Slack Channel ID") --- same channel as the
Acceptance Criteria routine, since this is the same project\'s hygiene
reporting. Post directly without asking for approval.

Tag people using real Slack \@mentions, not plain-text names --- resolve
each person\'s Slack member ID via their Jira account email before
posting.

Group by person within each check --- if the same person has multiple
flagged tickets under the same check, combine all of their ticket links
onto a single line, comma-separated, followed by their mention once at
the end.

Format:

:broom: \<Jira Project Space Name from Config File\> Ticket Hygiene
Check --- \<date\>

:red_circle: Missing Priority

\- \<Jira ticket URL\|PROJ-XXX\>, \<Jira ticket URL\|PROJ-YYY\> ---
\<@SlackUserID\> (PROJ = the Jira Hygiene Tool Config File\'s "Jira
Project Key")

\...

:stopwatch: Missing Original Estimate

\- \<Jira ticket URL\|PROJ-XXX\>, \<Jira ticket URL\|PROJ-YYY\> ---
\<@SlackUserID\> (PROJ = the Jira Hygiene Tool Config File\'s "Jira
Project Key")

\...

:calendar: Old/No Due Date

\- \<Jira ticket URL\|PROJ-XXX\>, \<Jira ticket URL\|PROJ-YYY\> ---
\<@SlackUserID\> (PROJ = the Jira Hygiene Tool Config File\'s "Jira
Project Key")

\...

Tickets checked: \<N\> \| Flagged: \<N\>

Missing Priority: \<N\>,

Missing Original Estimate: \<N\>,

Old/No Due Date: \<N\>

Omit any section with zero flagged tickets. If nothing is flagged across
all three checks, skip the Slack message entirely (consistent with the
AC checker\'s behavior --- no need to announce a clean run in the
regular channel).

De-duplication

Dedupe per check per ticket, not per ticket overall --- a ticket already
flagged today for missing Priority shouldn\'t block a newly-detected
missing Due Date from being flagged too. Before including a given
check\'s line (in either the Slack message or the Jira comment), check
the ticket\'s existing comments from this same automated check within
the last 24 hours; if that specific check was already flagged in that
window, drop it --- but still include any other check that\'s newly
failing.

Output

At the end of the run, report: how many tickets were checked, how many
were flagged, a breakdown by check (Missing Priority / Missing Original
Estimate / Old-No Due Date), and confirmation that both the Jira
comments and the Slack message were posted successfully (or note any
that failed and why).
