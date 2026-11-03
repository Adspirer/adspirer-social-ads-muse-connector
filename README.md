# Adspirer Social Ads for Muse

Paid social agent for Meta Ads (Facebook and Instagram) and TikTok Ads. Research competitor ads, review performance, catch creative fatigue and wasted spend, and plan, create and optimize campaigns, ad sets and ads, with your approval before any change that affects spend.

This is Adspirer's company-owned **submission kit for the Muse hosted MCP connector** covering Meta Ads (Facebook and Instagram) and TikTok Ads. It is not a Muse plugin runtime. The hosted Adspirer service is the runtime; this repository holds review materials only. The main connector, covering every platform, has its own kit at [Adspirer/adspirer-muse-connector](https://github.com/Adspirer/adspirer-muse-connector).

## Connection

| Field | Value |
| --- | --- |
| Connection type | Existing MCP |
| Endpoint | `https://mcp.adspirer.com/social-ads` |
| Transport | Streamable HTTP |
| Authentication | OAuth with PKCE (S256) |
| Registration | Dynamic client registration |
| Support | support@adspirer.com |

## Connector overview (guidelines §5.1)

- Who it's for: businesses, in-house marketers and agencies running Facebook, Instagram or TikTok ads.
- What it does: cross-platform reporting (spend, CPA, ROAS, CPM, CTR, frequency), creative-fatigue and wasted-spend analysis, audience and budget changes, ad copy, competitor research from the public Meta and TikTok ad libraries, and paused creation of campaigns, ad sets and ads.
- Why it beats the browser: it reads the user's own ad accounts through the Meta Marketing API and TikTok API for Business, so answers come from real account data. It finishes approved changes in the account instead of sending the user to Ads Manager.
- Relationship to Meta: Adspirer is an independent Meta Marketing API developer. For Muse users who advertise on Meta, it manages their own ad accounts with their authorization.

An Adspirer account and admin access to at least one Meta ad account or TikTok Ads account (read-only access is enough for reporting).

## Tools

10 top-level tools, each classified Read, Write or Sensitive write in [submission/TOOLS.md](submission/TOOLS.md). Every platform has a read-only router that the server enforces, and every write router asks for confirmation on the server before it runs.

## Submission workflow

1. Review `submission/connector.json`, `submission/FORM.md` and `submission/TOOLS.md`. The JSON is our internal source of truth, **not a published Muse manifest format**.
2. Run `python3 scripts/package.py --check` and `python3 scripts/package.py`.
3. Complete the [reviewer checklist](submission/REVIEWER.md) and arrange reviewer access privately.
4. Use the [Muse submission portal](https://muse.ai/platform). Copy the form details, upload the Adspirer icon, and attach or link the tool documentation.
5. Record the receipt and review state in a submission-tracking issue. Never put credentials or private reviewer details in public issues.

CI validates and builds a reviewer ZIP on pushes, pull requests and manual runs. It never submits to Muse.

## Status

Kit prepared 2026-11-02. Not yet submitted. Muse authorization and live tool execution remain to be verified in Muse.

## Documentation

- [Muse setup](https://www.adspirer.com/docs/ai-clients/muse)
- [MCP documentation](https://www.adspirer.com/docs/mcp)
- [Tool catalog](https://www.adspirer.com/docs/agent-skills/tools)
- [Security and data handling](https://www.adspirer.com/docs/knowledge-base/security)
- [Privacy policy](https://www.adspirer.com/privacy)
