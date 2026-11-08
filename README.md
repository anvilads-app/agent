# AnvilAds for AI Agents

[AnvilAds](https://anvilads.com) is an AI agent that builds, launches,
and optimizes Google Ads. You approve every launch.

This repo packages **Connect AI** — AnvilAds's remote MCP server — for
agent directories and skill registries, so your AI agent can check your
ads with your real data:

- How are my Google Ads doing? (spend, clicks, conversions, CPA)
- What did my campaigns do in the latest snapshot?
- What was in my latest morning report?
- What grades did my ads get?
- How many leads have I won, and for how much revenue?

Connect AI is **read-only by design**. It returns stored data only: no
writes, no changes, no live calls to Google. Every change and approval
happens in the AnvilAds dashboard.

- **Server:** `https://anvilads.com/mcp` (streamable HTTP)
- **Auth:** a per-business API key, sent as `Authorization: Bearer <key>`
- **Product page:** https://anvilads.com/connect-ai

## Get an API key

1. Sign up free at https://anvilads.com and connect a Google Ads account.
2. In the dashboard, open **Connect AI** and create a key.
3. The key starts with `anv_mcp_` and is shown **once** — copy it then.
   Keys are stored hashed and are revocable on the same page.

## Install

### skills.sh (Claude Code and other skill-based agents)

```bash
npx skills add anvilads-app/agent --skill anvilads
```

The skill lives at [`skills/anvilads/SKILL.md`](skills/anvilads/SKILL.md).
It teaches your agent how to configure the MCP connection, what the seven
tools return, and the standard workflows (morning check, grade review,
lead totals).

### Claude Code plugin

This repo is also a Claude Code plugin (`.claude-plugin/plugin.json`)
carrying the skill and the MCP server config. Set `ANVILADS_API_KEY` in
your environment to your key.

### Cursor Directory

Paste this repo's URL at https://cursor.directory/plugins/new and scan —
Cursor finds the MCP server config and the skill automatically. Manual
setup for any client is in [`mcp-config/`](mcp-config/README.md).

### Hermes Agent

```bash
hermes plugins install https://github.com/anvilads-app/agent
```

The repo root carries a Hermes-style `plugin.json` and `mcp.json`.
Set `ANVILADS_API_KEY` in your environment to your key.

### Official MCP Registry

`server.json` in this repo is the registry entry
(`com.anvilads/anvilads`) for
[registry.modelcontextprotocol.io](https://registry.modelcontextprotocol.io).

## The seven tools

All read-only, no arguments, scoped to the business that owns the key:

| Tool | Returns |
| --- | --- |
| `list_ad_accounts` | Google Ads accounts connected in AnvilAds |
| `list_campaigns` | Campaigns from the latest stored snapshot per account |
| `get_performance_summary` | Spend, clicks, conversions, CPA per account |
| `get_latest_report` | Most recent morning or weekly report |
| `get_lead_totals` | Leads, booked, won, lost, won revenue (test leads excluded) |
| `get_ad_grades` | Latest audit findings with letter grades and scores |
| `get_business_profile` | Business basics, plan tier, credit balance |

## Pricing

Public and simple: credit packs from $25 (credits are $0.20 each and
never expire), Unlimited $499/mo, Founding Beta $249/mo.
https://anvilads.com/pricing

## About

AnvilAds is a product of Faceless Marketing, LLC.
Support: support@anvilads.com · Logo: the anvil mark
([white twin](https://anvilads.com/checkout-icon.png), as used on the
site; a graphite master is the primary brand asset).
