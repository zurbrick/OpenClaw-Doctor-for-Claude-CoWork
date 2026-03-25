# OpenClaw CLI Complete Reference

## Table of Contents
1. Setup & Onboarding
2. Configuration
3. System Health & Diagnostics
4. Gateway Management
5. Agent & Messaging
6. Channel Management
7. Session Management
8. Automation (Cron, Webhooks, Hooks)
9. Models & Auth
10. Memory & Skills
11. Browser Control
12. Utilities
13. Global Flags

---

## 1. Setup & Onboarding

| Command | Purpose |
|---------|---------|
| `openclaw setup` | Initialize configuration and workspace |
| `openclaw onboard` | Interactive gateway, workspace, and skills onboarding |
| `openclaw configure` | Configuration wizard for models, channels, skills, gateway |

## 2. Configuration

| Command | Purpose |
|---------|---------|
| `openclaw config` | Non-interactive config helpers |
| `openclaw config get <path>` | Read a config value |
| `openclaw config set <path> <value>` | Set a config value |
| `openclaw config unset <path>` | Remove a config key |
| `openclaw config file` | Show config file path |
| `openclaw config validate` | Validate config schema |
| `openclaw security` | Audit config + local state for security issues |
| `openclaw security audit --deep` | Comprehensive security audit |

**Config set options:**
- `--ref-provider`, `--ref-source`, `--ref-id` — SecretRef builder
- `--batch-json`, `--batch-file` — Batch updates
- `--dry-run` — Validate without applying
- `--allow-exec` — Allow exec in dry-run

**RPC config methods:**
- `config.apply` — Validate, write, restart, wake
- `config.patch` — Merge partial update with restart

## 3. System Health & Diagnostics

| Command | Purpose |
|---------|---------|
| `openclaw doctor` | Health checks + quick fixes |
| `openclaw doctor --fix` / `--repair` | Auto-repair with backup |
| `openclaw doctor --deep` | Comprehensive diagnostics |
| `openclaw health` | Fetch health from running gateway |
| `openclaw status` | Show linked session health |
| `openclaw status --deep` | Add gateway health probes |
| `openclaw logs --follow` | Tail gateway logs in real time |

**Doctor checks:** Session locks, config schema, channel connectivity, cron jobs, memory search, Docker, SecretRefs, Telegram username resolution, macOS launchctl.

**Doctor --fix actions:** Creates backup at `~/.openclaw/openclaw.json.bak`, removes unknown keys, archives orphans as `.deleted.<timestamp>`, rewrites legacy cron formats.

## 4. Gateway Management

**Service lifecycle:**

| Command | Purpose |
|---------|---------|
| `openclaw gateway` / `openclaw gateway run` | Start gateway |
| `openclaw gateway start` | Start installed service |
| `openclaw gateway stop` | Stop installed service |
| `openclaw gateway restart` | Restart installed service |
| `openclaw gateway install` | Install as system service |
| `openclaw gateway uninstall` | Remove system service |

**Query commands:**

| Command | Purpose |
|---------|---------|
| `openclaw gateway health` | Health check probe |
| `openclaw gateway status` | Service status + RPC probe |
| `openclaw gateway status --deep` | Scan system services |
| `openclaw gateway probe` | Debug probe (remote + localhost) |
| `openclaw gateway call <method>` | Low-level RPC helper |
| `openclaw gateway discover` | mDNS discovery scan |

**Run options:**
- `--port <port>` — WebSocket port (default 18789)
- `--bind <mode>` — loopback|lan|tailnet|auto|custom
- `--auth <mode>` — token|password
- `--token <token>` / `--password-file <path>` — Credentials
- `--tailscale <mode>` — off|serve|funnel
- `--force` — Kill existing listener first
- `--verbose` — Verbose logging
- `--dev` — Dev config and workspace
- `--allow-unconfigured` — Skip config requirement

**Signals:** SIGUSR1 = in-process restart; SIGINT/SIGTERM = stop

## 5. Agent & Messaging

