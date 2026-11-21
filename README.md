# amia-skills

**Battle-tested agent-ops skills distilled from 17,468 AI coding agent commits in 30 days.**

Every skill in this pack works **standalone** in **Claude Code, OpenAI Codex, Cursor, Antigravity, and OpenClaw** — no account, no API key, and no Amia required.

## Install

```bash
# Any Agent Skills-compatible host (Claude Code, Codex, Cursor, Gemini CLI, OpenClaw)
npx skills add amiahq/amia-skills
```

Or copy a skill folder directly into `.claude/skills/` or `.agents/skills/`:

```bash
git clone https://github.com/amiahq/amia-skills.git
cp -r amia-skills/skills/* ~/.claude/skills/
```

## Included Skills

| Skill | What it solves | Proven source |
|---|---|---|
| [`verify-agent-work`](skills/verify-agent-work/SKILL.md) | Stops false-green agent reports using 6 hard rules: positive completion tokens, explicit 3-state `UNMEASURED`, red-probe mutation testing, compiler dead-code checks after refactors, and claims-as-outputs. | [Proof, not claims (17,468 commits)](https://amia.to/articles/verify-ai-coding-agents-rules/) |
| [`claude-codex-cross-review`](skills/claude-codex-cross-review/SKILL.md) | One writes, the other reviews — shared `AGENTS.md`/`CLAUDE.md`, per-writer git worktrees, `codex review` + `claude -p --permission-mode dontAsk`, and user-promise review prompts. | [Claude Code + Codex together](https://amia.to/articles/use-claude-code-and-codex-together/) |
| [`safe-overnight-agent`](skills/safe-overnight-agent/SKILL.md) | Replaces blind `--dangerously-skip-permissions` with allow/ask/deny rules, Bash sandbox + `denyRead`, `denyInsteadOfPrompting`, and wake-on-input `Notification` hooks. | [Permissions & sandbox guide](https://amia.to/articles/dangerously-skip-permissions-alternative/) |
| [`multi-agent-morning-list`](skills/multi-agent-morning-list/SKILL.md) | Turns noisy overnight agent logs into a crisp 3-bucket morning brief: **Needs you**, **Still working**, and **Done (with proof)**. | [Managing multiple sessions](https://amia.to/articles/manage-multiple-claude-code-sessions/) |

## Running these overnight across multiple agents?

All four skills work in your terminal today with zero extra tools. If you want Claude Code, Codex, Cursor, Grok Build, Antigravity and Muse Code in **one list** on Mac — continuing on a Cloud computer when your laptop closes and pinging your iPhone Lock Screen before anything irreversible — see [Amia](https://amia.to) (`https://amia.to/SKILL.md`).

## License

MIT — see [LICENSE](LICENSE).
