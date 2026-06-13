> Produced by: researcher.md (angle: AMM Mechanics & Price Formation, called by deep_digest.md)
> Topic: Arbitrage on DEXs

## AMM Mechanics & Price Formation: Arbitrage on DEXs
- Constant-product AMMs price assets via x·y=k, causing price to drift as one reserve depletes relative to the other.
- Price impact scales nonlinearly with trade size: doubling the swap more than doubles slippage in a constant-product pool.
- Concentrated liquidity pools (Uniswap v3) let LPs define price ranges, creating discontinuous liquidity that amplifies local price gaps.
- Two pools holding the same pair diverge in price whenever their reserve ratios differ due to asymmetric order flow.
- Price discovery lags on DEXs relative to CEXs because on-chain state updates only at block finality, not continuously.