| Command | Purpose |
|---------|---------|
| `openclaw agent --message <text>` | Run single agent turn |
| `openclaw agents list` | List configured agents |
| `openclaw agents add <name>` | Add isolated agent |
| `openclaw agents delete <id>` | Delete agent + prune workspace |
| `openclaw agents bind --agent <name> --bind <target>` | Add routing binding |
| `openclaw agents unbind --agent <name> --bind <target>` | Remove routing binding |
| `openclaw agents unbind --agent <name> --all` | Remove all bindings |
| `openclaw agents bindings` | List all bindings |
| `openclaw agents set-identity --agent <name>` | Set name/emoji/avatar |
| `openclaw message send` | Unified outbound messaging |

**Agent turn options:**
- `--session-id <id>` — Target session
- `--timeout <seconds>` — Turn timeout
- `--local` — Embedded mode (no gateway)

## 6. Channel Management

| Command | Purpose |
|---------|---------|
| `openclaw channels` | Manage chat accounts |
| `openclaw channels status --probe` | Validate channel configuration |
| `openclaw channels logs --channel <name>` | Channel-specific logs |
| `openclaw pairing approve <channel> <code>` | Approve DM pairing |
| `openclaw devices` | Manage gateway device pairing |

**Supported platforms:** WhatsApp, Telegram, Discord, Slack, Teams, Signal, iMessage, Mattermost, Google Chat

## 7. Session Management

| Command | Purpose |
|---------|---------|
| `openclaw sessions` | List stored sessions |
| `openclaw sessions --agent <id>` | Sessions for specific agent |
| `openclaw sessions --all-agents` | Aggregate all agents |
| `openclaw sessions --active <minutes>` | Filter by activity window |
| `openclaw sessions --json` | JSON output |
| `openclaw sessions cleanup --dry-run` | Preview maintenance |
| `openclaw sessions cleanup` | Run maintenance |
| `openclaw sessions cleanup --enforce` | Force maintenance |

**Session keys:** Format is `agent:<agentId>:<mainKey>`. DMs collapse to "main" session key per agent.

## 8. Automation

**Cron:**

| Command | Purpose |
|---------|---------|
| `openclaw cron` | Manage scheduled jobs |

Jobs configured in `~/.openclaw/cron/jobs.json`.

**Webhooks:**

| Command | Purpose |
|---------|---------|
| `openclaw webhooks` | Configure webhooks (Gmail Pub/Sub support) |

**Hooks:**

| Command | Purpose |
|---------|---------|
| `openclaw hooks` | Enable/disable plugin hooks |

## 9. Models & Auth

| Command | Purpose |
|---------|---------|
| `openclaw models` | List, set, manage model providers |

Supports fallbacks and auth profiles. Configure primary model and fallbacks:
```bash
openclaw config set agents.defaults.model.primary "anthropic/claude-sonnet-4-6"
openclaw config set agents.defaults.model.fallbacks '["anthropic/claude-haiku-4-5"]'
```

## 10. Memory & Skills

| Command | Purpose |
|---------|---------|
| `openclaw memory` | Vector search over memory files |
| `openclaw skills` | List, install, check skill readiness |

## 11. Browser Control

| Command | Purpose |
|---------|---------|
| `openclaw browser` | Control Chrome/Brave/Edge/Chromium instances |

## 12. Utilities

| Command | Purpose |
|---------|---------|
| `openclaw update` | Update OpenClaw |
| `openclaw backup` | Create and verify backups |
| `openclaw reset` | Reset local config/state |
| `openclaw uninstall` | Remove gateway service and local data |
| `openclaw dashboard` | Open web dashboard |
| `openclaw tui` | Terminal UI connected to gateway |
| `openclaw docs` | Search live documentation |
| `openclaw dns` | DNS helper for wide-area discovery |
| `openclaw completion` | Shell completion setup |
| `openclaw plugins` | Manage extensions |
| `openclaw secrets` | Handle secret providers and references |
| `openclaw sandbox` | List and recreate sandboxes |
| `openclaw approvals` | Manage approval allowlists |
| `openclaw system` | System events and heartbeat controls |
| `openclaw qr` | QR code operations |

## 13. Global Flags

| Flag | Purpose |
|------|---------|
| `--dev` | Isolate state under `~/.openclaw-dev` |
| `--profile <name>` | Isolate state under `~/.openclaw-<name>` |
| `--no-color` | Disable ANSI colors |
| `--update` | Shorthand for `openclaw update` |
| `-V` / `--version` / `-v` | Print version |
