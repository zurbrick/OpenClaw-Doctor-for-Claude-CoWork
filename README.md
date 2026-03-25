# OC-Doc

**Claude Cowork skill for troubleshooting, diagnosing, and improving OpenClaw deployments.**

This skill runs in Claude Cowork (or Claude Code) — not inside OpenClaw itself. It gives Claude deep knowledge of OpenClaw's CLI, configuration, gateway management, channel setup (Telegram, Discord, iMessage, Slack, etc.), and troubleshooting patterns so it can help you fix issues from the outside.

## Setup

### Important: Point Cowork at your OpenClaw workspace folder

For OC-Doc to be most useful, **select your OpenClaw workspace folder** (`~/.openclaw`) as your Cowork working directory, or at minimum ensure Claude has access to it via Desktop Commander or similar file access MCP.

Without access to `~/.openclaw/`, Claude can still advise on commands to run and interpret doctor output you paste in — but it won't be able to directly inspect your config, read logs, or check session state.

If you use Desktop Commander, Claude can run `openclaw` CLI commands directly on your Mac (e.g., `openclaw doctor`, `openclaw gateway restart`, `openclaw logs --follow`).

### Install from ClawHub

```bash
clawhub install oc-doc
```

### Install from GitHub

Clone into your skills directory:

```bash
git clone https://github.com/zurbrick/oc-doc.git ~/.claude/skills/oc-doc
```

## What it does

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
- "What do these channel warnings mean?" (paste doctor output)

## License

MIT
