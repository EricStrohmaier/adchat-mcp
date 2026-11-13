---
name: diagnose-performance
description: Find out why Meta or Google Ads results changed. Use when the user says results dropped, CPA or cost per result went up, ROAS fell, spend stopped, "what happened to my ads", "why is it worse this week", or asks for a health check of a Meta (Facebook/Instagram) or Google Ads account through AdChat.
---

# Diagnose performance

Goal: find the one or two real reasons results changed, backed by numbers, and suggest
the smallest fix. Most "my ads stopped working" cases are one of: delivery stopped,
tracking broke, the creative tired out, the audience got too small or too expensive,
or the comparison is unfair (a holiday, a sale, too few days of data).

## Steps

1. **Context first.** `get_account_briefing` for the account's goals, target CPA or
   ROAS and notes. If the user has no target, ask what a good result costs them.
2. **Pick the account.** `meta_list_accounts` (or `google_get_accounts`). Work in one
   account at a time.
3. **Confirm the change is real.** Compare two equal windows, for example the last 7
   days against the 7 before.
   - Meta: `meta_insights` at `level: account` with `time_increment: 1` (daily) over 14
     to 28 days.
   - Google: `google_get_insights` with `breakdown: date`.
   Look at spend, results, cost per result, CTR, CPM (Meta) or CPC (Google). Small
   numbers (under ~20 conversions a week) swing a lot: say so.
4. **Find where it moved.** Drill down one level at a time, sorted by spend:
   - Meta: `meta_insights` at `level: campaign`, then `adset`, then `ad`.
   - Google: `google_list` with `level: campaigns`, then `ad_groups`, `keywords`.
   Usually one campaign or ad set explains most of the change.
5. **Check the usual causes**, in this order:
   - **Delivery:** `meta_diagnose` with `kind: delivery_issues` (disapproved ads,
     learning limited, budget or billing problems). Google: `google_list` with
     `level: ads` for disapprovals and status.
   - **Tracking:** `meta_diagnose` with `kind: pixel_health`. A sudden drop to near
     zero conversions with steady clicks is usually tracking, not ads.
   - **Creative fatigue:** frequency above ~3 to 4 in prospecting with CTR falling
     week over week. See the `creative-analysis` skill.
   - **Cost pressure:** CPM up with CTR flat means the auction got pricier
     (seasonality, competition), not that the ads got worse.
   - **Breakdowns:** `meta_insights` with `breakdowns` such as `age`,
     `publisher_platform`, `platform_position` or `country` to spot one segment
     eating budget. Google: `google_get_insights` with `breakdown: device` or `network`.
   - **Recent edits:** `get_change_history` (Meta) or `google_get_change_history`.
     A budget jump or targeting edit often resets learning.
6. **Check money, if connected.** `get_true_profit` compares Meta spend with real
   revenue; it can show that a "worse" ROAS is actually fine, or the reverse.
7. **Open alerts.** `get_alerts` may already describe the problem and a suggested fix.

## What to show the user

- One sentence answer first: "Cost per purchase rose from $18 to $27, mostly because
  ad set *Broad US* lost its best ad to a disapproval on Tuesday."
- A small table: the before/after window for the 3 to 5 numbers that matter.
- The cause, with the evidence, and how sure you are.
- One to three fixes, smallest first. Name what each will change.

## Guardrails

- Read-only until the user picks a fix. Fixes go through the `safe-changes` flow.
- Don't call a change a trend on fewer than 3 days of data or a handful of conversions.
- Meta and Google count conversions differently; don't add them up as if equal.
- Meta budgets are in cents; insights spend is in currency units. Google amounts are
  in currency units.
- Don't recommend raising budget to fix a drop you haven't explained.

## Example prompt

> "My Facebook cost per lead doubled this week. What happened?"

Briefing → account-level daily insights for 14 days → campaign and ad set level →
`meta_diagnose` delivery issues → frequency and CTR by ad → change history. Answer:
"One ad set's frequency hit 5.2 and CTR halved; the others are steady. Pause its two
oldest ads and add fresh creative. Want me to pause them?"
