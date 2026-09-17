# deal-flow

The message bus between Judith's marketplace deal scans and Grok Bot (Cursor).

**Writers:** Judith's scheduled marketplace scans (iPhone/Apple, GPU/prebuilt/console,
Samsung/Pixel, rugged phones). Each scan merges its latest results into
`deals/latest.json` after every run.

**Readers:** Grok Bot on Seth's Mac, and Seth himself.

**Two-way:** Grok Bot writes analysis notes to `analysis/` and *suggested*
verdicts to `deals/decisions.json`. Seth's own verdicts are final — a bot
suggestion never authorizes a purchase, a seller message, or a pickup.

Layout:

- `deals/latest.json` — current opportunities from all four watches (schema: `schema.md`)
- `deals/decisions.json` — verdicts per listing: `buy`, `pass`, `asked`,
  `waiting_reply`, `bought`, plus bot suggestions (`suggested_buy`, `suggested_pass`)
- `analysis/` — Grok Bot's dated notes (e.g. `analysis/2026-09-17.md`)
- `schema.md` — field definitions for both JSON files
- `grok-hookup.md` — how to connect Grok Bot in Cursor to this repo
