# OpenClaw Doctor (oc-doc)

A Claude skill for troubleshooting, diagnosing, and optimizing [OpenClaw](https://github.com/nichochar/open-claw) deployments. Manage your entire OpenClaw setup through Claude CoWork or Claude Code — from quick health checks to deep channel debugging.

## Installation

```bash
# Manual
git clone https://github.com/zurbrick/OpenClaw-Doctor-for-Claude-CoWork.git ~/.claude/skills/oc-doc
```

## What It Does

OC-Doc equips Claude with deep knowledge of OpenClaw's CLI, configuration patterns, and troubleshooting workflows so it can diagnose and fix your deployment from the outside. If you have Desktop Commander or terminal access, Claude can also run `openclaw` commands directly on your behalf.

The skill follows a 5-step diagnostic sequence that mirrors how an experienced operator would troubleshoot:

1. **Run `openclaw doctor`** — catches most common issues in one command
2. **Check gateway health** — validates service status and RPC connectivity
3. **Inspect logs** — identifies rate limiting, network failures, or message drops
4. **Examine sessions** — detects stuck conversations or stale lock files
5. **Channel-specific diagnosis** — addresses platform-specific problems

## Trigger Phrases

This skill activates when you say things like:

- "openclaw doctor", "gateway not responding", "bot not replying"
- "agent stuck", "messages not delivering", "Telegram bot broken"
- "openclaw won't start", "session stuck", "cron not running"
- "heartbeat failed", "channel warnings", "openclaw update"
- "my bot is down", "the agent isn't working"

## Structure

```
oc-doc/
├── SKILL.md                    # Core diagnostic workflows and optimization guidance
├── README.md
├── VERSION
├── CHANGELOG.md
├── evals/
│   └── evals.json              # Evaluation scenarios for skill testing
└── references/
    ├── channels.md             # Platform-specific troubleshooting (Telegram, Discord, etc.)
    └── cli-reference.md        # Complete OpenClaw CLI documentation (80+ commands)
```

## Coverage

| Area | Depth |
|------|-------|
| Gateway management | Full — lifecycle, health, RPC, service install |
| Telegram | Deep — privacy mode, DM policies, webhooks, forums, exec approvals |
| Discord | Moderate — OAuth scopes, intents, role routing |
| Slack / iMessage / WhatsApp | Basic — setup and common issues |
| Session debugging | Full — lock detection, cleanup, agent isolation |
| Cron & automation | Full — job config, webhook setup, hooks |
| Multi-agent routing | Full — agent creation, bindings, identity |
| Security | Full — audit, secret refs, config hardening |

## Dependencies

- **OpenClaw 2026.3.x or later** installed and configured
- **Desktop Commander MCP** or terminal access (optional, for running commands directly)
- No API keys or external dependencies required

## Compatibility

- Claude CoWork
- Claude Code

## Contributing

Found a bug or want to improve this skill? Open an issue or submit a PR. See the issue templates for guidance.

## License

MIT

## Changelog

See [CHANGELOG.md](./CHANGELOG.md) for version history.
