Daily Commenting Guardrail Check

Task

Check every active, assigned ticket in a progressing status in the
current open sprint(s) of the Jira project specified in the Jira Hygiene
Tool Config File (see "Jira Project Space Name" and "Jira Project Space
ID (URL)" for the site) for compliance with the team\'s commenting
guidelines, and compile a consolidated report of non-compliant tickets
--- for this test, posted to Slack rather than commented on each ticket.

Reference guideline: Commenting on Tickets: Best Practices and
Guidelines (Confluence, space specified in the Jira Hygiene Tool Config
File's "Confluence Project Space Name").

Scope (JQL)

project = \<project key specified in the Jira Hygiene Tool Config
File\'s "Jira Project Key" field\> AND sprint in openSprints() AND type
not in (Epic) AND assignee is not EMPTY AND status not in (\"To Groom\",
\"Grooming - In Progress\", \"Grooming - Tech Detailing\", \"To
Estimate\", \"To Do\", \"Done\", \"Won\'t Fix\")

Rules:

DO check all tickets matching the JQL above, regardless of issue type
(Story, Task, Sub-task, Epic, Bug).

DON\'T include tickets in a non-progressing status: \"To Groom\",
\"Grooming - In Progress\", \"Grooming - Tech Detailing\", \"To
Estimate\", \"To Do\".

DON\'T include tickets in a completed status: \"Done\", \"Won\'t Fix\".

DON\'T include tickets outside the currently open sprint(s).

DON\'T include unassigned tickets.

Pull the project\'s actual workflow status list. Treat the explicit
non-progressing and completed statuses above as the exclusion list;
treat every other status as a progressing status in scope.

The rule, per the guideline

A status-update comment is expected:

At least once a day from the assignee, and

Whenever the ticket\'s workflow status changes --- from whoever made
that change, and

Whenever the ticket\'s assignee changes --- from whoever made that
change.

These are three independent checks, each able to implicate a different
person. Evaluate all three for every ticket in scope, using the fixed
test window above in place of \"now.\"

Check A --- Daily update. If the current assignee has not posted any
comment on the ticket within the fixed 24-hour test window, this check
fails --- unless the current assignee was assigned 2 hours or less
before the window\'s end (i.e., assigned after 3:00 PM ET on Aug 7,
2026). In that case, skip Check A for this ticket.

Check B --- Status change justification. If the status changed within
the fixed test window, identify who made that change. If that person has
not posted a comment timestamped after the change (and before the
window\'s end), this check fails for that person. If no status change
occurred in the window, this check doesn\'t apply.

Check C --- Assignee change justification. If the assignee changed
within the fixed test window, identify who made that change. If that
person has not posted a comment timestamped after the change (and before
the window\'s end), this check fails for that person. If no assignee
change occurred in the window, this check doesn\'t apply.

Excluding Jira Automation actions. Skip any status/assignee change in
the window whose author is an automation/bot actor (e.g., \"Automation
for Jira\") for Checks B and C --- don\'t fall back to blaming a human
for a bot-driven change. For Check A, don\'t count automation/bot-posted
comments as satisfying the human daily-update requirement.

What counts as a valid comment for each check

The content bar differs by check --- don\'t apply the same yardstick to
all three.

For Check A: A comment passes if it clearly conveys (a) what progress
was made and (b) what happens next, per the guideline\'s template ---
whether or not it uses the literal headers (\"Description\" and \"Next
Steps\" are the two always-required fields; Steps Taken, Testing
Instructions, and Deployment Instructions are optional). It fails even
if timestamped correctly if it\'s a trivial one-liner with no real
substance (e.g., \"working on it\").

For Check B and Check C: Don\'t apply the daily-update template here.
These comments only need to state the reason for that specific action
--- no progress details, testing instructions, or next steps required.
For Check B, it passes if it explains why the status was changed (e.g.,
\"Moving to Blocked --- waiting on client API credentials\"). For Check
C, it passes if it explains why the assignee was changed (e.g.,
\"Reassigning to Moumita for QA context\"). A bare acknowledgment with
no reason (\"done,\" \"moved,\" \"ok\") fails.

When in doubt on any check, lean toward flagging it.

Action:

1\. Comment on the Jira ticket

Add a single comment on the Jira ticket covering every check that
failed, tagging a different person per line as applicable --- don\'t
default to tagging only the current assignee. Use a real Jira mention
for each --- not plain text --- via \[\~accountid:{accountId}\] so it
renders as an actual tag and notifies them.

Build the comment from whichever of these apply:

If Check A (daily update) failed:

\[\~accountid:{current assignee\'s accountId}\] no status-update comment
has been posted on this ticket in the last 24 hours. Please share a
quick progress update per our commenting guidelines.

If Check B (status change) failed:

\[\~accountid:{status-changer\'s accountId}\] you moved this ticket\'s
status recently but haven\'t left a comment explaining the update, per
our commenting guidelines. Please add one.

If Check C (assignee change) failed:

\[\~accountid:{assignee-changer\'s accountId}\] you reassigned this
ticket recently but haven\'t left a comment explaining the change, per
our commenting guidelines. Please add one.

If more than one check fails on the same ticket, combine the applicable
lines into one comment rather than posting separately for each. If the
same person would be tagged twice (e.g., they both changed the status
and are the current assignee failing the daily check), only tag them
once and merge the reasons into a single line for them.

If a changelog lookup for Check B or C finds no relevant history at all
(e.g., a very old ticket with no change record within reach), skip that
specific check rather than guessing who to tag.

2\. Post one consolidated Slack message

Instead of commenting on each ticket, compile every check failure across
all tickets in scope into one Slack message posted to the Slack channel
specified in the Jira Hygiene Tool Config File ("Slack Channel Name" /
"Slack Channel ID"). Post directly without asking for approval.

Tag people using real Slack \@mentions, not plain-text names --- resolve
each person\'s Slack member ID via their Jira account email before
posting.

Group by person within each check, rather than one line per ticket.
Within a given check section, if the same person has multiple flagged
tickets, combine all of that person\'s ticket links onto a single line,
comma-separated, followed by their mention once at the end --- don\'t
repeat a person\'s mention across multiple lines in the same section.
Each person still gets their own line; only tickets belonging to the
same person in the same check get merged onto it.

Format:

:test_tube: \<Jira Project Space Name from Config File\> Commenting
Guardrail Check

:large_blue_circle: Missing daily update

\- \<Jira ticket URL\|PROJ-XXX\>, \<Jira ticket URL\|PROJ-YYY\>, \<Jira
ticket URL\|PROJ-ZZZ\> --- \<@SlackUserID\> (PROJ = the Jira Hygiene
Tool Config File\'s "Jira Project Key")

\...

:large_yellow_circle: Status change not explained

\- \<Jira ticket URL\|PROJ-XXX\>, \<Jira ticket URL\|PROJ-YYY\> ---
\<@SlackUserID\>

\...

:large_purple_circle: Assignee change not explained

\- \<Jira ticket URL\|PROJ-XXX\>, \<Jira ticket URL\|PROJ-YYY\> ---
\<@SlackUserID\>

\...

Tickets checked: \<N\> \| Flagged: \<N\>

Missing daily update: \<N\>,

Status change not explained: \<N\>,

Assignee change not explained: \<N\>

Omit any of the three sections entirely if it has no flagged tickets. If
nothing was flagged at all across all three checks, still post a short
message confirming the test ran cleanly (e.g., \"No commenting guardrail
violations found for Aug 7 test window --- N tickets checked.\") ---
since this is a one-off test, we want confirmation it executed even on a
clean result, unlike the production routine which may skip messaging on
a clean run.

De-duplication

Not applicable --- this is a single, one-off test run, not a recurring
check.

Output

After posting, report back: the Slack message permalink, and a one-line
count summary (tickets checked, tickets flagged, and the breakdown by
Check A/B/C).
