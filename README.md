# OC-Doc

**Claude Cowork / Claude Code skill for troubleshooting, diagnosing, and improving OpenClaw deployments.**

This is a **Claude skill** — it runs in Claude Cowork or Claude Code, not inside OpenClaw itself. It gives Claude deep knowledge of OpenClaw's CLI, configuration, gateway management, channel setup (Telegram, Discord, iMessage, Slack, etc.), and troubleshooting patterns so it can help you fix issues from the outside.

## Install

Clone into your Claude skills directory:

```bash
git clone https://github.com/zurbrick/oc-doc.git ~/.claude/skills/oc-doc
```

That's it — the skill is available in your next Cowork or Claude Code session.

## Setup Tips

For OC-Doc to be most useful, **select your home directory or `~/.openclaw`** as your Cowork working directory, or ensure Claude has access via Desktop Commander or similar file-access MCP.

Without access to `~/.openclaw/`, Claude can still advise on commands to run and interpret `openclaw doctor` output you paste in — but it won't be able to directly inspect your config, read logs, or check session state.

With Desktop Commander, Claude can run `openclaw` commands directly on your Mac (e.g., `openclaw doctor`, `openclaw gateway restart`, `openclaw logs --follow`).

## What It Does

- **Diagnoses issues** — Interprets `openclaw doctor` output, session locks, channel warnings, and log patterns
- **Provides playbooks** — Step-by-step fixes for stuck agents, gateway failures, post-update issues, cron problems, and message delivery failures
- **Channel-specific troubleshooting** — Deep knowledge of Telegram (privacy mode, groups, webhooks), Discord, iMessage, Slack, and WhatsApp configuration
- **Configuration optimization** — Performance tuning, multi-agent setup, model fallbacks, security hardening
- **Full CLI reference** — Every `openclaw` command, subcommand, and flag documented

## Usage

Just describe your OpenClaw problem in Cowork. The skill triggers automatically on mentions of OpenClaw issues, gateway problems, stuck agents, channel failures, or configuration questions.

Examples:
- "My OpenClaw bot stopped responding in Telegram"
- "Gateway won't start after updating OpenClaw"
- "Help me set up a second agent for work"
- "What do these channel warnings mean?" *(paste doctor output)*

## Skill Contents

```
oc-doc/
├── SKILL.md              # Main skill — diagnostic workflows, playbooks, config optimization
└── references/
    ├── channels.md       # Telegram, Discord, iMessage, Slack, WhatsApp troubleshooting
    └── cli-reference.md  # Complete openclaw CLI command reference
```

## License

MIT
