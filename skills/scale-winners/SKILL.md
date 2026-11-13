---
name: scale-winners
description: Grow spend on Meta or Google Ads campaigns, ad sets or ads that are clearly working, without breaking them. Use when the user asks to scale, increase budget, "put more money behind what works", find winners, or move budget from losers to winners in Meta (Facebook/Instagram) or Google Ads through AdChat.
---

# Scale winners

Goal: put more money behind what is reliably beating the user's target, in steps small
enough that performance holds. Scaling too fast is the most common way to turn a winner
into a loser.

## What counts as a winner

All of these, over the last 7 to 14 days:

- Cost per result at or below target (or ROAS at or above target). Get the target from
  `get_account_briefing`; if there is none, ask.
- Enough volume to trust: roughly 15+ conversions in the window.
- Stable: not one lucky day. Check daily numbers.
- Room to grow: on Meta, frequency under ~3 for prospecting; on Google, impression
  share lost to budget (not to rank).

## Steps

1. **Context.** `get_account_briefing` (targets, monthly budget limits) and the user's
   spending rules if they mention them.
2. **Find candidates.**
   - Meta: `meta_insights` at `level: adset` (or `campaign` with campaign budgets),
     `date_preset: last_14d`, then `time_increment: 1` on the top ones for stability.
   - Google: `google_list` with `level: campaigns` for the last 14 days. For
     budget-limited campaigns, `google_run_gaql` with
     `metrics.search_budget_lost_impression_share` (money in micros there).
3. **Check profit.** `get_true_profit` if revenue is connected: scale on real profit,
   not platform-reported ROAS alone.
4. **Pick the move.**
   - **Raise budget 15–20%** on the winner. Wait 3 to 4 days before the next step.
   - **Move budget** from a clear loser to the winner when the total must stay the same
     (pair this with `cut-wasted-spend`).
   - **Duplicate** a winning Meta ad set into a new audience with `meta_duplicate`
     (`level: adset`); the copy starts PAUSED.
5. **Propose.** Show a table: object, current budget, new budget, why (numbers).
6. **Apply after Yes.** Budget increases always need the user's Yes (except trusted
   mode). Meta: `meta_update` with `level: adset` or `campaign` and the new
   `daily_budget` in cents; several at once: `meta_bulk_update` with
   `budget_change_percent` and `preview: true` first. Google:
   `google_update_campaign_budget` with the new `daily_budget` in currency units.
7. **Follow up.** Suggest checking again in 3 to 4 days before another step.

## What to show the user

```
Scale proposal · Acme Shop · last 14 days · target CPA $25

Ad set               Spend   Purch.  CPA     Freq  Budget now → new
Lookalike 1% US      $812    52      $15.60  1.9   $60.00 → $72.00 (+20%)
Retargeting 30d      $301    21      $14.30  3.6   hold (frequency high)
```

## Guardrails

- No more than +20% per object per 3 days unless the user insists, and say the risk.
  The user's spending rules may cap it anyway; a refusal comes back with reasons.
- Meta budgets in cents (`7200` = $72.00). Google budgets in currency units (`72.00`).
- A Google shared budget changes every campaign that uses it: say so.
- Don't scale an ad set in Meta's learning phase or with delivery issues
  (`meta_diagnose`, `kind: delivery_issues`).
- Never raise budget to "fix" a drop. Diagnose first (`diagnose-performance`).

## Example prompt

> "Which of my Facebook ad sets should get more budget? Go ahead with the best one."

Briefing → ad set insights 14 days → daily check on the top 3 → true profit → proposal
table → "Raise *Lookalike 1% US* from $60 to $72 a day?" → Yes → `meta_update`
`daily_budget: 7200`.
