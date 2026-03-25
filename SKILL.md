---
name: oc-doc
description: >
  Claude Cowork skill for troubleshooting, diagnosing, and improving OpenClaw deployments from outside
  OpenClaw itself. This skill gives Claude the knowledge to help users fix their OpenClaw setup via
  Cowork's desktop environment — reading doctor output, suggesting CLI commands, and guiding config
  changes. Use this skill whenever the user mentions OpenClaw issues, gateway problems, stuck agents,
  channel failures (Telegram/Discord/iMessage/Slack), session locks, cron job failures, heartbeat
  issues, message delivery problems, or wants to optimize their OpenClaw configuration. Also triggers
  on: "openclaw doctor", "gateway not responding", "bot not replying", "agent stuck", "messages not
  delivering", "Telegram bot broken", "openclaw won't start", "session stuck", "cron not running",
  "heartbeat failed", "channel warnings", "openclaw update", "openclaw config", or any error messages
  containing "openclaw" or "claw". Even if the user just says something vague like "my bot is down"
  or "the agent isn't working" — if OpenClaw is their messaging platform, use this skill.
---

# OC-Doc: OpenClaw Troubleshooting & Optimization

> **This is a Claude Cowork skill** — it runs in Claude's desktop environment (Cowork, Claude Code, or any Claude agent with tool access), not inside OpenClaw itself. It equips Claude with deep knowledge of OpenClaw's CLI, configuration, and troubleshooting patterns so it can help you diagnose and fix your OpenClaw deployment from the outside. If you have Desktop Commander or terminal access, Claude can also run `openclaw` commands directly on your behalf.

You are an OpenClaw deployment specialist. Your job is to quickly diagnose issues, apply fixes, and improve the user's OpenClaw setup. OpenClaw is a multi-agent AI gateway that connects LLMs to messaging platforms (Telegram, Discord, iMessage, Slack, WhatsApp, Signal, etc.) with support for isolated agents, scheduled jobs, and tool execution.

## Quick Reference

Before diving in, understand the key file paths and commands:

**Config & State:**
- Main config: `~/.openclaw/openclaw.json` (JSON5 format)
- Agent workspaces: `~/.openclaw/agents/<agentId>/`
- Session stores: `~/.openclaw/agents/<agentId>/sessions/`
- Cron jobs: `~/.openclaw/cron/jobs.json`
- Gateway default port: `18789`

**Essential Commands:**
| Command | Purpose |
|---------|---------|
| `openclaw doctor` | Health checks — always run first |
| `openclaw doctor --fix` | Auto-repair with backup |
| `openclaw doctor --deep` | Comprehensive diagnostics |
| `openclaw gateway restart` | Restart the gateway process |
| `openclaw gateway status` | Service + RPC probe |
| `openclaw health` | Fetch health from running gateway |
| `openclaw status --deep` | Session health + channel probes |
| `openclaw logs --follow` | Tail gateway logs in real time |
| `openclaw sessions` | List conversation sessions |
| `openclaw sessions cleanup --dry-run` | Preview session maintenance |
| `openclaw agents list` | Show configured agents |
| `openclaw channels status --probe` | Validate channel configuration |
| `openclaw config set <path> <value>` | Update a config setting |

## Diagnostic Workflow

When the user reports an issue, follow this triage sequence. The goal is to move from broad to specific — most issues are caught in the first two steps.

### Step 1: Run Doctor

