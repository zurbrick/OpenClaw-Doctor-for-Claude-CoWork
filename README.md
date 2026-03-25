# OpenClaw Doctor for Claude CoWork

**The missing troubleshooting companion for OpenClaw.** Diagnose gateway failures, fix stuck agents, debug Telegram/Discord/iMessage/Slack channels, optimize multi-agent routing, and manage your entire OpenClaw deployment — all from Claude CoWork or Claude Code.

## Why This Exists

OpenClaw is powerful, but when something breaks at 11pm and your Telegram bot goes silent, you're left grepping through docs and guessing at CLI commands. OC-Doc puts an OpenClaw expert inside Claude. Paste your `openclaw doctor` output, describe the symptom, or just say "my bot is down" — Claude knows exactly what to check and how to fix it.

## What Claude Can Do With This Skill

**Diagnose in seconds** — Reads `openclaw doctor` output and immediately identifies the issue: stale session locks, hung Telegram handlers, gateway auth mismatches, unreachable groups, privacy mode traps, cron scheduler failures, and more.

**Run commands for you** — With Desktop Commander or terminal access, Claude executes `openclaw gateway restart`, `openclaw logs --follow`, `openclaw config set`, and any other CLI command directly on your machine. No copy-paste required.

**Step-by-step playbooks** for the most common emergencies: agent not responding on Telegram, gateway won't start after update, messages delivering but no response, cron jobs not firing, post-upgrade auth failures.

**Channel-specific deep dives** — Telegram privacy mode, BotFather setup, group configuration, webhook vs polling, DM pairing, forum topics. Discord OAuth scopes and Message Content Intent. iMessage automation permissions. Slack event subscriptions. WhatsApp Business API rate limits.

**Configuration optimization** — Multi-agent routing with most-specific-wins priority, model fallback chains for graceful degradation during rate limiting, channel health check tuning, session cleanup, security hardening with pairing policies and exec approvals.

**Full CLI reference** — Every `openclaw` command, subcommand, and flag. Gateway lifecycle, agent management, session inspection, channel probing, cron scheduling, memory search, browser control, backup/restore, and more.

## Install

Clone into your Claude skills directory:

```bash
git clone https://github.com/zurbrick/OpenClaw-Doctor-for-Claude-CoWork.git ~/.claude/skills/oc-doc
```

The skill is immediately available in your next Claude CoWork or Claude Code session. No configuration needed.

## Setup for Maximum Power

OC-Doc works at three levels depending on what access Claude has:

| Access Level | What Claude Can Do |
|---|---|
| **No file access** | Interpret doctor output you paste in, advise on commands to run, explain error messages |
| **File access** (`~/.openclaw` mounted) | Read your config, inspect session locks, check cron jobs, analyze logs directly |
| **Desktop Commander / terminal** | Run `openclaw` CLI commands on your Mac — restart gateways, tail logs, update config, probe channels |

For the full experience, select your home directory or `~/.openclaw` as your CoWork working directory, or connect Desktop Commander.

## Usage

Just describe your problem. The skill triggers automatically.

```
"My OpenClaw bot stopped responding in Telegram"
"Gateway won't start — says refusing to bind without auth"
"Help me set up separate agents for work and personal"
"What do these channel warnings mean?" [paste doctor output]
"My cron jobs aren't firing"
"Set up model fallbacks so my bot doesn't go down during rate limiting"
"How do I add a second Telegram group with different settings?"
"OpenClaw updated and now everything is broken"
```

## Skill Contents

```
oc-doc/
├── SKILL.md                    # Diagnostic workflows, playbooks, config optimization
├── README.md
└── references/
    ├── channels.md             # Telegram, Discord, iMessage, Slack, WhatsApp troubleshooting
    └── cli-reference.md        # Complete openclaw CLI command reference
```

## Covered Topics

OpenClaw gateway management, openclaw doctor interpretation, openclaw CLI reference, Telegram bot troubleshooting, Discord bot setup, iMessage automation, Slack integration, WhatsApp Business API, multi-agent routing, agent isolation, session lock debugging, cron job scheduling, heartbeat configuration, webhook setup, long polling, privacy mode, BotFather configuration, DM pairing policies, group allowlists, exec approvals, model fallback chains, rate limit handling, channel health checks, config validation, gateway service install, Tailscale integration, mDNS discovery, session cleanup, memory search, security audit, OpenClaw update troubleshooting, post-upgrade migration, config hot reload, streaming configuration, forum topic routing, sticker handling, inline buttons, and more.

## Compatibility

Works with OpenClaw 2026.3.x and later. Built for Claude CoWork and Claude Code. Requires no API keys, no external services, no additional dependencies.

## Contributing

Issues and PRs welcome at [github.com/zurbrick/OpenClaw-Doctor-for-Claude-CoWork](https://github.com/zurbrick/OpenClaw-Doctor-for-Claude-CoWork).

## License

MIT
