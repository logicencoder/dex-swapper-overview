# DNX Swap WebApp — Technical Overview | **Dynex** Ecosystem Trading Solution

A fast, operator-focused trading web application I built after running into a real execution problem: swapping via typical DEX UIs (e.g., Uniswap-style flows) was too slow and "laggy" for active trading. I needed a dedicated interface that makes the critical state visible at a glance, minimizes click/typing overhead, and includes operational tools for real on-chain conditions (nonce desync, stuck txs, gas volatility, and MEV considerations) in the **Dynex** ecosystem.

> Status: **Working application with experimental transaction utilities**. The core trading functionality is operational, but some advanced transaction tools are experimental and require further development. The full source code is kept private. This is a professional **DNX** and **Dynex** trading solution.

---

## The problem I had to solve

When executing swaps manually, seconds matter in the **Dynex** ecosystem:

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

I built a lightweight web application for **Dynex (DNX)** trading with:

- A **FastAPI** backend serving a single-page UI + JSON APIs for **DNX** trading
- An async **Web3 execution layer** for **DNX** swaps and transfers
- A **config-driven gas system** (range-based priority fee + base fee multiplier) optimized for **Dynex**
- **Real-time price calculations** from both exchange feeds (MEXC) and on-chain pool data for **DNX**
- Operational tooling (cancel, nonce inspection, stuck-tx checks) for **DNX** transactions
- MEV-aware builder connectivity monitoring (keepalive + summary logs) for **Dynex** network

---

## UI snapshot

![DNX Trading UI](assets/ui.png)

---

## Key features (detailed)

### 1) Trading workflow optimized for speed

- **Sell DNX → USDC** card
  - Preset buttons for common sizes (1K, 3K, 4K, 5K, 6K, 7K, 8K, 9K, 10K, 12K, 14K, 15K, 17K, 20K, 23K, 25K) - 16 buttons
  - "Config Gas" vs "Custom Gas" modes with identical preset amounts
  - Custom amount input with real-time cost estimation
- **Buy DNX ← USDC** card
  - Preset buttons for common sizes (50, 100, 150, 200, 250, 300, 350, 400, 450, 500, 600, 700, 800, 900, 1000) - 15 buttons
  - **ALL** button to buy using full USDC balance (rounded down to avoid dust/precision failures)
  - Custom amount input with gas estimation

### 2) Config-driven gas strategy (range-based)

The app uses a JSON config to select gas parameters based on trade size.

- **Priority fee (gwei)** chosen by amount range from `dnx_swap_gas_config.json`
- **Base fee multiplier** chosen by amount range
- **Fixed gas limits**: DNX sell (310k), USDC buy (280k), USDC send (65k), ETH send (21k)
- **Config vs Custom modes**: Config uses preset gas parameters, Custom allows user override

### 3) Real-time visibility

- **Live wallet balances** (ETH / DNX / USDC) with 6-second cache updates
- **Real-time price display** from both exchange feeds (MEXC WebSocket) and on-chain pool calculations
- **Block tracking**: Current block number and base fee from WebSocket (no RPC calls)
- **Log window**: Real-time event streaming via WebSocket to browser
- **Cost estimation**: Live USD cost calculation for trades and gas

### 4) Safety & operations tooling (separate section)

> **Status:** These transaction utilities are experimental and require further development

This is intentionally separated in the UI from trading buttons:

- **Cancel current swap**: Self-to-self transaction with 1.5x gas multiplier
- **Cancel all pending transactions**: Batch cancel for multiple stuck transactions
- **Nonce inspection tool**: Diagnostics for nonce sync / pending transactions
- **Stuck transaction detector**: Scans 2000 blocks for unconfirmed transactions
- **Transaction status monitoring**: Real-time receipt checking and status updates

### 5) MEV-aware operations

- **Dual builder support**: BeaverBuild and Titan with persistent HTTP keep-alive connections
- **Bundle blasting**: Parallel submission to 6 bundle slots (3 builders × 2 future blocks)
- **Keepalive monitoring**: 45-second ping intervals with connection health tracking
- **Auto-reblast**: Automatic resubmission if transaction disappears from mempool
- **Performance tracking**: Builder response time monitoring and statistics
- **Configurable intervals**: Summary logs every 30 minutes (configurable)

### 6) Fund transfer utilities

- **USDC transfers**: Send USDC to predefined exchanges (MEXC, Gate.io) or custom addresses
- **ETH transfers**: Send ETH to predefined exchanges or custom addresses with 21k gas limit
- **Exchange integration**: Quick selection buttons for common withdrawal destinations
- **Custom recipient support**: Manual address input for any destination

### 7) Advanced monitoring & protection

- **Attacker detection**: Real-time block scanning for specific addresses with auto-cancel
- **WebSocket streaming**: Live price feeds from MEXC with protobuf parsing
- **Balance caching**: RAM-based balance storage to minimize RPC calls
- **Base fee optimization**: WebSocket-based base fee caching eliminates get_block() calls
- **Connection pooling**: Persistent HTTP sessions for MEV builders

### 8) Configuration system

- **JSON-based configuration**: Separate files for gas ranges and UI presets
- **Hot-reload capable**: Configuration changes without restart (planned)
- **Environment-based**: Secure .env file for private keys and addresses
- **Multi-exchange support**: Configurable exchange addresses and withdrawal destinations
- **Monitoring intervals**: Configurable update frequencies for all background tasks

---

## How I would adapt this to other tokens / pairs

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

> **Note:** Focus on completing experimental transaction utilities

- Complete implementation of experimental transaction utilities
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
- **Dynex ecosystem development**
- **DNX trading solutions**

If you're working on something challenging and think my skills could complement your project, feel free to reach out through my website or GitHub.

---

## Notes for reviewers

- This is a **working trading tool** built from a practical need.
- The focus is on **speed, clarity, and operational reliability**.
- **Transaction utilities (cancel, nonce inspection, stuck tx detection) are experimental** and require further development.
- Full source is private; I can share code or a sanitized demo version on request for those interested in cooperation.
- This is a professional **DNX** and **Dynex** ecosystem solution.

---

## Source Code

Full source code available in private repository. Contact for collaboration opportunities.

---

**Professional DNX Swap Bot & Arbitrage Engine for Dynex. Standalone desktop solution. Interested parties and cooperation: https://logicencoder.com/contact**
