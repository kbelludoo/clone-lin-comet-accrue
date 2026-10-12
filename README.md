# clone-lin-comet-accrue

Experimental i64-scale LIN clone of [compound-finance/comet](https://github.com/compound-finance/comet) `contracts/CometWithExtendedAssetList.sol` (`accruedInterestIndices` / `getSupplyRate` / `getBorrowRate` / `mulFactor`) plus `CometCore.sol` constants (`SECONDS_PER_YEAR=31536000`, `FACTOR_SCALE=1e18`, `BASE_INDEX_SCALE=1e15`).

- **LIN clone:** [`src/lin_comet_accrue.lin`](src/lin_comet_accrue.lin)
- **Proofs (results only) live in lin-open:** https://github.com/kbelludoo/lin-open
- **Event receipt:** `docs/events/EVENT_COMET_ACCRUE_CLONE_LIN.rulel` in lin-open

This repository is the standalone LIN copy. It is **not** Compound III, **not** a Comet / USDC market, and does **not** claim Solidity uint256 / mainnet / PriceFeed parity. It is **not** the presentValue/principalValue kernel (`clone-lin-comet-pv`).

Upstream was BUSL-1.1 with Change Date 2025-12-31; the Change License is GPL-2.0-or-later (this run is 2026-10-12). This LIN kernel is an original uint64-scale implementation of the published per-second index update, not a verbatim Solidity dump.

Pinned upstream: commit `f766f51583c23acc33b2a7824654ef2029a96804`.

Canonical goldens (uint64-scale, 128-bit product):

- `mulFactor(1e18, 5e17)` = **5e17**
- `utilization(500, 1000)` = **5e17**
- `accrue(1e15, rate, 0)` = **1e15** (no elapsed time)
- `delta(1e15, 1, 1000)` = **1** (`index * (rate*dt) / 1e18`)
- `delta_twostep(1e15, 1, 1000)` = **0** (rejected `floor(index*rate/1e18)*dt` path)
- 1% wad per-second = `1e16 / 31536000` = **317097919**
- 1% wad per-block (Compound V2 JumpRate) = `1e16 / 2102400` = **4756468797**
- genesis index, 1% APY, 365-day year, III per-second delta = **9999999973584**
- same book with V2 per-block rate fed in as III per-second = **149999999982192** (~**15x** over-accrual)
- `rate_time_ok(2^32, 2^32)` = **0** (product does not fit uint64; Solidity uint256 would succeed)
- `1e18 * 1e18 / 1` fail-closes to **0**
