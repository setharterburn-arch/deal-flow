# Hooking Grok Bot (Cursor) into deal-flow

## One-time setup (on Seth's Mac)

1. Clone the repo (GitHub auth is already in the macOS keychain):
   ```
   gh repo clone setharterburn-arch/deal-flow ~/deal-flow
   ```
2. In Cursor: **File → Open Folder** → `~/deal-flow`.
3. Keep it fresh — pull on a schedule so Grok Bot always sees the latest scans.
   Add a launchd job (every 15 minutes):
   ```
   mkdir -p ~/Library/LaunchAgents
   ```
   Save this as `~/Library/LaunchAgents/com.seth.dealflow-pull.plist`:
   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
   <plist version="1.0">
   <dict>
     <key>Label</key><string>com.seth.dealflow-pull</string>
     <key>ProgramArguments</key>
     <array><string>/usr/bin/git</string><string>-C</string><string>/Users/seth/deal-flow</string><string>pull</string><string>--ff-only</string></array>
     <key>StartInterval</key><integer>900</integer>
   </dict>
   </plist>
   ```
   Then: `launchctl load ~/Library/LaunchAgents/com.seth.dealflow-pull.plist`
   (Replace `/Users/seth` with the real home path if different.)

## The prompt to give Grok Bot

Paste this into Cursor's chat (or save as `.cursor/rules/deal-flow.md` in the repo
so it applies automatically):

> You are wired into my deal-flow repo. `deals/latest.json` holds current
> Facebook Marketplace flip opportunities from four scheduled scans
> (iPhone/Apple, GPU/prebuilt/console, Samsung/Pixel, rugged phones).
> Field definitions are in `schema.md`. Every item has a clickable
> `product_url`, margin math, and — for uncertain items — the one question
> to ask the seller.
>
> Rules:
> - Re-read `deals/latest.json` whenever I ask about deals; it refreshes
>   roughly every 15 minutes via git pull.
> - Never message a seller, never commit to a purchase or pickup. You can
>   only write `suggested_buy` / `suggested_pass` entries to
>   `deals/decisions.json` (schema in `schema.md`) with your reasoning.
>   My verdict is final.
> - Write your working notes to `analysis/YYYY-MM-DD.md`, not into chat.
> - Prices are gross margins before fees/shipping/deductions. Flag anything
>   that could erase the margin (fakes, mining history, missing accessories).
> - Cite `listing_id` for every claim about a specific deal.

## What Grok Bot can do with it

- Rank and triage the day's opportunities; batch nearby pickups into one route.
- Track outcomes: when I tell it I bought/passed/asked, it records the verdict
  in `deals/decisions.json`, building a history that sharpens future calls.
- Draft the one-question seller messages (I copy/paste and send them myself).
- Spot patterns: which models, price bands, and cities actually convert.

## Notes

- Judith's scans merge new results after every run; `updated_at` per watch
  shows how fresh each section is.
- If a pull ever conflicts (both sides edited `decisions.json`), keep both
  entries and deduplicate by `listing_id` — newest `decided_at` wins.
