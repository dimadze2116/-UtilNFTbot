# UtilNFTbot (@UtilNFTbot)
### Finding real value among NFTs on NEAR

---

## The Problem

The NEAR NFT market is fragmented: some collections carry real utility (income, access, discounts), while others are just artwork. Most holders don't know:

- Which of their NFTs actually do anything beyond visual status
- Where exactly that utility applies (which DAO, staking pool, game, airdrop)
- Whether the project behind it is even still alive, or the utility technically exists but has nowhere left to be redeemed

Information is scattered across project blogs, Discord channels, and posts on X — there's no single place to check.

## The Solution

**ClaudeUtil** takes a NEAR wallet address, finds every NFT collection it holds, cross-references them against a curated utility registry, and returns results **ranked by usefulness**, showing:

- where the utility applies (specific contract, DAO, platform);
- what exactly it gives the holder;
- whether the project is still alive — verified automatically on-chain, not just claimed.

## How It Works

```
Wallet address
      │
      ▼
Data collection (FastNEAR API → list of held NFT contracts)
      │
      ▼
Match against the utility registry (curated database)
      │
      ▼
Live contract activity check (NearBlocks API)
      │
      ▼
Ranking by score (category weight × activity)
      │
      ▼
Output: Telegram bot + Mini App
```

## Scoring Model

Each utility belongs to one of the following categories, each with its own weight:

| Category | Example | Weight |
|---|---|---|
| Direct income | Staking, token airdrop | High |
| Product/DAO access | Voting rights, whitelist | High |
| Financial perk | Discount, DeFi boost | Medium-high |
| Game asset | Used inside a live game | Medium |
| Community/status | Discord role, events | Medium-low |
| Cosmetic only | Just artwork | Low |

The final score is reduced if the contract is inactive (checked automatically via its most recent on-chain transaction) — so a dead project's non-functioning mechanic doesn't score the same as a living one.

## Interfaces

**Telegram bot** — send a NEAR address as text, get the ranked list right in the chat. Fast entry point, nothing to install.

**Telegram Mini App** — the same result as a full UI: sortable cards with categories and a live activity badge for each utility.

## Current Status

- Fully working Node.js + TypeScript scaffold
- Utility registry: 6 verified real NEAR collections (including entries confirmed directly on the NearBlocks block explorer), each transparently labeled with its data source and confidence level
- Automated activity checking via the NearBlocks API, layered as a best-effort check on top of the curated data
- Ready-to-run launch scripts (`startbot.bat` / `restartbot.bat`) for local deployment

## Roadmap

- Expand the utility registry (community curation + moderation)
- Caching and reduced calls to external APIs
- Direct NEAR Wallet Selector integration in the Mini App (no manual address entry)
- Public web dashboard ranking utility across the whole NEAR ecosystem
