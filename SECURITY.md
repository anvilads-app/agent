# Security

## API keys

AnvilAds Connect AI keys (prefix `anv_mcp_`) are **per-business**: a key
can only read the data of the one business it was created for. Keys are
shown exactly once at creation, stored hashed on AnvilAds servers, and
can be revoked at any time from the dashboard (Dashboard → Connect AI).

- Never commit a real key to this or any repository. Config examples in
  this repo use placeholders on purpose.
- If a key leaks, revoke it in the dashboard and create a new one. The
  MCP server is read-only, so a leaked key cannot change campaigns,
  budgets, or billing — but treat it as a credential anyway.

## Reporting a vulnerability

Email **support@anvilads.com** with the details. Please do not open a
public issue for a security problem.
