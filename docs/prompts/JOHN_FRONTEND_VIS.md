# John Cha — Frontend Visualization Prompts

> **Role:** Frontend Visualization Lead  
> **Course:** CS 411 — University of Illinois Urbana-Champaign  
> **Assigned Branch:** `git checkout -b feat/john/backtest-vis-and-search`  
> **Execution Priority:** **Phase 2 & 3** — Builds the interactive backtest dashboard, financial charts, and stock search.

---

## 📌 Instructions for John:
You have 4 sequential prompts below. Feed them one by one into your AI coding assistant (or implement them in this exact order).

---

### 🔹 Prompt 1: Backtest Setup Configuration Form
*Copy and paste the prompt below:*

```markdown
You are the Frontend Visualization Lead for RebalanceLab (CS 411).
Your task is to build the Backtest Configuration Form in `src/pages/backtest/BacktestSetup.tsx` using the Toss Securities design system components created by Moonsu Kang (`Card`, `Button`, `Input`, `Select`).

### Requirements:
1. **Portfolio Selector:**
   - Dropdown querying `GET /api/portfolios` allowing the user to pick an existing portfolio.
2. **Strategy Selector:**
   - Three selectable strategy cards with icons and descriptions:
     - **Threshold-Based Rebalance:** Shows Drift Tolerance Input $\delta$ (default 5.0%, min 1.0%, max 20.0%).
     - **Calendar-Based Rebalance:** Shows Frequency Dropdown (`Monthly`, `Quarterly`, `Semi-Annually`, `Annually`).
     - **Cash-Flow-Aware Hybrid:** Combines drift threshold with smart deposit/withdrawal routing.
3. **Date Range Picker:**
   - Start Date & End Date inputs with quick preset buttons ("1 Year", "3 Years", "5 Years", "YTD").
4. **Cash Flow Consideration:**
   - Checkbox toggle: "Incorporate historical deposits & withdrawals into simulation".
5. **Execution Action:**
   - "Run Simulation" primary button with loading state that triggers `POST /api/backtests/run`.
   - On success, redirect or transition smoothly to the Results view.
```

---

### 🔹 Prompt 2: Interactive Performance Charts & Metric Cards
*Copy and paste the prompt below:*

```markdown
You are the Frontend Visualization Lead for RebalanceLab (CS 411).
Your task is to build `src/pages/backtest/BacktestResults.tsx` to visualize backtest simulation results using Recharts and Toss-style metric cards.

### Tech Stack:
- `recharts` (`npm install recharts`)
- Lucide-React icons

### Requirements:
1. **Summary Metric Cards Grid (Top):**
   - Final Portfolio Value ($) & Total Gain ($)
   - Total Return (%) with colored badge
   - **XIRR (%)** with tooltip explaining Extended Internal Rate of Return
   - Max Drawdown (%)
   - Total Turnover ($ or %)
   - Number of Rebalances Triggered
   - Benchmark (Treasury Risk-Free Rate) Return (%)

2. **Main Performance Time-Series Chart (Recharts `ResponsiveContainer` + `LineChart`):**
   - X-Axis: Dates formatted as `MMM YYYY`.
   - Y-Axis: Portfolio Valuation ($).
   - Line 1: Simulated Strategy Growth (Toss Blue `#3182F6`, strokeWidth 2.5).
   - Line 2: Benchmark Risk-Free Growth (Dashed Gray `#8B95A1`).
   - Custom Tooltip showing exact Date, Portfolio Value, and Benchmark Value.
   - Time range quick filters (1M, 6M, 1Y, ALL).

3. **Allocation Drift Area Chart (Recharts `AreaChart`):**
   - Shows asset weight percentages ($w_i$) over the backtest timeline.
   - Displays drift spikes exceeding the threshold and snapping back after rebalance events.

4. **Rebalance Events Feed / Table:**
   - Collapsible log of every rebalance event:
     - Date
     - Trigger Reason ("Drift > 5.0%" or "Quarterly Scheduled")
     - List of trades executed: `BUY 12.5 shares AAPL @ $185.20 ($2,315.00)`.
     - Remaining Cash Balance.
```

---

### 🔹 Prompt 3: Multi-Strategy Comparison Dashboard
*Copy and paste the prompt below:*

```markdown
You are the Frontend Visualization Lead for RebalanceLab (CS 411).
Your task is to build `src/pages/backtest/StrategyComparison.tsx` allowing users to compare 2 or 3 rebalancing strategies side-by-side.

### Requirements:
1. Configuration Bar:
   - Pick a portfolio and time horizon, then select up to 3 strategies to compare (e.g. "Threshold 5%" vs "Quarterly" vs "Cash-Flow Hybrid").
   - "Compare Strategies" button calling `POST /api/backtests/compare`.

2. Overlaid Growth Chart (Recharts):
   - Single chart displaying 3 colored curves simultaneously:
     - Blue: Threshold Strategy
     - Purple: Calendar Strategy
     - Green: Cash-Flow Hybrid Strategy
     - Dashed: Benchmark
   - Interactive legend with toggle to show/hide individual curves.

3. Side-by-Side Comparison Matrix Table:
   - Columns: Metric, Strategy A, Strategy B, Strategy C, Benchmark.
   - Rows:
     - Final Valuation ($)
     - Total Return (%)
     - Annualized Return (%)
     - XIRR (%)
     - Max Drawdown (%)
     - Total Turnover ($)
     - Trade Count
   - Highlight the winning strategy for each metric with a green badge.
```

---

### 🔹 Prompt 4: Stock & ETF Search Modal
*Copy and paste the prompt below:*

```markdown
You are the Frontend Visualization Lead for RebalanceLab (CS 411).
Your task is to build `src/components/search/StockSearchModal.tsx` for searching and picking securities.

### Requirements:
1. Search Input:
   - Search modal with search icon and 300ms debounce.
   - Queries `GET /api/stocks/search?q={query}`.
2. Results List:
   - Displays matching stocks and ETFs:
     - Ticker symbol (e.g. `AAPL`, `SPY`) in bold.
     - Company Name (e.g. "Apple Inc.").
     - Badge: `STOCK` (blue) or `ETF` (purple).
     - Exchange & currency.
3. Selection Callback:
   - `onSelect(stock: Stock)` prop passing the chosen security back to parent modal (such as Moonsu's "Add Holding" dialog).
4. Loading spinner during network request and empty state if no matching ticker is found.
```