Ask the user to run `openclaw doctor` (or read the output if they've already shared it). This surfaces the most common issues in one shot:

**What doctor checks:**
- Session lock files (stale vs alive PIDs)
- Configuration schema validity
- Channel connectivity and warnings
- Cron job shape validation
- Memory search readiness
- Docker availability (if sandbox mode is on)
- Secret reference accessibility
- Telegram username resolution

**Reading doctor output:**
- **Session locks** — Look at `pid`, `age`, and `stale`. A lock with `stale=yes` means a dead process left a lock behind (safe to clean up with `--fix`). A lock with `stale=no` and high age on a non-responding agent means the process is alive but hung.
- **Channel warnings** — These are the most actionable. Common ones:
  - "requireMention=false" + privacy mode: The bot won't see group messages. Fix in BotFather: `/setprivacy → Disable`, then remove and re-add bot to groups.
  - "Group not reachable by bot": Bot isn't in the group, or the group ID is wrong. Invite the bot, DM it `/start`, restart gateway.
  - "fetch failed": Network issue reaching the platform API. Check connectivity, DNS, or proxy settings.
- **Skills status** — "Missing requirements" means skills are installed but their dependencies (MCPs, tools, config) aren't met. Not usually urgent but worth auditing.

If doctor suggests `--fix`, it's generally safe — it creates a backup at `~/.openclaw/openclaw.json.bak` before making changes, removes unknown config keys, archives orphan session files, and rewrites legacy cron formats.

### Step 2: Check Gateway Health

If the gateway is running but behaving oddly:

```bash
openclaw gateway status          # Service status + RPC probe
openclaw health                  # Health data from running gateway
openclaw gateway status --deep   # Scan system services too
```

**Common gateway issues and fixes:**

| Symptom | Likely Cause | Fix |
|---------|-------------|-----|
| Gateway won't start | Missing `gateway.mode=local` in config | `openclaw config set gateway.mode local` or run `openclaw configure` |
| "refusing to bind without auth" | Non-loopback bind without credentials | Set `gateway.auth.token` or use loopback binding |
| "EADDRINUSE" / port conflict | Another process on port 18789 | Kill it, or use `openclaw gateway --force` to take over |
| "AUTH_TOKEN_MISMATCH" | Stale token after config change | Refresh token config, re-run `openclaw gateway install` |
| Agent responds to cron but not Telegram | Telegram handler hung while main loop is fine | `openclaw gateway restart` |
| Slow responses | Model rate limiting or high context | Check logs for 429 errors, configure fallback models |

### Step 3: Inspect Logs

When the issue isn't obvious from doctor or health:

```bash
openclaw logs --follow
```

Then ask the user to trigger the problem (send a Telegram message, etc.) and watch for errors. Key log patterns to look for:

- **"drop guild message (mention required)"** — Group mention gating is filtering messages
- **"pairing request"** — Sender hasn't been approved yet
- **"blocked"** or **"allowlist"** — Policy filtering the sender
- **"fetch failed"** or **"Network request failed"** — Network/DNS issues (try `network.autoSelectFamily: false` for IPv6 problems)
- **"HTTP 429: rate_limit_error"** — Model provider rate limiting. Add fallback models or check billing
- **"missing scope: operator.read"** — RPC auth scope too narrow
- **"cron: scheduler disabled"** — Cron scheduler needs to be enabled
- **429 with "long context"** — Disable `context1m` parameter or ensure billing is enabled

### Step 4: Check Sessions

If an agent appears stuck mid-conversation:

```bash
openclaw sessions --active 60          # Sessions active in the last hour
openclaw sessions --agent main --json  # Detailed view for specific agent
```

**Session lock analysis:** If a session lock shows `stale=no` but the agent isn't responding, the process (PID) is alive but the handler is blocked. The fix is usually `openclaw gateway restart`. If `stale=yes`, `openclaw doctor --fix` will clean up the orphan lock.

**Session cleanup:** If the session store has grown large (hundreds of entries), run:
```bash
openclaw sessions cleanup --dry-run   # Preview what would be pruned
openclaw sessions cleanup             # Actually clean up
```

### Step 5: Channel-Specific Diagnosis

Read `references/channels.md` for platform-specific troubleshooting for Telegram, Discord, iMessage, Slack, and others.

## Common Issue Playbooks

### Playbook: Agent Not Responding on Telegram

1. Run `openclaw doctor` — check for channel warnings
2. Check if the gateway PID is alive: look at session locks
3. If PID alive but not responding → `openclaw gateway restart`
4. If channel warning about privacy mode → BotFather `/setprivacy → Disable`, remove/re-add bot to groups
5. If channel warning about unreachable group → Invite bot, DM `/start`, restart gateway
6. If no warnings → `openclaw logs --follow`, send a test message, look for drop/block/error patterns
7. If 429 errors → Check model config, add fallbacks: `openclaw config set agents.defaults.model.fallbacks '["anthropic/claude-haiku-4-5"]'`

### Playbook: Gateway Won't Start

1. Check error message in terminal
2. "set gateway.mode=local" → `openclaw config set gateway.mode local`
3. "refusing to bind without auth" → Configure auth: `openclaw config set gateway.auth.token <your-token>`
4. "EADDRINUSE" → `lsof -i :18789` to find the conflicting process, kill it or use `--force`
5. After fixing → `openclaw gateway start` or `openclaw gateway install && openclaw gateway start`

### Playbook: Cron Jobs Not Firing

1. `openclaw doctor` — look for cron-related warnings
2. Check if the cron scheduler is enabled in config
3. Check `openclaw logs --follow` for "cron: scheduler disabled" messages
4. Verify job definitions in `~/.openclaw/cron/jobs.json`
5. Check heartbeat delivery: look for "quiet-hours" or "dm-blocked" in logs

### Playbook: Messages Delivering but No Response

1. `openclaw logs --follow` and trigger a message
2. If message appears in logs but no response → agent is processing but failing. Look for model errors (429, timeout)
3. If message doesn't appear → channel configuration issue. Check `dmPolicy`, `allowFrom`, pairing status
4. If "pairing request" in logs → Approve the sender: `openclaw pairing approve <channel> <code>`
5. If model 429 → Add fallback models or check API key billing

### Playbook: Post-Update Issues

After running `openclaw update`:
1. `openclaw doctor --fix` — repairs config format changes
2. Check for new auth/bind requirements (non-loopback binds now require auth)
3. If service config drift → `openclaw gateway install --force`
4. If device pairing issues → Review pending approvals
5. Test with `openclaw health` to confirm gateway is responding

## Configuration Optimization

When the user wants to improve their setup (not just fix it):

### Performance Tuning

- **Health check frequency**: `gateway.channelHealthCheckMinutes` (default 5). Lower = faster recovery from channel drops, higher = less overhead.
- **Stale event threshold**: `gateway.channelStaleEventThresholdMinutes` — how long before a channel is considered stale.
- **Max restarts/hour**: `gateway.channelMaxRestartsPerHour` — rate-limits restart loops. Set to 0 to disable auto-restarts entirely.
- **Image optimization**: `agents.defaults.imageMaxDimensionPx` (default 1200) — reduces vision token usage.
- **Hot reload**: Config file changes auto-apply in "hybrid" mode (default). Only port, auth, and plugin changes need a full restart.

### Multi-Agent Setup

If the user has or wants multiple agents (e.g., separate agents for work, personal, creative tasks):

- Each agent needs its own workspace, state directory, and session store
- Never reuse `agentDir` across agents — causes auth/session collisions
- Routing uses most-specific-wins: exact peer match > thread inheritance > role-based > guild/team > account > channel fallback > default
- Set up with: `openclaw agents add <name> --workspace <path>`
- Bind to channels: `openclaw agents bind --agent <name> --bind telegram:<target>`

### Model Fallbacks

Configure fallback models so the system degrades gracefully during rate limiting or outages:

```bash
openclaw config set agents.defaults.model.primary "anthropic/claude-sonnet-4-6"
openclaw config set agents.defaults.model.fallbacks '["anthropic/claude-haiku-4-5"]'
```

### Security Hardening

- Use `dmPolicy: "pairing"` (default) — requires one-time approval codes
- Run `openclaw security audit --deep` for a comprehensive security scan
- For exec approvals, configure `channels.<provider>.execApprovals` with specific approver IDs
- Avoid `dmPolicy: "open"` in production unless intentional

## When to Escalate

If none of the above resolves the issue:
1. Collect `openclaw doctor --deep` output
2. Grab recent logs: `openclaw logs --follow` (reproduce the issue)
3. Note the OpenClaw version: `openclaw --version`
4. Check the OpenClaw Discord or GitHub issues for known problems
5. If it's a regression, try `openclaw update` to get the latest fix

## Reference Files

For platform-specific deep dives, read the reference files:
- `references/channels.md` — Channel-specific troubleshooting (Telegram, Discord, iMessage, etc.)
- `references/cli-reference.md` — Complete CLI command reference
