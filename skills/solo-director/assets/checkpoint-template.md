# Director checkpoint

<!--
Copy to <repo>/.director/checkpoint.md and fill every section. Rewrite the whole
file each time (don't append): it must always describe now. Delete these comments.
A section with nothing in it says "none", so a reader knows it wasn't forgotten.
After any compaction or clear, the Director reads this file before doing anything else.
-->

Written: <YYYY-MM-DD HH:MM> · Reason: <before compaction | end of a major task | user asked>
Context when written: <percentUsed>% of <contextWindow> tokens (auto-compact set to <autoCompactsAtPercent>%)

## Who and where

- Director session: <session id> · title "<title>" · pinned: <true/false, as get_session showed>
- Account: plan <plan from get_usage>, checked <date>
- Project: <repo folder> · base branch `<base>` (from <where you checked it>)
- Checks before every PR: `<the repo's own commands>`
- Repo rules files: <the ones that exist>

## Board

| # | Task (as the user named it) | Session id | Title | Branch | Owns files | State | Waiting on |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | <task> | <local_…> | <title> | <branch> | <paths> | <not started / working / waiting / blocked / PR #n open / merged / stopped> | <who or what, exactly> |

## In flight right now

<!-- One line per session that is not finished. What it is doing, what it last reported
(quote its DIRECTOR: line), and what the Director owes it, if anything. -->

- <session id> (<title>): last report "<DIRECTOR: … line, verbatim>" at <time>. Director owes: <nothing | an answer to … | a review of PR #n>.

## Open decisions (the user's, still unanswered)

<!-- Quote word for word. Keep the question as it was put to the user, the user's own
words so far if any, and which session is blocked. Never paraphrase: a paraphrase
changes the question. -->

1. Asked <date time>: "<the question, verbatim>". User so far: "<their words, verbatim, or none>". Blocks: <session / task>.

## Decisions made

<!-- The user's words, verbatim, with date and time, and what each one applies to. -->

- <YYYY-MM-DD HH:MM> "<user's words, verbatim>" → applies to <task / PR / everything>.

## Requests not yet acted on

<!-- Anything the user asked for that is not on the board yet. Not a to-do list of
your own ideas: only what the user asked. -->

- <YYYY-MM-DD HH:MM> "<user's words, verbatim>" → status: <queued, waiting for go / needs a decision>.

## Next steps

<!-- In order. The first one is what you do right after rereading this file. -->

1. <step>

## Not to do

<!-- Standing limits that a fresh reader might break: e.g. no merges without the
user's word, a PR the user said to hold, a file no worker may touch. -->

- Nothing merges without the user's word.
