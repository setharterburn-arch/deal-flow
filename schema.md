# deal-flow schema

## `deals/latest.json`

```jsonc
{
  "updated_at": "2026-09-17T09:15:00-05:00",   // last merge, America/Chicago
  "timezone": "America/Chicago",
  "watches": {
    "iphone_apple":   { "updated_at": "...", "confident_flips": [Item], "needs_eyes": [Item] },
    "pc_console":     { "updated_at": "...", "confident_flips": [Item], "needs_eyes": [Item] },
    "samsung_pixel":  { "updated_at": "...", "confident_flips": [Item], "needs_eyes": [Item] },
    "rugged":         { "updated_at": "...", "confident_flips": [Item], "needs_eyes": [Item] }
  }
}
```

### Item (confident_flips)

| field        | type   | meaning |
|--------------|--------|---------|
| `listing_id` | string | Facebook Marketplace listing id |
| `title`      | string | listing title as posted |
| `price`      | number | asking price in USD |
| `buy_price`  | number | wholesale buy price in USD (what the buyer pays Seth) |
| `buyer`      | string | `BestDealPhones` or `Fixit` (or `GameStop` / `eBay` for pc_console) |
| `margin`     | number | `buy_price` (or expected net) minus `price`, USD |
| `condition`  | string | as identified, e.g. `B-good`, `sealed`, `unknown` |
| `location`   | string | city/state as posted |
| `distance_mi`| number | miles from Glasgow, KY 42141 |
| `product_url`| string | clickable Marketplace URL — always present |
| `found_at`   | string | ISO timestamp when the scan first saw it |

### Item (needs_eyes)

Same fields, plus:

| field               | type   | meaning |
|---------------------|--------|---------|
| `best_case_margin`  | number | margin if the uncertainty resolves in Seth's favor |
| `question`          | string | the ONE question to ask the seller |

`margin` is omitted on needs_eyes items; `best_case_margin` is omitted on confident flips.

## `deals/decisions.json`

```jsonc
{
  "decisions": [
    {
      "listing_id": "123",
      "verdict": "buy",              // buy | pass | asked | waiting_reply | bought
                                     // bots may only use: suggested_buy | suggested_pass
      "decided_at": "2026-09-17T10:00:00-05:00",
      "decided_by": "seth",          // seth | grok-bot | judith
      "note": "seller confirmed 256GB unlocked"
    }
  ]
}
```

Rules:

- Only `seth` writes `buy` / `pass` / `asked` / `waiting_reply` / `bought`.
- Bots write `suggested_buy` / `suggested_pass` with a `note` explaining why.
- A suggestion is never authorization. Purchases, seller messages, and pickups
  need Seth's explicit word, every time.
- `listing_id` values are unique — update the existing entry instead of appending
  a duplicate.
