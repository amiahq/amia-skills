---
name: claude-codex-cross-review
description: "Pair Claude Code and OpenAI Codex on the same repository without overwriting files or manually copy-pasting diffs. Covers shared AGENTS.md/CLAUDE.md rules, git worktree isolation, `codex review`, `claude -p --permission-mode dontAsk`, and user-promise review prompts."
---

# Claude Code + Codex Cross-Review

Use this workflow when running Claude Code and Codex together so one agent writes and the other audits the diff directly from git.

## 1. Share one rules file

Keep project rules in one file so both harnesses never drift:
- Put shared rules in `AGENTS.md` (read natively by Codex, and by Claude Code when no `CLAUDE.md` exists).
- If you also keep Claude-only instructions in `CLAUDE.md`, put `@AGENTS.md` on line 1 (or symlink `ln -s CLAUDE.md AGENTS.md`).

## 2. Isolate concurrent writers with git worktrees

Never let two writing agents edit the same working tree concurrently. Give a second writer its own worktree and branch:

```sh
git worktree add ../repo-codex -b codex/<task-slug>
cd ../repo-codex && codex
```

A read-only reviewer can safely share the writer's checkout.

## 3. Have Codex review Claude Code's diff

Run Codex's native diff reviewer directly (verified with Codex CLI 0.171.0):

```sh
codex review --uncommitted     # staged, unstaged, and untracked changes
codex review --base main       # current branch against main
codex review --commit <sha>    # one specific commit
```

**Important CLI constraint:** `codex review` cannot combine custom prompt text with `--uncommitted`. When you want a targeted review of a user-facing promise, pass the instruction alone:

```sh
codex review "Review the uncommitted changes. Check whether <user-facing invariant> still holds. Run the smallest command that proves it and quote the command and output."
```

To let Claude Code invoke `codex review` automatically before claiming done, add to `.claude/settings.json`:

```json
{
  "permissions": {
    "allow": ["Bash(codex review *)"]
  }
}
```

## 4. Have Claude Code review Codex's diff (read-only)

Run Claude Code non-interactively in `dontAsk` mode so any non-approved tool is denied instead of hanging on a prompt:

```sh
claude -p "Review the changes on this branch against main. List concrete bugs a caller or user would hit, with file and line. Change nothing." \
  --permission-mode dontAsk \
  --allowedTools "Read" "Grep" "Bash(git diff *)"
```

## 5. Review the user promise, not just compilation

Never stop at "does it compile?" Name the concrete user promise that a silent shortcut could break (e.g., backward OS compatibility, existing caller defaults, edge-case rounding, non-admin permissions) and demand an executed check or concrete line-level proof.

## 6. Resolve findings cleanly

- Fix every **P0 / P1** finding, or state the verified evidence for why it does not apply.
- Re-run verification after edits; never trust a pre-fix green.
