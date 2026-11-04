# AdChat: Meta Ads MCP server

Run Facebook and Instagram ads from your AI app. AdChat is a hosted, remote
[Model Context Protocol](https://modelcontextprotocol.io) server for the Meta
Marketing API: ask how your ads are doing in plain English, get answers from
your live numbers, and (when you turn it on) create, edit, pause and resume
campaigns, ad sets and ads.

```
https://mcp.adchat.app
```

- **Transport:** Streamable HTTP
- **Auth:** OAuth 2.1 with PKCE (S256) and dynamic client registration. Sign in with your AdChat account; no API keys, no Meta tokens to copy.
- **Works in:** Claude, ChatGPT, Grok and Grok Bot, Cursor, VS Code, Claude Code, Codex, and any app that accepts a remote MCP server URL.
- **Website:** https://adchat.app · **Setup for every app:** https://adchat.app/connect

> This repository holds documentation and directory metadata for the hosted
> server. The server itself is operated by AdChat and is not open source.

## Connect

| App | How |
|---|---|
| **Claude** (claude.ai, desktop) | Customize → Connectors → **+ Add** → **Add custom connector**, name it AdChat, paste the URL. [Guide](https://adchat.app/guides/claude) |
| **ChatGPT** | chatgpt.com/plugins → **+** → **Add custom MCP server**, paste the URL, choose OAuth. [Guide](https://adchat.app/guides/chatgpt) |
| **Grok** | grok.com/connectors → **New Connector** → **Custom**, paste the URL. [Guide](https://adchat.app/guides/grok) |
| **Grok Bot** | Ask your bot: `Add a custom MCP server called adchat at https://mcp.adchat.app and start the sign-in.` |
| **Claude Code** | `claude mcp add --transport http adchat https://mcp.adchat.app`, then `/mcp` to sign in |
| **Cursor** | [Add to Cursor](https://adchat.app/connect?app=cursor), or add to `~/.cursor/mcp.json` (below) |
| **VS Code** | [Add to VS Code](https://adchat.app/connect?app=vscode), or add an HTTP MCP server with the URL |
| **Anything else** | Paste the URL where the app asks for a remote MCP server and sign in when prompted |

```json
{
  "mcpServers": {
    "adchat": { "url": "https://mcp.adchat.app" }
  }
}
```

The first time you connect, you sign in to AdChat (free, no card), connect
Facebook once, and choose which ad accounts the AI may see.

## What you can ask

- "How did my Facebook ads do last week? Which ad had the lowest cost per result?"
- "Break down last month's spend by age, placement and country."
- "Which ad sets are spending without results?"
- "Draft a 15% budget increase on my best ad set and show me before you apply it."
- "Build a 1% lookalike of my purchasers in the US and estimate its size."

## Tools

55 tools on the hosted server. Read tools are always on; write tools need write
access turned on in the AdChat dashboard.

**Accounts and reporting:** `get_ad_accounts`, `get_account_info`, `get_account_pages`, `get_account_briefing`, `get_insights`, `get_true_profit`, `get_alerts`

**Campaigns, ad sets, ads:** `get_campaigns`, `get_campaign_details`, `create_campaign`, `update_campaign`, `get_adsets`, `get_adset_details`, `create_adset`, `update_adset`, `create_budget_schedule`, `get_ads`, `get_ad_details`, `create_ad`, `update_ad`

**Creatives and media:** `get_ad_creatives`, `get_creative_details`, `create_ad_creative`, `update_ad_creative`, `get_ad_image`, `get_image_by_hash`, `get_ad_video`, `upload_ad_image`, `get_upload_link`, `compute_image_crops`

**Targeting and audiences:** `search_interests`, `get_interest_suggestions`, `search_behaviors`, `search_demographics`, `search_geo_locations`, `estimate_audience_size`, `get_custom_audiences`, `get_custom_audience_details`, `create_website_audience`, `create_engagement_audience`, `create_lookalike_audience`, `delete_custom_audience`

**Research:** `search_ads_archive` (Meta Ad Library), `search_pages_by_name`

**Changes and safety:** `list_pending_changes`, `approve_change`, `reject_change`, `undo_change`, `get_change_history`

**Memory and files:** `remember`, `save_file`, `list_files`, `read_file`, `ask_user`, `get_login_link`

## Safe by default

- **Read-only until you say otherwise.** Write tools are refused until write access is turned on for your account.
- **Paused by default.** New campaigns, ad sets and ads are created paused, so nothing spends by surprise.
- **Drafts and approval (Pro).** Changes can be returned as drafts you approve first, checked against spend rules (for example a cap on budget increases per 24 hours) and protected campaigns that are enforced in code.
- **Undo (Pro).** Most changes can be reversed in one click for 30 days.
- **Account allowlist.** The AI only sees the ad accounts you picked.
- **Rate limit:** 120 requests per minute per user.

AdChat is an approved Meta app with Standard Access to the Marketing API, so it
works on any ad account you manage in Ads Manager.

## Pricing

Free plan: read-only, one ad account, no card. Paid plans: Connect $29/month,
Pro $49/month, Agency $99/month. Current details: https://adchat.app/pricing

## Links

- Website: https://adchat.app
- Setup: https://adchat.app/connect
- Guides: https://adchat.app/guides
- Troubleshooting: https://adchat.app/meta-ads-mcp-not-working
- Security: https://adchat.app/security
- Privacy: https://adchat.app/privacy · Terms: https://adchat.app/terms
- Support: eric@adchat.app

Listed in the official MCP Registry as `app.adchat/meta-ads`.
