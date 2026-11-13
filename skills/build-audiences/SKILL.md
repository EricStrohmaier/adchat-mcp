---
name: build-audiences
description: Create Meta (Facebook/Instagram) audiences through AdChat: website visitors and purchasers (pixel), page and Instagram engagers, lookalikes, interest targeting, and audience size estimates. Use when the user asks for retargeting, a lookalike, a custom audience, interest targeting, exclusions, or "who should I target".
---

# Build audiences

Goal: give the user the few audiences that matter for their stage, named clearly, and
used correctly (including exclusions). For most small accounts with a working pixel:
broad prospecting, a lookalike of buyers, and a retargeting audience are enough.

## Pick the right audience

| Need | Audience | How |
|---|---|---|
| Bring back site visitors | Website audience, 30 or 180 days | `meta_audience`, `action: create_website` |
| Bring back buyers / exclude buyers | Website audience on the Purchase event | `create_website` with `include_events` |
| Bring back people who engaged | Page or Instagram engagers | `meta_audience`, `action: create_engagement` |
| Find people like buyers | Lookalike 1% of purchasers | `meta_audience`, `action: create_lookalike` |
| Cold, no data yet | Broad or interests | `meta_search_targeting`, `kind: interests` |

## Steps

1. **Context.** `get_account_briefing`. See what exists:
   `meta_list` with `level: custom_audiences`. Don't build duplicates.
2. **Pixel.** `meta_list` with `level: pixels` and `meta_diagnose` with
   `kind: pixel_health`. Website audiences need a pixel that is firing the events.
3. **Create** (one at a time, tell the user each one):
   - Website: `meta_audience` with `action: create_website`, `pixel_id`, `name`,
     `retention_days` (max 180), optional `url_contains`, `include_events`,
     `exclude_events`.
   - Engagement: `action: create_engagement`, `source_type` (`page` or `instagram`),
     `source_id` (from `meta_get_account` with `include: ["pages"]`), `event`,
     `retention_days`.
   - Lookalike: `action: create_lookalike`, `origin_audience_id` (a source with at
     least ~100 people in the country, ideally 1,000+), `country`, `ratio` (0.01 = 1%).
4. **Interests (cold).** `meta_search_targeting` with `kind: interests` and
   `kind: interest_suggestions`. Keep 3–6 related interests in one group.
5. **Size check.** `meta_estimate_audience` with the full `targeting`. Retargeting
   under ~1,000 people barely delivers; prospecting under ~500k is narrow.
6. **Use it.** Audiences do nothing until an ad set uses them. Offer to add them to a
   new PAUSED ad set (`launch-campaign`) or to an existing one with `meta_update`
   (`level: adset`, new `targeting`); editing an ACTIVE ad set needs a Yes.

## What to show the user

```
Created (not used by any ad set yet)
- Website visitors 30d (excl. purchasers)   · pixel Acme  · fills over ~24h
- Purchasers 180d                           · source for lookalike
- Lookalike 1% US – Purchasers 180d         · ~2.3M people
Suggested use: Prospecting = Lookalike 1% US, exclude Purchasers 180d.
Retargeting = Website visitors 30d, exclude Purchasers 180d.
```

## Guardrails

- Creating audiences needs no Yes on autopilot (it doesn't spend), but say what you
  created. Deleting an audience (`action: delete`) can't be undone: always ask first.
- Name audiences by source, window and country: `Purchasers 180d`, `LAL 1% US – Purchasers 180d`.
- New audiences take hours to fill; lookalikes can take up to a day.
- Special ad categories (housing, credit, employment, politics) restrict targeting and
  lookalikes. Check the campaign before suggesting.
- Always exclude recent purchasers from prospecting and retargeting unless the user
  sells repeat purchases on purpose.

## Example prompt

> "Set up retargeting for people who visited my site but didn't buy, and a lookalike of
> my customers in Canada."

Briefing → existing audiences → pixel health → website visitors 30d excluding
Purchase → Purchasers 180d → lookalike 1% CA → size estimate → suggested use → offer
to add them to ad sets.
