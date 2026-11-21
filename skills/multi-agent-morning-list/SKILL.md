---
name: multi-agent-morning-list
description: "Compress parallel or overnight multi-agent work into a crisp 3-bucket morning brief: Needs you, Still working, and Done (with verified receipts). Use at the end of a long run, swarm, or overnight shift."
---

# Multi-Agent Morning List

When multiple agents or long overnight tasks finish, never dump raw logs or walls of prose. Output a scannable **3-bucket morning brief** so the human can clear blockers in under 2 minutes.

## Output Structure (Strict Order)

### 1. Needs you *(decisions & approvals first — max 1 line per item + clear options)*
List every blocked session or irreversible decision waiting for a human yes/no:
- **[Agent / Task]** — exact question or irreversible action requested
- **Default / Recommendation:** what happens if approved vs. skipped
- **Cost / Risk:** blast radius in 1 line

*(If nothing is blocked, write: `None.`)*

### 2. Still working *(in-flight runs only)*
- **[Agent / Task]** — current step, last heartbeat / observed progress artifact, and whether anything looks stalled.

### 3. Done *(with machine-verified receipts only)*
Never mark a task done without citing the exact verification output:
- **[Agent / Task]** — 1-line outcome · verified commit/file (`git show HEAD:...` or output path) · positive test/build token (or explicitly labeled `UNMEASURED`).

### 4. Spend & Hygiene *(3 lines max)*
- Commits landed / files changed / branches or worktrees left open.
- Any failed or skipped checks that need follow-up.

## Hard Rules
- Put **Needs you** at the very top — never bury a blocker under completed work.
- Every item under **Done** must link to a real commit SHA, file path, or test output; unverified claims go under **Needs you** or are tagged `UNMEASURED`.
- Keep the entire morning brief under 40 lines unless there are more than 10 active tasks.
