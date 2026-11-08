# AnvilAds MCP — client configuration

Connect AI is AnvilAds's remote MCP server. It is **read-only**: it
returns your business's stored Google Ads data (accounts, campaigns,
performance snapshots, reports, ad grades, lead totals) and changes
nothing.

- **Server URL:** `https://anvilads.com/mcp`
- **Transport:** streamable HTTP
- **Auth:** your per-business API key as a bearer token

## Get a key first

1. Sign in at https://anvilads.com (free to sign up).
2. In the dashboard, open **Connect AI** and create a key.
3. The key starts with `anv_mcp_` and is **shown once** — copy it then.
   Keys are stored hashed and can be revoked on the same page, effective
   immediately.

## Generic config (`mcp.json` in this folder)

```json
{
  "mcpServers": {
    "anvilads": {
      "type": "http",
      "url": "https://anvilads.com/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_ANVILADS_API_KEY"
      }
    }
  }
}
```

Replace `YOUR_ANVILADS_API_KEY` with your key. Keep the key out of
version control — prefer an environment variable where your client
supports one.

## Claude Code

Either add the config above to `.mcp.json` in your project (using
`"Authorization": "Bearer ${ANVILADS_API_KEY}"` and exporting
`ANVILADS_API_KEY`), or run:

```bash
claude mcp add --transport http anvilads https://anvilads.com/mcp \
  --header "Authorization: Bearer anv_mcp_YOUR_KEY"
```

Then ask Claude: "How are my Google Ads doing?" It will use the
`get_performance_summary`, `list_campaigns`, and `get_latest_report`
tools to answer from your stored AnvilAds data.

## Cursor

Add the config to `~/.cursor/mcp.json` (global) or `.cursor/mcp.json`
in your project, restart Cursor, and the `anvilads` server appears in
the MCP tools list. The server is also listed on Cursor Directory from
this repo.

## Troubleshooting

- **401** — missing, malformed, or revoked key. Re-check the header, or
  create a fresh key on the Connect AI page in your dashboard.
- **Empty data** — no Google Ads account connected yet, or the first
  daily snapshot has not been stored. Connect an account in the
  dashboard first.

Full product page: https://anvilads.com/connect-ai
Support: support@anvilads.com
