# DNX Swap WebApp — My Work (Beta)

A fast, operator-focused trading web app I built after running into a real execution problem: swapping via typical DEX UIs (e.g., Uniswap-style flows) was too slow and “laggy” for active trading. I needed a dedicated interface that makes the critical state visible at a glance, minimizes click/typing overhead, and includes operational tools for real on-chain conditions (nonce desync, stuck txs, gas volatility, and MEV considerations).

> Status: **beta / work in progress**. The goal of this repository is to showcase the product and engineering work. The full source code is kept private.

---

## The problem I had to solve

When executing swaps manually, seconds matter:

- Standard DEX UIs often require multiple screens and confirmations.
- Gas and base fee changes can quickly invalidate assumptions.
- Stuck transactions and nonce mismatches are common in active usage.
- MEV threats and private relay/builder operational reliability matter.

I wanted a single page that:

- **Shows everything important** (balances, prices, block/base fee) while trading.
- **Executes common actions immediately** via preset buttons.
- Provides **safety and recovery tooling** when something goes wrong.

---

## Solution overview

I built a lightweight web app with:

- A **FastAPI** backend serving a single-page UI + JSON APIs.
- An async **Web3 execution layer** for swaps and transfers.
- A **config-driven gas system** (range-based priority fee + base fee multiplier).
- Operational tooling (cancel, nonce inspection, stuck-tx checks).
- MEV-aware builder connectivity monitoring (keepalive + summary logs).

---

## UI snapshot

![DNX Trading UI](assets/ui.png)

---

## Key features (detailed)

### 1) Trading workflow optimized for speed

- **Sell DNX → USDC** card
  - Preset buttons for common sizes
  - “Config Gas” vs “Custom Gas” modes
- **Buy DNX ← USDC** card
  - Preset buttons for common sizes
  - **ALL** button to buy using the full USDC balance (rounded down to avoid dust/precision failures)

### 2) Config-driven gas strategy (range-based)

The app uses a JSON config to select gas parameters based on trade size.

- **Priority fee (gwei)** chosen by amount range
- **Base fee multiplier** chosen by amount range
- A **default gas limit** used as a baseline

This avoids “one-size-fits-all” gas settings and makes behavior predictable.

### 3) Real-time visibility

- Live wallet balances (ETH / DNX / USDC)
- Price display (e.g., ETHUSDT and DNXUSDT)
- Block number + base fee tracking
- Log window for actions + system events

### 4) Safety & operations tooling (separate section)

This is intentionally separated in the UI from trading buttons:

- **Cancel current swap**
- **Cancel all pending transactions**
- **Nonce inspection tool** (diagnostics for nonce sync / pending txs)
- **Stuck transaction detector** (identify pending/stuck states and show details)

### 5) MEV-aware operations

- Persistent connections and keepalive to selected builders/relays
- Periodic summary logs (interval configurable)
- Goal: improve reliability of the execution pipeline under real conditions

---

## How I would adapt this to other tokens / pairs

This system is reusable.

I can build similar apps for other ERC-20 tokens or pairs by:

- Updating token addresses/decimals and pair routing
- Adjusting preset buttons per token liquidity/typical sizing
- Adding/rewriting gas range tables for different volatility regimes
- Extending safety checks (slippage policies, profit checks, circuit breakers)
- Adding additional on-chain actions (approvals, multi-hop routes, bridging hooks)

---

## What I’d improve next (roadmap)

- Packaging (Docker, reproducible env)
- Automated tests for gas range selection and tx-building invariants
- Better configuration UX (in-app config editor + validation)
- Observability upgrades (structured logs, metrics)
- Additional MEV execution options (private tx routing strategies)

---

## Notes for reviewers

- This is a **real trading tool** built from a practical need.
- The focus is on **speed, clarity, and operational reliability**.
- Full source is private; I can share code or a sanitized demo version on request.
