# Changelog

## [v1.1.0] - 2026-04-07

### Added
- Symptom-to-diagnosis quick reference table in SKILL.md — maps user-reported symptoms directly to probable causes and fix commands
- Coverage depth indicators in channels.md — each channel now shows its documentation depth (Deep/Moderate/Basic)
- Explicit fallback guidance for basic-coverage channels ("run `openclaw docs <channel>` for more")
- Setup checklists for Discord, Slack, iMessage, and WhatsApp
- Error/issue tables for Discord, Slack, iMessage, and WhatsApp (previously only Telegram had one)
- 7 new evaluation scenarios: cron failures, session cleanup, security audit, Discord issues, cold-start triage, rate limiting, and forum topic routing (total: 10 evals, up from 3)

### Changed
- Expanded Discord section from 4 bullet points to full setup checklist + error table + routing docs
- Expanded Slack section from 3 bullet points to setup checklist + error table + config example
- Expanded iMessage section from 4 bullet points to requirements + limitations + error table
- Expanded WhatsApp section from 3 bullet points to requirements + error table

## [v1.0.0] - 2026-04-07

### Added
- Core diagnostic skill (SKILL.md) with 5-step troubleshooting workflow
- CLI reference covering 80+ OpenClaw commands across 13 categories
- Channel-specific troubleshooting for Telegram, Discord, iMessage, Slack, and WhatsApp
- Detailed Telegram guide: privacy mode, DM policies, groups, webhooks, forums, exec approvals
- Emergency playbooks for hung handlers, stale session locks, and cron failures
- Multi-agent routing and model fallback configuration guidance
- Security hardening and config optimization patterns
- 3 evaluation scenarios (hung handler, post-update auth, multi-agent setup)
- GitHub Actions CI for skill validation on push/PR
