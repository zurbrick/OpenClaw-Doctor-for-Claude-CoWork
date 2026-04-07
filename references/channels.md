# Channel-Specific Troubleshooting

## Coverage Depth

> **Important for Claude:** This table shows how much troubleshooting knowledge is available per channel. For channels marked "Basic," do NOT fabricate detailed troubleshooting steps — instead, recommend `openclaw docs <channel>` or checking the OpenClaw Discord for help.

| Channel | Coverage | What's Covered |
|---------|----------|---------------|
| Telegram | **Deep** | Setup, privacy mode, DM policies, groups, webhooks, forums, exec approvals, stickers, full error table |
| Discord | **Moderate** | Setup, OAuth scopes, intents, role routing, guild routing |
| Slack | **Basic** | Setup and common OAuth/scope issues only |
| iMessage | **Basic** | Setup requirements and macOS permissions only |
| WhatsApp | **Basic** | Setup requirements and rate limiting only |

For basic-coverage channels: guide the user through `openclaw channels status --probe` and `openclaw logs --follow` first (these work for all channels), then suggest `openclaw docs <channel>` for platform-specific details beyond what's documented here.

---

## Telegram

**Coverage: Deep** — full troubleshooting, config examples, and error tables.

### Setup Checklist
1. Create bot via @BotFather → `/newbot` → save token
2. Set token in config: `channels.telegram.botToken` or `TELEGRAM_BOT_TOKEN` env var
3. Set `dmPolicy` (default "pairing" — users need approval codes)
4. Configure group access in `channels.telegram.groups`
5. Start gateway: `openclaw gateway`

### Privacy Mode (Most Common Issue)
Telegram bots have Privacy Mode ON by default — they only see messages that mention them or are replies to them. This catches people constantly.

**Fix:**
1. Message @BotFather
2. Run `/setprivacy`
3. Select your bot
4. Choose `Disable`
5. **Remove and re-add the bot to each group** (privacy change only takes effect after rejoin)

If you don't want to disable privacy mode globally, make the bot a group admin instead.

### DM Access Control
| `dmPolicy` | Behavior |
|------------|----------|
| `pairing` (default) | Users need one-time approval code (expires 1 hour) |
| `allowlist` | Only numeric user IDs in `allowFrom` can message |
| `open` | Anyone can message (use `allowFrom: ["*"]`) |
| `disabled` | All DMs blocked |

**Finding your Telegram user ID:** DM the bot and check `openclaw logs --follow` — look for the `from.id` field.

### Group Configuration
```json5
{
  channels: {
    telegram: {
      groups: {
        "-1001001234567": {          // Group chat ID
          groupPolicy: "open",       // Who can trigger responses
          requireMention: false,     // Respond without @mention
        },
      },
    },
  },
}
```

Get group IDs by forwarding a message to `@userinfobot` or checking gateway logs.

### Mention Requirements
- Default: groups require @mention
- Override per-group: `requireMention: false`
- Bot commands: `/activation always` (no mention needed) or `/activation mention` (mention required)

### Polling vs Webhook
Default is long polling (simpler, works behind NAT). For webhook mode:
```json5
{
  channels: {
    telegram: {
      webhookUrl: "https://your-domain.com/telegram",
      webhookSecret: "your-secret",
      webhookPort: 8787,
    },
  },
}
```

### Forum Topics (Supergroups)
Topic IDs append to session keys as `:topic:<threadId>`. Each topic can route to a different agent via `agentId`. Topics inherit group settings unless overridden.

### Common Telegram Errors
| Error/Log Message | Meaning | Fix |
|-------------------|---------|-----|
| "drop guild message (mention required)" | Bot requires @mention in groups | Set `requireMention: false` or disable privacy mode |
| "pairing request" | User hasn't been approved | Approve via `openclaw pairing approve telegram <code>` |
| "Group not reachable by bot" | Bot not in group or wrong group ID | Invite bot, DM `/start`, restart gateway |
| "BOT_COMMANDS_TOO_MUCH" | Too many menu entries | Reduce commands or set `commands.native: false` |
| "fetch failed" / "Network request failed" | Network issues | Check DNS, try `network.autoSelectFamily: false` for IPv6 issues |
| Streaming glitches | Live preview edit failures | Set `streaming: "off"` to disable streaming preview |

### Sticker Support
Enable with `actions.sticker: true`. Stickers are cached at `~/.openclaw/telegram/sticker-cache.json`.

### Exec Approvals via Telegram
```json5
{
  channels: {
    telegram: {
      execApprovals: {
        enabled: true,
        approvers: ["123456789"],  // Numeric user IDs
        target: "dm"
      },
    },
  },
}
```

---

## Discord

**Coverage: Moderate** — setup, permissions, and routing. For advanced Discord troubleshooting (slash commands, embed formatting, voice), run `openclaw docs discord`.

