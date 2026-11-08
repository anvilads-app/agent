---
name: anvilads
description: Connect to AnvilAds (Connect AI) to check a business's Google Ads performance from an AI agent. Use when the user asks how their Google Ads are doing, wants a campaign performance summary, their latest AnvilAds morning or weekly report, their ad audit grades, lead totals (leads, booked, won, revenue), or which Google Ads accounts and campaigns AnvilAds is managing for them. Read-only access to stored AnvilAds data via a remote MCP server and a per-business API key.
---

# AnvilAds — Check Google Ads Performance From Your Agent

AnvilAds (https://anvilads.com) is an AI agent that builds, launches, and
optimizes Google Ads for a business. Nothing goes live without the owner's
approval in the AnvilAds dashboard.

This skill connects your agent to **Connect AI**, AnvilAds's remote MCP
server, so you can answer questions about the business's ads with its real,
stored data:

- "How are my Google Ads doing?"
- "What did my campaigns spend this week, and what did I get for it?"
- "What was in my latest AnvilAds report?"
- "What grades did my ads get on the last audit?"
- "How many leads have I won, and how much revenue is that?"

## What this connection can and cannot do

Connect AI is **read-only, by design**. It returns stored data only: no
writes, no changes, no live calls to Google. Every tool is scoped to the
one business that owns the API key — a key physically cannot see another
business's data, and no tool accepts a business or account parameter.

Anything that changes ads — building campaigns, approving launches,
pausing, editing budgets — happens in the AnvilAds dashboard at
https://anvilads.com. If the user asks you to change something, summarize
what they want and send them to the dashboard to approve it there.

## Setup

### 1. Get an API key

The user needs an AnvilAds account with at least one connected Google Ads
account. Keys are per-business and self-serve:

1. Sign in at https://anvilads.com (free to sign up).
2. In the dashboard, open **Connect AI**.
3. Create a key. It starts with `anv_mcp_` and is **shown exactly once** —
   copy it immediately.
4. Keys are stored hashed on AnvilAds's side and can be revoked from the
   same Connect AI page at any time. Revoking takes effect immediately.

If the user has no account yet, send them to https://anvilads.com to sign
up first, then to the Connect AI page in their dashboard.

### 2. Configure the MCP server

Server URL: `https://anvilads.com/mcp` (streamable HTTP).
Auth: the API key as a bearer token — `Authorization: Bearer <key>`.
Requests without a valid key return 401.

**Claude Code** — add to `.mcp.json` in the project, or run
`claude mcp add --transport http anvilads https://anvilads.com/mcp --header "Authorization: Bearer anv_mcp_YOUR_KEY"`:

```json
{
  "mcpServers": {
    "anvilads": {
      "type": "http",
      "url": "https://anvilads.com/mcp",
      "headers": {
        "Authorization": "Bearer ${ANVILADS_API_KEY}"
      }
    }
  }
}
```

Then set the environment variable: `export ANVILADS_API_KEY="anv_mcp_..."`.

**Cursor** — add the same block to `~/.cursor/mcp.json` (or the project's
`.cursor/mcp.json`), replacing the placeholder with the key or an
environment-variable reference your client supports.

**Any MCP client** — point it at the URL above with the bearer header.
The server speaks MCP over streamable HTTP (protocol 2025-03-26).

### Security rules for the key

- Treat the key like a password. Never print it in full, never commit it
  to a repo, never paste it into a chat or a ticket.
- Prefer an environment variable (`ANVILADS_API_KEY`) over a literal key
  in a config file.
- If a key may have leaked, tell the user to revoke it on the Connect AI
  page and create a new one. Revocation is immediate.

## The 7 tools

All tools take **no arguments**. Each returns the data for the key's
business only.

| Tool | What it returns |
| --- | --- |
| `list_ad_accounts` | The Google Ads accounts connected to this business in AnvilAds (customer id, name, currency, status). Stored data only. |
| `list_campaigns` | Campaigns from the most recent stored snapshot for each connected ad account (name, type, status, budget, spend, conversions, CPA). |
| `get_performance_summary` | Latest stored snapshot metrics per ad account: spend, clicks, conversions, CPA, and the snapshot window. |
| `get_latest_report` | The most recent morning or weekly report AnvilAds generated for this business, including whether it was emailed. |
| `get_lead_totals` | Lead pipeline totals from the Leads tab: leads, booked, won, lost, and won revenue. Test leads are excluded. |
| `get_ad_grades` | The latest stored audit findings with their letter grades and scores. |
| `get_business_profile` | The business's own basics: name, industry, location, goal, plan tier, subscription status, and credit balance. No secrets or contact details. |

Notes on freshness: snapshots are stored when AnvilAds runs its daily
checks, and reports are generated each morning (and weekly). Always tell
the user the snapshot time or window a tool returned — never present a
stored snapshot as live, to-the-minute data.

## Suggested workflows

### Morning performance check

1. Call `get_performance_summary` for the headline numbers (spend,
   clicks, conversions, CPA per account).
2. Call `list_campaigns` to see which campaigns drove them.
3. Call `get_latest_report` and fold in anything the morning report
   flagged.
4. Summarize in plain language: what was spent, what it produced, and
   anything that looks off. Name the snapshot window.

### Audit-grade review

1. Call `get_ad_grades` for the latest findings, grades, and scores.
2. Group findings by severity. Explain the lowest grades first, in plain
   language — what the finding means and why it matters.
3. Fixes themselves happen in AnvilAds (the agent works on the account
   daily; approvals happen in the dashboard). Point the user there for
   anything that needs action.

### Lead totals check

1. Call `get_lead_totals`.
2. Report leads, booked, won, lost, and won revenue. Test leads are
   already excluded, so the numbers are safe to quote as-is.

### "What is AnvilAds doing for me?" overview

1. Call `get_business_profile` (plan, status, credit balance).
2. Call `list_ad_accounts`, then `list_campaigns`.
3. Close with `get_latest_report` so the user hears the newest summary
   in AnvilAds's own words.

## Troubleshooting

- **401 Unauthorized** — the key is missing, malformed, revoked, or not
  sent as `Authorization: Bearer <key>`. Check the header first; if it
  still fails, have the user create a fresh key on the Connect AI page.
- **Empty results / "no snapshots yet"** — the business has not connected
  a Google Ads account yet, or AnvilAds has not run its first daily
  check. Send the user to the dashboard to connect an account.
- **No reports yet** — morning reports are generated daily for connected
  accounts; a brand-new connection may not have one until the next run.

## About AnvilAds

AnvilAds is an AI agent that builds, launches, and optimizes Google Ads —
the owner approves every launch. Pricing is public: credit packs from $25
(credits are $0.20 each and never expire), Unlimited at $499/mo, and a
Founding Beta at $249/mo. AnvilAds is a product of Faceless Marketing,
LLC. Support: support@anvilads.com. More on Connect AI:
https://anvilads.com/connect-ai
