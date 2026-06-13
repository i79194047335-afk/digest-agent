> Routed by: ORCHESTRATOR.md → deep_digest.md → researcher.md × 3 → writer.md
> Topic: Arbitrage on DEXs

---

## AMM Mechanics & Price Formation

Automated market makers price assets through reserve ratios rather than order books. The constant-product formula (x·y=k) means every trade shifts reserves and moves quoted price; a pool with imbalanced reserves quotes differently than one in equilibrium. Two pools holding the same pair diverge when their reserves develop different ratios through asymmetric order flow — this divergence is the fundamental source of DEX arbitrage. Price impact scales nonlinearly: doubling a swap more than doubles slippage, so larger arbitrage trades consume progressively more of the available spread. Uniswap v3's concentrated liquidity model adds complexity: liquidity is distributed across discrete price ranges, creating sharp price discontinuities when a trade exhausts an active range boundary. DEX prices update only at block finality — roughly twelve seconds on Ethereum — while centralized exchanges reprice continuously. This structural lag means DEX prices trail their CEX equivalents by seconds, opening persistent, predictable gaps between every block.

---

## Arbitrage Strategies & Execution Infrastructure

Three strategy types capture DEX price gaps, each exploiting a different source of dislocation. DEX-DEX arbitrage is the purest form: buy the underpriced token on one pool and sell on the overpriced pool in a single atomic transaction, so the trade either succeeds in full or reverts with no position. CEX-DEX arbitrage targets the lag between an off-chain price move and the slower on-chain update, requiring low-latency infrastructure to win the race to the next block. Cross-chain arbitrage extends the search to price gaps across L1 and L2 networks, using bridges or intent-based solver networks matching buyers and sellers across chains. Flash loans remove capital requirements from the first two strategies: a searcher borrows millions uncollateralized, executes the full round-trip, and repays in one transaction. To avoid mempool exposure and pay-to-revert losses, searchers route bundles through MEV relays — Flashbots, MEV-Boost — submitting privately and landing only if profitable.

---

## Risk, Competition & Profitability Economics

DEX arbitrage economics have compressed dramatically as searcher competition has intensified. On heavily traded Ethereum pairs, margins on pure arbitrage are now near zero: dozens of bots submit competing bundles per block, and only the highest bidder lands. Gas costs for failed attempts are a structural tax — bots in the public mempool may revert on 40–80% of attempts, burning capital on transactions that produce nothing. Reversion risk is also a function of competition: when two searchers simultaneously spot the same opportunity, exactly one wins and the other pays gas for a failed transaction. Sandwich attacks introduce an additional hazard; a searcher executing a large arb can become a sandwiching target if their trade is visible before inclusion. By 2026, profitability has migrated toward thinner markets: newly launched pools with temporary price dislocation, long-tail token pairs with fewer bots, and cross-chain routes where latency reduces the field of viable competitors.
