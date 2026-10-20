# Solo Director

A Claude Code skill, packaged as a plugin, that turns one session in the Claude Desktop app into
the **Director** of your project. You tell it the tasks. It plans them as a board, starts a
separate session for each one once you say go, watches those sessions, passes your words to them
word for word, and reports back. It keeps the board in a file, so when its own memory fills up and
gets compacted, it picks up where it left off instead of losing track of running work.

It is for one person on one Claude subscription, working entirely in the Claude Desktop app's
**Code** tab. It is not built for setups that span several subscriptions or a second machine.

## Install

From a terminal, in one line (Claude Code 2.1.292 or later):

```bash
claude plugin install solo-director --marketplace nicolaithomson/solo-director
```

Or inside Claude Code, on any version, as two commands:

```text
/plugin marketplace add nicolaithomson/solo-director
/plugin install solo-director@solo-director
```

Then start a **new** session (skills load when a session starts). Open your project folder in the
Claude Desktop app's Code tab and say **"be my Director"**. You can also call it by name:
`/solo-director:solo-director`.

## What you need

- **The Claude Desktop app, Code tab.** The Director uses the app's own session tools
  (`start_session`, `SendMessage`, `list_sessions`, `get_session`, `list_events`, `get_usage`,
  `set_pinned`, `set_session_title`). The terminal CLI doesn't have them. If one is missing, the
  Director says which one and asks you to do that step in the app.
- **One Claude subscription.** Every session the Director starts bills the same account and
  shares its 5-hour and weekly limits. It tells you the plan and how much is used before it
  starts anything, and runs at most three sessions at once unless you say otherwise.
- **A git repository.** Each task session works on its own branch, in its own worktree.

## What it does

1. **Checks before it acts.** It reads your plan and usage, the base branch, your rules files
   (`CLAUDE.md`, `AGENTS.md`, `CONTRIBUTING.md`, …) and the repo's own check commands. It never
   invents a check your repo doesn't have.
2. **Plans a board.** One session per task you named, never extra ones. Each session owns its own
   files, so two sessions don't edit the same file. You get one table and a short numbered list of
   questions.
3. **Waits for your go.** Nothing starts, and no branch is created, until you say so.
4. **Launches and watches.** It pins itself (and only itself), starts each session with a written
   brief, and confirms each one is running. Sessions report back by themselves. It doesn't poll.
5. **Relays word for word.** A worker's question reaches you as the worker wrote it. Your answer
   reaches the worker as you wrote it. The Director's own reading is always labelled as its own.
6. **Never merges without your word**, given for that pull request. When you give it, the session
   that owns the PR updates from the base branch, re-runs the checks, and merges only if they pass.
7. **Keeps its memory safe.** It measures its own context use, writes a checkpoint at set points,
   and at 40% asks you to paste a prepared `/compact` line, because it can't run that command
   itself. After any compaction it rereads the checkpoint before doing anything else.

It doesn't write your project's code itself. The sessions do that.

## What it changes on your machine

All of these are local to your repo, and none of them is pushed:

- `<repo>/.director/` holds the checkpoint, the saved briefs and long messages. If git doesn't
  already ignore it, it's added to `.git/info/exclude`.
- `<repo>/CLAUDE.local.md` gets a short block between `<!-- solo-director:start -->` and
  `<!-- solo-director:end -->`, so the Director finds its checkpoint after a compaction.
- **Only if you say yes:** one setting in `<repo>/.claude/settings.local.json`
  (`CLAUDE_CODE_AUTO_COMPACT_WINDOW`), so automatic compaction comes at about half the window
  instead of near full. It shows you the exact JSON first, and merges it in without touching your
  other settings.
- A branch and worktree for each task session, and a pull request when your repo uses them.

When the board is done, it asks before it removes the `CLAUDE.local.md` block, archives finished
sessions or unpins itself. It never deletes a branch or worktree that has uncommitted work.

## Update and remove

```bash
claude plugin marketplace update solo-director
claude plugin update solo-director@solo-director
```

```bash
claude plugin uninstall solo-director@solo-director
```

## What's inside

```text
.claude-plugin/plugin.json        the plugin manifest
.claude-plugin/marketplace.json   makes this repo its own one-plugin marketplace
skills/solo-director/
  SKILL.md                        the skill
  assets/checkpoint-template.md   what a checkpoint must hold
  assets/worker-brief-template.md the brief each task session gets
  references/context-management.md  which compaction mechanisms were tested, and how
  references/learnings.md         where each rule came from
```

The compaction facts in `references/context-management.md` were tested on Claude Code 2.1.296
and the Claude Desktop app's bundled 2.1.295, in October 2026. Behaviour can change between
versions. Where that file and a live reading disagree, the Director trusts the live reading.

## Licence

MIT. See [LICENSE](LICENSE).
