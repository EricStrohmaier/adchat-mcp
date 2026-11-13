---
name: cut-wasted-spend
description: Find and stop Meta and Google Ads spend that isn't producing results. Use when the user asks to cut waste, reduce costs, find what's not working, "where am I losing money", pause bad ads, lower budgets, or clean up an account in Meta (Facebook/Instagram) or Google Ads through AdChat.
---

# Cut wasted spend

Goal: stop paying for things that have had a fair chance and failed, and keep
everything that's still learning or working. Pausing too early wastes money too.

## What counts as waste

Over the last 14 to 30 days:

- **Spent 2–3× the target cost per result with zero results.** Target from
  `get_account_briefing`; if there is none, ask.
- **Cost per result 2× target or worse** with enough spend to judge.
- **Audiences or placements** (Meta breakdowns) eating budget with no results.
- **Search terms** that cost money and will never convert (Google; see
  `google-search-cleanup`).
- **Duplicate effort**: two ad sets or campaigns targeting the same people and bidding
  against each other.

Not waste: anything under ~3 days old, in learning, or with too little spend to judge.

## Steps

1. **Context.** `get_account_briefing` for targets and notes ("don't touch brand
   campaign").
2. **Meta.**
   - `meta_insights` at `level: ad` for the last 14–30 days, sorted by spend. Flag ads
     with spend ≥ 2× target and no results, or cost per result ≥ 2× target.
   - Same at `level: adset`.
   - `meta_insights` with `breakdowns: ["publisher_platform", "platform_position"]`
     and `["age"]` on the biggest spender for segments with spend and no results.
3. **Google.**
   - `google_list` with `level: campaigns`, then `level: keywords` (cost, conversions,
     quality score).
   - `google_list` with `level: search_terms`: costly searches with no conversions.
   - `google_get_insights` with `breakdown: network` and `device` for leaks such as
     display or search partners.
4. **Check alerts.** `get_alerts` may already list waste with suggested changes.
5. **Propose.** One table per platform, biggest saving first.
6. **Apply after the user picks.** Pausing and lowering budgets need no Yes on
   autopilot, but always show the list first and let the user remove items; on review
   mode ask Yes/No. See `safe-changes`.
   - Meta pause: `meta_update` with `status: PAUSED`, or `meta_bulk_update` with
     `preview: true` first for several.
   - Meta budget down: `meta_update` with the new `daily_budget` in cents.
   - Google pause: `google_set_status` with `status: PAUSED`.
   - Google negatives: `google_add_negative_keywords` (`google_add_keywords` adds paid keywords).
   - Google budget down: `google_update_campaign_budget` in currency units.

## What to show the user

```
Waste found · last 30 days · target CPA $25 · about $640/month

Meta
Ad / ad set                     Spend   Results  Why                      Action
Static B (Broad US)             $210    0        8× target, no results    Pause ad
Audience Network (all ad sets)  $95     0        no results               Exclude placement*
Google
"free jacket pattern"           $48     0        no buying intent         Negative (phrase)
Display expansion, Campaign A   $130    1        $130 per conversion      Discuss
```
\*Placement edits change targeting on an ad set: name the ad set and the change.

## Guardrails

- Pausing is reversible; deleting is not. Prefer pause.
- Don't pause the last active ad in an ad set without saying the ad set will stop.
- Editing an ACTIVE ad set's targeting needs a Yes on autopilot.
- Meta budgets in cents; Google in currency units.
- Be honest about uncertainty. "Probably waste" items go in a "discuss" column.

## Example prompt

> "Find where I'm wasting money on Facebook and Google this month and pause the worst."

Briefing → Meta ad and ad set insights → breakdowns → Google campaigns, keywords,
search terms → table → user picks → pauses and negatives → one-line summary per change.
