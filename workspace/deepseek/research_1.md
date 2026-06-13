> Produced by: researcher.md (angle: AMM Price Mechanics, called by deep_digest.md)
> Topic: Arbitrage on DEXs

## AMM Price Mechanics: Arbitrage on DEXs
- Constant-product AMMs (x*y=k) shift the spot price with every trade, creating immediate post-trade discrepancies versus other pools.
- Price impact grows non-linearly with trade size; large swaps move the curve steeply, widening inter-pool gaps.
- Concentrated liquidity AMMs (e.g. Uniswap v3) expose tick boundaries where price can gap discontinuously when liquidity runs out.
- Stale oracle prices in AMM pools lag real-time market moves, making the pool temporarily mispriced and arbitrageable.
- Fee tiers create price bands; a pool's spot price must deviate beyond its fee tier before arbitrage becomes profitable.
