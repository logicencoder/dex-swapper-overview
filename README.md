# DNX Swap WebApp — Technical Overview (Experimental)

A fast, operator-focused trading web application I built after running into a real execution problem: swapping via typical DEX UIs (e.g., Uniswap-style flows) was too slow and "laggy" for active trading. I needed a dedicated interface that makes the critical state visible at a glance, minimizes click/typing overhead, and includes operational tools for real on-chain conditions (nonce desync, stuck txs, gas volatility, and MEV considerations).

> Status: **experimental / semi-working / needs further development**. The goal of this repository is to showcase the product concept and engineering approach. Many components are experimental and not fully implemented. The full source code is kept private.

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

I built a lightweight web application with:

- A **FastAPI** backend serving a single-page UI + JSON APIs
- An async **Web3 execution layer** for swaps and transfers
- A **config-driven gas system** (range-based priority fee + base fee multiplier)
- **Real-time price calculations** from both exchange feeds (MEXC) and on-chain pool data
- Operational tooling (cancel, nonce inspection, stuck-tx checks)
- MEV-aware builder connectivity monitoring (keepalive + summary logs)

> **Note:** Many features are experimental and require further development to be fully functional.

---

## UI snapshot

![DNX Trading UI](assets/ui.png)

---

## Key features (detailed)

### 1) Trading workflow optimized for speed

> **Status:** Experimental implementation, needs further development

- **Sell DNX → USDC** card
  - Preset buttons for common sizes
  - "Config Gas" vs "Custom Gas" modes
- **Buy DNX ← USDC** card
  - Preset buttons for common sizes
  - **ALL** button to buy using the full USDC balance (rounded down to avoid dust/precision failures)

### 2) Config-driven gas strategy (range-based)

> **Status:** Implemented but experimental, needs testing

The app uses a JSON config to select gas parameters based on trade size.

- **Priority fee (gwei)** chosen by amount range
- **Base fee multiplier** chosen by amount range
- A **default gas limit** used as a baseline

This avoids "one-size-fits-all" gas settings and makes behavior predictable.

### 3) Real-time visibility

> **Status:** Partially implemented, experimental

- Live wallet balances (ETH / DNX / USDC)
- **Real-time price display** from both exchange feeds (MEXC WebSocket) and on-chain pool calculations
- Block number + base fee tracking
- Log window for actions + system events

### 4) Safety & operations tooling (separate section)

> **Status:** Experimental, not fully functional

This is intentionally separated in the UI from trading buttons:

- **Cancel current swap** (experimental implementation)
- **Cancel all pending transactions** (experimental implementation)
- **Nonce inspection tool** (diagnostics for nonce sync / pending txs) - experimental
- **Stuck transaction detector** (identify pending/stuck states and show details) - experimental

### 5) MEV-aware operations

> **Status:** Experimental concept, needs refinement

- Persistent connections and keepalive to selected builders/relays
- Periodic summary logs (interval configurable)
- Goal: improve reliability of the execution pipeline under real conditions

---

## How I would adapt this to other tokens / pairs

> **Status:** Conceptual design, not fully implemented

This system is designed to be reusable across different applications.

I can build similar systems for other ERC-20 tokens or pairs by:

- Updating token addresses/decimals and pair routing
- Adjusting preset buttons per token liquidity/typical sizing
- Adding/rewriting gas range tables for different volatility regimes
- Extending safety checks (slippage policies, profit checks, circuit breakers)
- Adding additional on-chain actions (approvals, multi-hop routes, bridging hooks)
- **Adapting real-time price feeds** for different exchanges and pools

---

## What needs improvement (development priorities)

> **Note:** This is an experimental system requiring significant further development

- **Automated tests for gas range selection and tx-building invariants**
- **Better configuration UX (in-app config editor + validation)**
- **Additional MEV execution options (private tx routing strategies)**
- Complete implementation of experimental features
- Enhanced error handling and recovery mechanisms
- Production-ready deployment and monitoring

---

## Collaboration Opportunities

I'm open to collaboration on interesting projects that align with my expertise. If you see potential synergies between your project and my work, I'd be interested in discussing potential cooperation.

**Contact & Portfolio:**
- **Website:** [logicencoder.com](https://logicencoder.com/)
- **Applications Gallery:** [logicencoder.com/applications/](https://logicencoder.com/applications/)
- **GitHub:** [github.com/logicencoder](https://github.com/logicencoder)

**Areas of Interest:**
- High-performance backend systems
- Real-time data processing
- API design and architecture
- Database optimization
- Cloud infrastructure
- Automation tools

If you're working on something challenging and think my skills could complement your project, feel free to reach out through my website or GitHub.

---

## Notes for reviewers

- This is an **experimental trading tool** built from a practical need.
- The focus is on **speed, clarity, and operational reliability**.
- **Many features are experimental and not fully functional** - this is a work in progress.
- Full source is private; I can share code or a sanitized demo version on request for those interested in cooperation.

---

## Source Code

Full source code available in private repository. Contact for collaboration opportunities.
