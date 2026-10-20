# Context management: what actually works

These facts were checked on 15 Oct 2026 against Claude Code 2.1.296 in the terminal and against
the 2.1.295 binary that Claude Desktop (app 2.37337.1) bundles. Each fact gives the test behind
it. Behaviour can change between versions, so where this file and a live `get_usage` disagree,
trust the live reading and add a row to `learnings.md`.

## Words

- **Context window**: how much conversation a session can hold, in tokens. `get_usage` reports it
  as `context.contextWindow` (for example 200000 or 1000000).
- **Compaction**: when the window fills, Claude Code replaces the older conversation with a
  summary. The summary is written by the model and can drop details, such as a session id or
  the exact words of a question. That is the loss this skill prevents.
- **Auto-compact point**: `get_usage` reports it as `context.autoCompactsAtPercent`. The default
  read 97 on a 1M window, which is 33k tokens short of full. On a 200k window, the same 33k
  buffer puts it at about 83%.

## What `get_usage` reports

`get_usage` (session `"self"` or another session's id) returns:

- `plan`: plan name, the 5-hour and weekly limits as percent used with reset times, and extra
  usage. These are the account's limits, shared by every session on it.
- `context`: `tokensUsed`, `contextWindow`, `percentUsed`, `autoCompactsAtPercent`, and the
  largest categories. It is read from the session's own process, so an idle or archived
  session reports it as unavailable.

## Mechanisms

| Mechanism | Works? | How it was checked | Who acts |
| --- | --- | --- | --- |
| Director reads its own context level | Yes | `get_usage` on a live desktop session returned `percentUsed` and `autoCompactsAtPercent` | Automatic |
| Director starts its own compaction | **No** | No tool runs `/compact`. `send_message` refuses the session's own id. Built-in commands aren't skills. `/compact` is only recognised at the start of a typed message. | The user types `/compact …` |
| `/compact <focus line>` | Yes | Docs; terminal test kept the remembered word named in the focus line | The user |
| `clear_session("self")` | Exists, but it wipes everything | Tool contract: clears after the turn, the app asks the user to approve, and nothing carries over except files | The user approves; not used by this skill |
| `CLAUDE_CODE_AUTO_COMPACT_WINDOW=<tokens>` | Yes | `/context` with 500000 showed `23.7k / 500k` instead of `/ 1m` (2.1.296 and Desktop's 2.1.295). With 100000, a one-process test compacted four times. | The user approves the settings change once |
| `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=50` | **No effect seen** | `/context` was unchanged with it set by env or by settings. It is documented as lower-only, and third-party reports say it stopped working in July 2026. | Don't use |
| `env` block in settings reaches Desktop sessions | Yes | A variable that exists only in `~/.claude/settings.json` `env` was present in a Desktop session's shell | n/a |
| Running session picks up a settings change | **No** | `.claude/settings.local.json` was written while a Desktop session ran: neither `get_usage` nor the shell env changed | The setting applies to sessions started after the change |
| `CLAUDE.local.md` re-read after compaction | Yes | In one process, a `CLAUDE.local.md` created after the session started was in context after `/compact`, and after automatic compaction | Automatic, once the Director has written it |
| SessionStart hook, matcher `compact` | Yes | It fired on manual and on automatic compaction, and its output was in context afterwards | Needs a settings change and a new session; not needed when the `CLAUDE.local.md` note is in place |
| PreCompact hook that blocks | Possible, risky | Docs: exit code 2 skips a proactive compaction, but at the hard limit the request fails instead | Not used |
| `DISABLE_AUTO_COMPACT=1` | Documented | Docs | Never: a full window then fails instead of compacting |

## The setting, worked out

The auto-compact point in tokens is the setting's value minus the buffer. Work the buffer out
from today's reading rather than assuming 33k:

```
buffer = contextWindow × (100 − autoCompactsAtPercent) / 100
value  = contextWindow / 2 + buffer, rounded up to a whole thousand, at least 100000
```

Two examples. Always use the live reading: a session that reads 95% on a 200k window gets
110000, not 134000.

| Model window | Default reading | Buffer | Value to set | Compacts near |
| --- | --- | --- | --- | --- |
| 1,000,000 | 97% | 30,000 | 530000 | 500k (50%) |
| 200,000 | about 83% | about 34,000 | 134000 | 100k (50%) |

Where it goes: the project's `.claude/settings.local.json`, under `env`. That file is personal
and per project: it is not shared with collaborators, and other projects are untouched. Git
usually ignores it, but check with `git check-ignore`; if it isn't ignored, add it to
`.git/info/exclude`, which is local and never pushed. Merge it into any existing file: never
overwrite the user's other settings.

```json
{
  "env": {
    "CLAUDE_CODE_AUTO_COMPACT_WINDOW": "530000"
  }
}
```

**How to confirm it** in the first session started after the change: work out the compaction
point in tokens as `contextWindow × autoCompactsAtPercent / 100`. It should be near half the
model's full window. Whether `get_usage` then reports the smaller window or the full one has not
been checked, which is why the check uses tokens rather than the percent. If the point has not
moved, say so and carry on with the manual protocol.

## Why the plan doesn't rest on the setting

The setting is a safety net for when the user is away. The protocol works without it:

1. The Director checks `get_usage` itself.
2. It writes the checkpoint before 40%, and asks the user to type `/compact` with a prepared
   focus line.
3. It leaves itself a note in `CLAUDE.local.md` that survives any compaction.

The setting changes only one thing: if nobody types `/compact`, the automatic compaction comes
at about 50% instead of near full. With a fresh checkpoint, nothing is lost either way.
