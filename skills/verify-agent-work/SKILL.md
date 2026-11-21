---
name: verify-agent-work
description: "Verify code changes, test suites, refactors and landing reports before claiming done. Enforces positive completion tokens, explicit UNMEASURED states, red-probe guard testing, and compiler reachability checks. Use before finishing any coding task, reviewing an agent's PR, or hardening CLAUDE.md/AGENTS.md."
---

# Verify Agent Work (6 Rules from 17,468 Commits)

Apply these six rules before reporting any task complete, reviewing an agent's output, or hardening a repository's rules file.

## 1. Require a positive completion token — never "zero errors"

- Never treat the absence of `error:` lines as a pass. A starved, killed, or filtered command prints zero errors too.
- Read and quote the mechanism's own positive completion token (`Finished`, `test result: ok. N passed; 0 failed`, `PASSED`, or the exact `git show HEAD:<path>` blob).
- Any count below its expected floor (e.g., 0 tests matched) is **`UNMEASURED`**, never passing or equal.

## 2. Never collapse "I could not tell" into `false` or empty

- A 2-state type (`bool`, `Option<T>`, `{}`) over a 3-state reality (*yes / no / could not ask*) hides failures silently.
- Check every error/timeout fallback (`.unwrap_or(false)`, `catch -> {}`, `|| true`): if nothing downstream can distinguish "command failed" from "clean / negative", replace it with an explicit 3-state result (`Ok(true) / Ok(false) / Unmeasured(err)`).
- Prefer a self-describing record on disk over a bare pointer or wall-clock poll.

## 3. Prove new guard tests go RED before trusting them

- A test whose expected outcome matches the fail-safe fallback passes vacuously.
- For any new guard, policy check, or bugfix regression test:
  1. Temporarily collapse the guard/fix (keeping a clean revert path).
  2. Run the scoped test and confirm a **`RED`** result (actual failure with a test-runner completion line — not a compile error or 0-tests-matched `UNMEASURED`).
  3. Restore the guard and confirm **`GREEN`** with the positive test count.
- Assert the invariant the fix must preserve, never a brittle symptom of the broken code.

## 4. Check compiler dead-code warnings after every refactor

- Moving a call site often leaves the old safety guard and its unit tests intact (`grep` still sees the tests as callers while production code runs unguarded).
- After any refactor, run a full `--all-targets` / typecheck / linter pass and inspect every newly introduced `never used` / `unused` warning before reporting done.

## 5. Keep claims as outputs — including error messages

- Verify what landed from `git show HEAD:<path>` or `git status`, never from an edit tool's "+N lines" summary.
- Audit error messages you write: say where to inspect, never assert a cause ("failed because X" / "details in foo.log") unless the code actually observed X or wrote `foo.log`.

## 6. Separate reversible work from irreversible authority

- Execute reversible verification (reads, builds, unit tests, diffs) automatically.
- Stop and ask the user before irreversible actions: force pushes, production deploys, deleting user data, charging money, or sending external messages.

## Output checklist before saying "Done"

Report only verified outputs:
- **Changed files:** exact paths verified on disk / git.
- **Verification commands & positive tokens:** exact command + observed pass count / exit status (or explicit `UNMEASURED` with reason).
- **Red-probe result (for new guards/bugfixes):** confirmed `RED` when collapsed → `GREEN` when restored.
- **Open risks or unverified paths:** stated plainly.