### Setup Checklist
1. Create application in [Discord Developer Portal](https://discord.com/developers/applications)
2. Enable "Message Content Intent" under Bot settings (required to read messages)
3. Add bot to server with OAuth2 scopes: `bot` + `applications.commands`
4. Set token in config: `channels.discord.botToken` or `DISCORD_BOT_TOKEN` env var
5. Start gateway: `openclaw gateway`

### Common Discord Issues
| Issue | Cause | Fix |
|-------|-------|-----|
| Bot not responding in server | Missing OAuth2 scopes or bot not in server | Re-invite with correct scopes (`bot` + `applications.commands`) |
| Missing messages / bot ignores some messages | "Message Content Intent" not enabled | Enable in Discord Developer Portal → Bot → Privileged Gateway Intents |
| Bot responds in wrong server | No guild-specific routing | Use guild (server) ID in agent bindings for per-server assignment |
| Permission errors | Missing channel permissions | Ensure bot role has Send Messages, Read Message History, Embed Links |

### Configuration Pattern
```json5
{
  channels: {
    discord: {
      enabled: true,
      botToken: "your-discord-bot-token",
      dmPolicy: "pairing",
    },
  },
}
```

### Routing
- **Role-based routing**: Bindings can match on Discord role IDs — useful for routing different teams to different agents
- **Guild ID routing**: Use guild (server) ID in agent bindings for per-server agent assignment
- **DM vs server**: DMs route through the default agent unless explicitly bound

---

## Slack

**Coverage: Basic** — setup and common permission issues. For advanced Slack troubleshooting (interactive components, modals, home tabs), run `openclaw docs slack`.

### Setup Checklist
1. Create a Slack app at [api.slack.com/apps](https://api.slack.com/apps)
2. Add required OAuth scopes: `chat:write`, `channels:history`, `channels:read`, `im:history`, `im:read`, `im:write`
3. Enable Event Subscriptions and subscribe to: `message.channels`, `message.im`
4. Install app to workspace and copy the Bot User OAuth Token
5. Set token in config: `channels.slack.botToken` or `SLACK_BOT_TOKEN` env var

### Common Slack Issues
| Issue | Cause | Fix |
|-------|-------|-----|
| 401 Unauthorized | Invalid or expired token | Re-install app to workspace, copy new Bot User OAuth Token |
| 403 Forbidden | Missing OAuth scopes | Add required scopes in app settings, reinstall to workspace |
| Bot doesn't see messages | Event subscriptions not configured | Configure Event Subscriptions in Slack app dashboard |
| Bot can't post to channel | Missing `chat:write` scope or not in channel | Add scope, invite bot to channel with `/invite @botname` |

### Configuration Pattern
```json5
{
  channels: {
    slack: {
      enabled: true,
      botToken: "xoxb-your-slack-bot-token",
      dmPolicy: "pairing",
    },
  },
}
```

---

## iMessage

**Coverage: Basic** — setup requirements and platform constraints. For advanced iMessage troubleshooting, run `openclaw docs imessage`.

### Requirements
- macOS only (requires Messages.app)
- Uses AppleScript/Shortcuts integration
- macOS must grant automation permissions to the gateway process

### Limitations
- No streaming support
- Limited media support compared to other channels
- No group chat management
- Delivery receipts not supported

### Common Issues
| Issue | Cause | Fix |
|-------|-------|-----|
| "not permitted" errors | Missing macOS automation permissions | System Settings → Privacy & Security → Automation → enable for gateway |
| Messages not sending | Messages.app not signed in | Open Messages.app and sign in to iMessage |
| Delayed responses | AppleScript execution overhead | Expected — iMessage integration is inherently slower |

---

## WhatsApp

**Coverage: Basic** — setup requirements and rate limiting. For advanced WhatsApp troubleshooting, run `openclaw docs whatsapp`.

### Requirements
- WhatsApp Business API or Cloud API account
- More complex setup than other channels — requires Meta Business verification for production

### Common Issues
| Issue | Cause | Fix |
|-------|-------|-----|
| Rate limited | WhatsApp's strict message limits | Configure model fallbacks, reduce message frequency |
| "Template required" | Initiating conversations requires approved templates | Use pre-approved message templates for first contact |
| Webhook delivery failures | Incorrect webhook URL or SSL issues | Verify webhook URL is HTTPS with valid certificate |

---

## General Channel Troubleshooting

For any channel, regardless of coverage depth:
1. `openclaw channels status --probe` — validates configuration and connectivity
2. `openclaw logs --follow` — watch for channel-specific errors
3. Check `dmPolicy` and `allowFrom` settings
4. Verify the channel is `enabled: true` in config
5. After config changes, the gateway hot-reloads most settings automatically
6. If all else fails: `openclaw docs <channel>` for the latest platform-specific docs
