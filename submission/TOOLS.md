# Tool documentation: Adspirer Social Ads

Endpoint: `https://mcp.adspirer.com/social-ads`. Tool list as served on 2026-11-02: 10 top-level tools (7 read, 1 write, 2 sensitive write). The live `tools/list` response is authoritative; re-check it before submitting and update this file if it differs.

Classes follow the Muse connector guidelines (§3): **Read** changes nothing; **Write** creates, changes or deletes something; **Sensitive write** is consequential or irreversible, such as spending money or deleting data. A tool that can both read and write is classified as a write.

| Tool | Class | What it does |
| --- | --- | --- |
| `get_connections_status` | Read | Lists the user's connected ad accounts and their health. |
| `get_usage_status` | Read | Shows the user's Adspirer plan, quota and usage this period. |
| `get_tool_schema` | Read | Returns the exact input schema for a tool before it is called. |
| `get_meta_campaign_performance` | Read | Meta (Facebook and Instagram) campaign performance for a date range. |
| `competitor_ads_research` | Read | Looks up a brand's public ads in the Meta and TikTok ad libraries. Reads public data only. |
| `meta_ads_read` | Read | Router for read-only Meta tools: campaigns, ad sets, ads, audiences, creatives, pixels, insights. The server refuses any mutating tool through this router. |
| `tiktok_ads_read` | Read | Router for read-only TikTok tools: campaigns, ad groups, ads, creatives, pixels, reports. The server refuses any mutating tool through this router. |
| `activate_ad_accounts` | Write | Chooses which of the user's connected ad accounts are active in Adspirer. Changes Adspirer state only; it never changes anything in the ad platform and is reversible. |
| `meta_ads_write` | Sensitive write | Router for Meta changes: create campaigns, ad sets and ads (always paused), enable or pause, budgets and bids, audiences, creatives, deletions. Can affect real ad spend. The server refuses read-only tools through this router and asks for confirmation before executing. |
| `tiktok_ads_write` | Sensitive write | Router for TikTok changes: campaigns, ad groups and ads (created paused), budgets, bids, targeting, creatives. Can affect real ad spend. Same server-side confirmation as meta_ads_write. |

## How the read/write split is enforced

Platform operations sit behind paired routers: each platform has a `*_read` tool and a `*_write` tool, called with `action: "list_tools"` and then `action: "execute"` with a `tool_name`.

- **Read routers** list and run only read-only operations. The server refuses any mutating operation sent through them, so a user or reviewer can grant read-only use by approving only the read tools.
- **Write routers** list and run only mutating operations. Before executing, the server asks for confirmation: an MCP elicitation where the client supports it, otherwise a preview that runs only when the call is repeated with `confirm: true`. A decline returns "was not run" and makes no change.
- **New campaigns are always created paused.** Turning one on, raising a budget or changing a bid is a separate sensitive write that needs its own approval.

Muse's own approval step for sensitive writes still applies on top of this. The server-side confirmation is an extra safeguard, not a substitute.

## Inputs, outputs and errors

- `tools/list` returns a JSON Schema for each tool. `get_tool_schema` returns the schema of any operation behind a router.
- Results are MCP `content` blocks with text, plus structured data where the operation returns it. Money is reported in the ad account's currency.
- Errors return `isError: true` with a plain-language message. Common cases: no connected account for the platform, several accounts connected and none named (the error lists them), insufficient platform permissions, plan quota exhausted (`get_usage_status` explains the limit), and platform API errors, passed through with the platform's message.
- Rate limits: the ad platforms' API limits apply, and Adspirer meters tool calls against the user's plan.

## Authentication

OAuth 2.1 authorization code flow with PKCE (S256) and dynamic client registration. Protected-resource metadata: `https://mcp.adspirer.com/.well-known/oauth-protected-resource/social-ads`. Advertised scopes include `campaigns:read`, `campaigns:write` and `performance:read`. Ad-platform credentials stay server-side with Adspirer and are never exposed to Muse.

## Data accessed

Account and campaign data from Meta and TikTok (names, budgets, audiences, creatives, performance metrics). If the user runs lead-generation ads, read tools can return lead-form submissions, which contain personal data such as names and contact details; they are fetched only when the user asks for them. Competitor research reads only public ad-library data.
