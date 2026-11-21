---
name: safe-overnight-agent
description: "Configure coding agents for unattended and overnight runs without turning off all permissions blindly. Sets up allow/ask/deny rules, OS-enforced Bash sandboxing with credential denyRead, denyInsteadOfPrompting, and wake-on-input Notification hooks."
---

# Safe Overnight Agent Setup

Guide the user to replace all-or-nothing `--dangerously-skip-permissions` with a layered setup where routine reversible work runs uninterrupted and irreversible actions stop cleanly.

Always show the exact config diff and ask for confirmation before modifying `~/.claude/settings.json` or `.claude/settings.json`.

## 1. Apply the core boundary

- **Allow:** reversible local work inside the repo (reads, edits, builds, tests, local commits).
- **Ask:** irreversible or external actions (`git push`, deploys, destructive deletes, payments, sending messages).
- **Deny:** credential and secret reads (`.env`, `~/.ssh`, `~/.aws/credentials`), in every mode.

## 2. Configure allow / ask / deny rules

Deny overrides ask; ask overrides allow in every mode:

```json
{
  "permissions": {
    "allow": ["Bash(npm run *)", "Bash(cargo test *)", "Bash(git commit *)"],
    "ask": ["Bash(git push *)"],
    "deny": ["Read(./.env)", "Read(./.env.*)"],
    "defaultMode": "acceptEdits"
  }
}
```

Note the two limits of permission rules alone:
1. Bash patterns match the literal command string (e.g., `git -C . push` can slip past `Bash(git push *)`).
2. Rules are checked by the agent harness, not the OS kernel. Always pair with the OS sandbox below.

## 3. Enable the OS-enforced Bash sandbox

Enable `/sandbox` in Claude Code (macOS, Linux, WSL2) and explicitly deny credential reads — by default, sandboxed shell commands can still read files outside the repo:

```json
{
  "sandbox": {
    "enabled": true,
    "autoAllowBashIfSandboxed": true,
    "filesystem": {
      "denyRead": ["~/.ssh", "~/.aws/credentials", "~/**/.env"]
    },
    "network": {
      "allowedDomains": ["github.com", "*.npmjs.org", "crates.io"]
    }
  }
}
```

Keep both `Read(./.env)` (blocks Claude's built-in Read tool) and `filesystem.denyRead` (blocks shell commands).

## 4. Prevent silent overnight stalls

An unattended session waiting on one prompt sits idle until morning. Offer the user their preferred mode:

- **Option A — Never stall, deny prompts overnight:** Add `"denyInsteadOfPrompting": true` under `permissions` (Claude Code v2.1.319+). Any action that would prompt is cleanly denied so the run continues on remaining work.
- **Option B — Wake me when blocked:** Configure `preferredNotifChannel` or a `Notification` hook (fires when waiting on input or permission) to ping desktop/phone.

## 5. Pick the right mode by environment

| Environment | Recommended setup |
|---|---|
| Interactive at desk | `acceptEdits` + allow/ask/deny rules |
| Hands-off on personal Mac/laptop | `auto` mode + OS sandbox + `denyRead` + `ask` for pushes/deploys |
| Unattended overnight | Same + `denyInsteadOfPrompting: true` or push notification hook |
| Throwaway container/VM with zero secrets | `--dangerously-skip-permissions` only here |
