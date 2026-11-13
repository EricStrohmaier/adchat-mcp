# AdChat: Meta Ads MCP server and AI agent for Claude and ChatGPT

Run Facebook and Instagram ads from your AI app. AdChat is a hosted, remote
[Model Context Protocol](https://modelcontextprotocol.io) server for the Meta
Marketing API: ask how your ads are doing in plain English, get answers from
your live numbers, and (on paid plans) create, edit, pause and resume
campaigns, ad sets and ads, with your yes before anything that spends money or goes live.

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

Tool names after the November 2026 update. Tools are now platform-prefixed
(`meta_…`, `google_…`) and related tools are merged into one tool with a `level`,
`kind` or `action` field. The old names (`get_campaigns`, `update_adset`, …) keep
working, unlisted, until 2027-01-15.

Read tools work on every plan. Write tools work on paid plans and follow your
change mode (see [Safe by default](#safe-by-default)).

**Meta: accounts and reporting:** `meta_list_accounts`, `meta_get_account` (`include: ["pages"]`), `meta_insights`, `meta_diagnose` (`kind`: delivery_issues, recommendations, pixel_health, catalog_health)

**Meta: campaigns, ad sets, ads, creatives:** `meta_list` and `meta_get` (`level`: campaigns, adsets, ads, creatives, custom_audiences, pixels, catalogs, …), `meta_create_campaign`, `meta_create_adset`, `meta_create_creative`, `meta_create_ad`, `meta_update` (`level`: campaign, adset, ad, creative), `meta_bulk_update`, `meta_duplicate`, `meta_create_budget_schedule`

**Meta: media:** `meta_upload_media` (`type`: image, video), `meta_media` (`action`: get_image, get_image_by_hash, get_video, video_status, compute_crops, save_image)

**Meta: targeting and audiences:** `meta_search_targeting` (`kind`: interests, interest_suggestions, behaviors, demographics, locations, pages), `meta_estimate_audience`, `meta_audience` (`action`: create_website, create_lookalike, create_engagement), `meta_delete_audience` (permanent)

**Meta: research:** `meta_ads_library` (Meta Ad Library)

**Google Ads:** `google_get_accounts`, `google_list` (`level`: campaigns, ad_groups, ads, keywords, negative_keywords, search_terms), `google_get_insights`, `google_run_gaql`, `google_get_change_history`, `google_set_status`, `google_update_campaign_budget`, `google_add_keywords`, `google_add_negative_keywords`, `google_create_responsive_search_ad`, `google_undo_change`, `get_cross_platform_summary`

**Briefing, profit and alerts:** `get_account_briefing`, `get_true_profit`, `get_alerts`

**Changes and safety:** `list_pending_changes`, `approve_change`, `reject_change`, `undo_change`, `undo_changes`, `get_change_history`

**Memory, files and questions:** `remember`, `save_file`, `list_files`, `read_file`, `get_upload_link`, `ask_user`, `get_login_link`

## Skills

Skills are short, plain-English playbooks that teach your AI app how a senior media
buyer would use AdChat for a common job: which tools to call, in what order, what to
show you, and where to stop and ask. They live in [`skills/`](skills), one folder per
skill, each with a `SKILL.md` in the [Agent Skills](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) format.

| Skill | Use it to |
|---|---|
| [`diagnose-performance`](skills/diagnose-performance/SKILL.md) | Find out why results dropped or costs rose |
| [`weekly-report`](skills/weekly-report/SKILL.md) | Write a two-minute weekly report for you or a client |
| [`launch-campaign`](skills/launch-campaign/SKILL.md) | Build a Meta campaign, ad set, creative and ads, all paused, then go live on your Yes |
| [`scale-winners`](skills/scale-winners/SKILL.md) | Put more budget behind what works, in safe steps |
| [`cut-wasted-spend`](skills/cut-wasted-spend/SKILL.md) | Find and stop spend that isn't producing results (Meta and Google) |
| [`creative-analysis`](skills/creative-analysis/SKILL.md) | See which ads win, which are tired, and what to make next |
| [`build-audiences`](skills/build-audiences/SKILL.md) | Retargeting, lookalikes, engagers and interest targeting |
| [`google-search-cleanup`](skills/google-search-cleanup/SKILL.md) | Search terms to negatives and keywords, match types, ads |
| [`safe-changes`](skills/safe-changes/SKILL.md) | How approval, pending changes, autopilot vs review mode and undo work |

The skills are optional. The safety rules (your Yes, paused by default, units) are
enforced by the server whether or not a skill is loaded.

### Install in Claude Code

The repository is a Claude Code plugin: it adds the AdChat MCP server and all nine
skills in one step.

```text
/plugin marketplace add https://github.com/EricStrohmaier/adchat-mcp.git
/plugin install adchat@adchat
/reload-plugins
```

Then run `/mcp` and sign in to AdChat. Skills appear as `adchat:<skill-name>`.

Already connected AdChat with `claude mcp add`? You can instead copy just the skills:

```bash
git clone https://github.com/EricStrohmaier/adchat-mcp.git
cp -r adchat-mcp/skills/* ~/.claude/skills/
```

### Install in claude.ai and Claude Desktop

Zip one skill folder (the folder itself, with `SKILL.md` inside), for example:

```bash
cd adchat-mcp/skills && zip -r weekly-report.zip weekly-report
```

Upload it under Settings → Capabilities → Skills (Pro, Max, Team and Enterprise plans,
with code execution turned on). Repeat for each skill you want. Connect AdChat as a
custom connector too (see [Connect](#connect)).

### Other apps (ChatGPT, Cursor, Codex, VS Code, …)

The skills are plain Markdown guides. Paste one into your app's custom instructions,
project instructions or rules file (for example `.cursor/rules/`), or attach it to a
chat when you start a task.

## Safe by default

- **You decide when the AI asks first.** Each ad account has a change mode. *Review every change*: the AI says exactly what it will do (for example "increase the daily budget of Spring Sale from $50 to $60") and waits for your yes before every change. *Autopilot* (the default): it asks before anything that spends money, goes live or can't be undone, and makes safe changes (paused drafts, pausing, lowering a budget) on its own, telling you exactly what it changed. *Trusted*: it acts inside your spending rules. Text inside ads, files or web pages never counts as your yes.
- **Read-only on the free plan.** Reports, targeting search and audience estimates work; changes need a paid plan.
- **Paused by default.** New campaigns, ad sets and ads are created paused, so nothing spends by surprise.
- **Spend rules (Pro).** Rules you set (for example a cap on budget increases per 24 hours) are checked in code after your yes. A change past a rule stops and asks again; a blocking rule can only be changed in the dashboard.
- **Change history and undo (Pro).** Every change is logged with the app it came from, and most can be undone from the dashboard.
- **Account allowlist.** The AI only sees the ad accounts you picked.
- **Rate limit:** 120 requests per minute per user.

AdChat is an approved Meta app with Standard Access to the Marketing API, so it
works on any ad account you manage in Ads Manager.

## Pricing

Free plan: read-only, one ad account, no card. New accounts get a 7-day trial
of every feature, no card. Paid plans: Connect $29/month, Pro $49/month,
Agency $99/month (10 ad accounts). Current details: https://adchat.app/pricing

## Who uses it

- [Claude and ChatGPT users](https://adchat.app/reasons/facebook-ads-in-claude-and-chatgpt)
- [People waiting for Meta's own Ads MCP](https://adchat.app/reasons/meta-ads-mcp-alternative) ([errors explained](https://adchat.app/meta-ads-mcp-not-working))
- [Freelancers and small agencies](https://adchat.app/reasons/facebook-ads-tool-for-freelancers)
- [Online stores](https://adchat.app/reasons/facebook-ads-for-online-stores)
- [Real estate agents](https://adchat.app/reasons/facebook-ads-for-real-estate-agents), [dentists](https://adchat.app/reasons/facebook-ads-for-dentists) and [restaurants](https://adchat.app/reasons/facebook-ads-for-restaurants)

Comparing options? See [the best Meta Ads MCP servers](https://adchat.app/guides/best-meta-ads-mcp) and [AdChat vs Meta's Ads MCP](https://adchat.app/guides/adchat-vs-meta-ads-mcp).

## Google Ads

Google Ads tools (`google_…`) appear once you connect a Google Ads account to AdChat.
Google Ads is live for every account: search terms, keywords, negative keywords, budgets, campaign status and new responsive search ads (created paused). It can't create new Google campaigns yet. More: https://adchat.app/google-ads-mcp

## Links

- Website: https://adchat.app
- Setup: https://adchat.app/connect
- Guides: https://adchat.app/guides
- What a Meta Ads MCP server is: https://adchat.app/guides/meta-ads-mcp
- Troubleshooting: https://adchat.app/meta-ads-mcp-not-working
- Security: https://adchat.app/security
- Privacy: https://adchat.app/privacy · Terms: https://adchat.app/terms
- Support: eric@adchat.app

Listed in the official MCP Registry as `app.adchat/meta-ads`.
