# Learnings

Where each rule came from, newest first. When a run teaches something new, add a row: what
happened, then the rule it produced.

## Provenance

This skill is the single-account, desktop-only sibling of a larger Director setup that ran
across several subscriptions and machines. It shares that setup's lessons where they apply,
and drops what only applies to setups with several subscriptions:

- **Kept:**
  - check facts rather than remembering them;
  - never run two Directors;
  - show the board and wait for a go;
  - pass the user's words on verbatim, apart from your own reading;
  - workers own separate files;
  - nobody merges without the user's word;
  - add no tasks the user didn't name;
  - remove template lines that don't apply;
  - pin only the Director.
- **Dropped:**
  - per-repo billing tools;
  - terminal multiplexers and pane etiquette;
  - load monitors and a second runner machine;
  - team launchers;
  - second-opinion reviewers from other agent CLIs.
- **New:** the context protocol. A desktop Director has no wrapper to restart it, so it must
  survive compaction by itself.

## Rows

| When | What happened | Rule it produced |
| --- | --- | --- |
| 15 Oct 2026 (test, round 3) | Draft briefs written before the user's go kept `{{…}}` slots for answers still pending. | A draft says `waiting on question N` in that spot. Before sending, it holds what the answer decides, in the slot's own form. Only `{{DECISIONS}}` quotes the user verbatim, with date and time. |
| 15 Oct 2026 (PR re-review) | A checkpoint naming a Director that was closed or deleted matched neither resume nor takeover, so a new session could start fresh over it. | Any checkpoint means resume, takeover or stop, never a fresh start. A fresh start archives the old checkpoint first. |
| 15 Oct 2026 (PR review) | Review found four gaps, and fixing them turned up a fifth. A checkpoint jumped straight to resume, so a takeover skipped the live-Director check, the pin and the note rebind. Any pinned session counted as a Director. The checkpoint path was relative to the folder, not the repo root. Workers weren't told to re-check before merging. | A checkpoint naming another session is a handover: check for a live Director, pin yourself, rebind the note. Only a title or a checkpoint makes a session a Director. Paths start at the git root. The brief carries the merge re-check. Reports from a previous Director's sessions don't reach the new one. |
| 15 Oct 2026 (test, round 2) | After a resume, the Director asked whether to merge a PR that a standing "don't merge till I've looked" ruling still covered. | A standing ruling holds until the user lifts it. Don't offer to break it. |
| 15 Oct 2026 (test, round 1) | A fresh-start board said only "I'll ask you to paste one line". In another run, with the user about to leave, the `/compact` request came fourth in the message, after the status table. | The board spells out automatic, needs-you-sometimes and needs-you-once. A `/compact` request goes first in the message. |
| 15 Oct 2026 (test) | `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=50` left `/context` unchanged. `CLAUDE_CODE_AUTO_COMPACT_WINDOW` moved the window, in the terminal and in Desktop's bundled binary. | Use the window setting, worked out from today's `get_usage`. Never the percent override. |
| 15 Oct 2026 (test) | A settings file written while a Desktop session ran changed neither its `get_usage` reading nor its environment. | Settings changes apply to sessions started afterwards. Say so, and don't wait on them for the current run. |
| 15 Oct 2026 (test) | A `CLAUDE.local.md` created mid-session was in context after both manual and automatic compaction. | The Director leaves itself a reread note there, so any compaction leads back to the checkpoint. |
| 15 Oct 2026 (test) | No tool can run `/compact`, and `send_message` refuses the session's own id. | The Director prepares the `/compact` line, and the user types it. It never claims to have compacted itself. |
| 15 Oct 2026 | The app refuses `start_session` on a turn started by another session's report. | Starts happen on the user's turns. Between them, propose the next start. |
| 15 Oct 2026 | The author: only the Director is pinned, never its workers. | Pin self with `set_pinned`, confirm with `get_session`. Never pin a started session. |
| 14 Oct 2026 (multi-account Director) | A launcher stated which account paid from a four-day-old note; the app was signed in to another. | `get_usage` before stating who pays. |
| 14 Oct 2026 (multi-account Director) | A template carried another project's base branch, checks and domain rules. | Every fact in a brief is checked for this repo, today. |
| 14 Oct 2026 (multi-account Director) | A second Director nearly started while one with six open decisions was idle. | Look for a running Director first. Never two. |
| 14 Oct 2026 (multi-account Director, tests) | Runs invented tasks, and kept lines that didn't apply. | Only the user's tasks. Tag conditional lines and resolve them. |
| 12 Oct 2026 (multi-account Director) | A type error passed the unit tests and failed only at build. | "Done" means the repo's own checks passed, all of them. |
| 11 Oct 2026 (multi-account Director) | The author asked for work broken up into sessions "that I can track". | One task per session, each visible by name. |
| 11 Oct 2026 (multi-account Director) | Parallel sessions all edited the same few shared files. | Each file has one owner; shared changes land first. |
| 11 Oct 2026 (multi-account Director) | Predicted text was reported as the user's words. | Only the user's actual messages are their words. |
