# Near NFT Utility (@UtilNFTbot)

### Finding real value among NFTs on NEAR

---

## The Problem

The NEAR NFT market is fragmented: some collections carry real utility (income, access, discounts), while others are just artwork. Most holders don't know:

- which of their NFTs actually do anything beyond visual status;
- where exactly that utility applies;
- whether the project behind it is even still alive, or the utility technically exists but has nowhere left to be redeemed.

Information is scattered across project blogs, Discord, and posts on X — there's no single place to check.

## The Solution

Given a NEAR wallet address, the service finds every NFT collection it holds, cross-references them against a curated utility registry, and returns results **ranked by usefulness**, showing: where the utility applies, what exactly it gives the holder, and whether the project is still alive — verified automatically on-chain, not just claimed.

## How It Works

```
Wallet address (bot or Mini App)
      │
      ▼
FastNEAR API → list of held NFT contracts
      │
      ▼
Match against the curated utility registry (data/utilities.json)
      │
      ▼
Live contract activity check (NearBlocks API, 5s timeout, 1h cache)
      │
      ▼
Score = category weight × activity → ranked descending
      │
      ▼
Output: Telegram bot (text) + Mini App (cards), in RU/EN
```

## Scoring Model

| Category | Example | Weight |
|---|---|---|
| Direct income | Staking, token airdrop | High |
| Product/DAO access | Voting rights, whitelist | High |
| Financial perk | Discount, DeFi boost | Medium-high |
| Game asset | Used inside a live game | Medium |
| Community/status | Discord role, events | Medium-low |
| Cosmetic only | Just artwork | Low |

The score is reduced if the contract is inactive (checked automatically via its most recent on-chain transaction).

## Interfaces

- **Telegram bot** — send an address as text, get the ranked list right in the chat. Language choice (🇷🇺/🇬🇧) persists across sessions.
- **Telegram Mini App** — the same result as a full UI: sortable cards, a language switcher, and a dark "ledger" theme with score seals.

## Tech Stack & Architecture

- Node.js + TypeScript, grammY (bot), Express (API + Mini App static files)
- **Production**: single process on Render (free tier has no separate background worker) — the bot runs via **webhook**, not long polling
- Local development is unchanged — bot and server run as two separate processes via long polling + ngrok
- Resilience: timeouts on every external request, `onTimeout: "return"` instead of crashing the process, global handlers for uncaught exceptions
- Keep-alive via an external ping to `/healthz` every 10 minutes (cron-job.org) so the free instance doesn't spin down

## Current Status (live in production)

- Repository on GitHub, deployed on Render — public address, available 24/7
- Registry: **6 verified real** NEAR collections (DoubleDog, USMen, DataBabies, NEAR Legion, Black Dragon, Armor/Perk), each transparently labeled with its source and confidence level
- Fully bilingual interface (RU/EN) — both the bot and the Mini App

## Roadmap

**Near-term:**
- Keep expanding the utility registry (next batch via HOT Craft/HOT Protocol or from submitted contract IDs)
- Uptime monitoring on top of the keep-alive ping (alerts if the service is actually down, not just asleep)

**Mid-term:**
- Move `utilities.json` to a real database (Postgres) with a submission/moderation form for community entries
- Cache FastNEAR responses too (currently only the activity check is cached, not the wallet's NFT list itself)
- NEAR Wallet Selector in the Mini App — connect a wallet directly instead of typing an address

**Long-term:**
- A public web dashboard ranking utility across the whole NEAR ecosystem (not just for a specific wallet's holdings)
- Move to a paid hosting tier if the bot gains real traction (the free tier isn't built for 24/7 use without wake-up delays)
