# Channel-Specific Troubleshooting

## Telegram

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

### Common Issues
- **Bot not responding in server**: Check that the bot has the correct OAuth2 scopes (bot + applications.commands) and is in the server
- **Missing messages**: Verify the bot has "Message Content Intent" enabled in the Discord Developer Portal
- **Role-based routing**: Discord supports routing by role — bindings can match on Discord role IDs
- **Guild ID routing**: Use guild (server) ID in agent bindings for per-server agent assignment

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

---

## iMessage

### Key Points
- iMessage requires macOS with Messages.app
- Uses AppleScript/Shortcuts integration
- More limited than other channels — no streaming, limited media support
- Check macOS permissions for automation

---

## Slack

### Common Issues
- **OAuth scopes**: Ensure the app has all required scopes (chat:write, channels:history, etc.)
- **401/403 errors**: Usually missing OAuth scopes
- **Event subscriptions**: Must be configured in the Slack app dashboard

---

## WhatsApp

### Key Points
- Requires WhatsApp Business API or Cloud API setup
- More complex setup than other channels
- Rate limiting is strict — configure fallbacks

---

## General Channel Troubleshooting

For any channel:
1. `openclaw channels status --probe` — validates configuration and connectivity
2. `openclaw logs --follow` — watch for channel-specific errors
3. Check `dmPolicy` and `allowFrom` settings
4. Verify the channel is `enabled: true` in config
5. After config changes, the gateway hot-reloads most settings automatically
