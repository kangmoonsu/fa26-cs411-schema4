# Minsuk Choi — Backtesting Backend Prompts

> **Role:** Backtesting Backend Lead  
> **Course:** CS 411 — University of Illinois Urbana-Champaign  
> **Assigned Branch:** `git checkout -b feat/minsuk/backtest-engine-and-metrics`  
> **Execution Priority:** **Phase 2 (After Hakeem creates tables)** — Builds core financial algorithms and simulation endpoints.

---

## 📌 Instructions for Minsuk:
You have 4 sequential prompts below. Feed them one by one into your AI coding assistant (or implement them in this exact order).

---

### 🔹 Prompt 1: Market & Benchmark Data Ingestion with Caching
*Copy and paste the prompt below:*

```markdown
You are the Backtesting Backend Lead for RebalanceLab (CS 411).
Your task is to build the historical market data pipeline using `yfinance` and the FRED API, caching daily records into GCP MySQL using parameterized native SQL.

### Requirements:
1. Install dependencies: `pip install yfinance pandas requests`.
2. Create `backend/app/services/market_data.py`:
   - `fetch_and_cache_prices(tickers: list[str], start_date: str, end_date: str)`:
     - Check `price_history` table using native SQL:
       ```sql
       SELECT DISTINCT ticker, date FROM price_history 
       WHERE ticker IN (:tickers) AND date BETWEEN :start_date AND :end_date;
       ```
     - For missing dates, download adjusted price data using `yfinance`:
       ```python
       import yfinance as yf
       df = yf.download(tickers, start=start_date, end=end_date, group_by='ticker')
       ```
     - Upsert downloaded records into `price_history` with native parameterized SQL:
       ```sql
       INSERT INTO price_history (ticker, date, open_price, high_price, low_price, close_price, volume)
       VALUES (:ticker, :date, :open, :high, :low, :close, :volume)
       ON DUPLICATE KEY UPDATE close_price = VALUES(close_price), volume = VALUES(volume);
       ```
3. Benchmark Data Integration:
   - `fetch_benchmark_rates(start_date: str, end_date: str)`:
     - Fetch 3-Month Treasury Bill Secondary Market Rate (FRED series `DGS3MO`) or default risk-free annual rate series (e.g. 4.0% annualized).
     - Cache daily rate records into `benchmark_rates`.
```

---

### 🔹 Prompt 2: Core Rebalancing Strategy Algorithms
*Copy and paste the prompt below:*

```markdown
You are the Backtesting Backend Lead for RebalanceLab (CS 411).
Your task is to implement the 3 portfolio rebalancing algorithms: Threshold-based, Calendar-based, and Cash-Flow-Aware Hybrid.

### Mathematical & Simulation Specification:
Given a portfolio with assets $i \in \{1, \dots, N\}$, target weights $w_i^*$, and cash balance $C(t)$:
On each trading day $t$ with closing prices $P_i(t)$ and held shares $S_i(t)$:
- Asset Value: $V_i(t) = S_i(t) \cdot P_i(t)$
- Total Portfolio Value: $V_{total}(t) = C(t) + \sum_{i=1}^N V_i(t)$
- Current Weight: $w_i(t) = V_i(t) / V_{total}(t)$

### Implement 3 Strategy Engines in `backend/app/services/rebalance/`:

1. **Threshold-Based Strategy (`threshold.py`):**
   - Check drift: $\text{Drift}_i(t) = |w_i(t) - w_i^*|$.
   - Trigger condition: If any $\text{Drift}_i(t) > \delta$ (user threshold, e.g. 0.05):
     - Calculate target dollar value for each asset: $T_i = V_{total}(t) \cdot w_i^*$.
     - Sell overweight assets ($V_i(t) > T_i$): sell $\Delta S_i = (V_i(t) - T_i) / P_i(t)$ shares, adding funds to cash.
     - Buy underweight assets ($V_i(t) < T_i$): buy $\Delta S_i = (T_i - V_i(t)) / P_i(t)$ shares using available cash.
     - Record rebalance trade events (Date, Ticker, BUY/SELL, Shares, Price, Amount).

2. **Calendar-Based Strategy (`calendar.py`):**
   - Trigger condition: Only rebalance on specified calendar dates (e.g., first trading day of each month, quarter, or year).
   - On scheduled day, rebalance all weights back to $w_i^*$.

3. **Cash-Flow-Aware / Hybrid Strategy (`hybrid.py` - Creative Component):**
   - Trigger condition: Check threshold $\delta$.
   - **Smart Cash Flow Allocation:** When user deposits cash $\Delta C$ on day $t$:
     - Do NOT sell overweight assets immediately!
     - Instead, route new deposit cash $\Delta C$ preferentially to buy the most underweight assets ($w_i < w_i^*$) to bring them closer to target.
     - Only execute sell trades if drift still exceeds $\delta$ after new cash is fully deployed.
     - When withdrawals occur, liquidate overweight assets first.
     - Minimizes turnover and transaction frequency.
```

---

### 🔹 Prompt 3: Financial Metrics & XIRR Calculator
*Copy and paste the prompt below:*

```markdown
You are the Backtesting Backend Lead for RebalanceLab (CS 411).
Your task is to implement the financial performance metrics module in `backend/app/services/metrics.py`, specifically Extended Internal Rate of Return (XIRR).

### Requirements:
1. **Extended Internal Rate of Return (XIRR):**
   - Unlike basic CAGR, XIRR accounts for irregular cash deposits and withdrawals over time.
   - Equation to solve for annual rate $r$:
     $$\sum_{k=0}^{N} \frac{C_k}{(1 + r)^{(d_k - d_0) / 365.0}} = 0$$
     Where:
     - $C_0 = -V_{\text{initial}}$ at start date $d_0$.
     - $C_k = -\text{deposit}$ or $+\text{withdrawal}$ at event date $d_k$.
     - $C_N = +V_{\text{final}}$ at end date $d_N$.
   - Implement root finding using `scipy.optimize.newton` or bisection method with fallback if convergence fails.
2. **Total Return & Annualized Return:**
   $$\text{Total Return} = \frac{V_{\text{final}} - \text{Net Deposits} - V_{\text{initial}}}{V_{\text{initial}} + \text{Weighted Deposits}}$$
3. **Maximum Drawdown (MDD):**
   - Track peak portfolio valuation $M(t) = \max_{\tau \le t} V(\tau)$.
   $$\text{Drawdown}(t) = \frac{M(t) - V(t)}{M(t)}$$
   $$\text{MDD} = \max_t \text{Drawdown}(t)$$
4. **Turnover Ratio:**
   $$\text{Turnover} = \frac{\sum_{\text{all rebalance trades}} \min(\text{Total Purchases}, \text{Total Sales})}{\text{Average Portfolio Value}}$$
5. Return all metrics packaged cleanly in a Pydantic model `BacktestMetrics`.
```

---

### 🔹 Prompt 4: Backtesting Execution Endpoints & Comparison
*Copy and paste the prompt below:*

```markdown
You are the Backtesting Backend Lead for RebalanceLab (CS 411).
Your task is to build the FastAPI endpoints in `backend/app/routers/backtest.py` to run simulations, save results to GCP MySQL, and support multi-strategy comparison.

### Requirements:
1. `POST /api/backtests/run`:
   - Request Body:
     ```json
     {
       "portfolio_id": 1,
       "strategy_type": "THRESHOLD",
       "start_date": "2023-01-01",
       "end_date": "2024-01-01",
       "threshold_pct": 5.0,
       "frequency": "QUARTERLY",
       "include_cash_flows": true
     }
     ```
   - Workflow:
     1. Query portfolio holdings and initial cash from MySQL.
     2. Ensure price history is cached via `market_data.py`.
     3. Run selected rebalancing strategy engine.
     4. Calculate metrics (Total Return, Annualized Return, XIRR, MDD, Turnover, Rebalance count).
     5. Save summary into `backtests` table with native SQL:
        ```sql
        INSERT INTO backtests (portfolio_id, strategy_type, start_date, end_date, initial_value, final_value, total_return, xirr, max_drawdown, total_turnover, rebalance_count, result_summary_json)
        VALUES (:portfolio_id, :strategy_type, :start_date, :end_date, :initial_value, :final_value, :total_return, :xirr, :max_drawdown, :total_turnover, :rebalance_count, :result_summary_json);
        ```
     6. Return complete JSON response including daily history and rebalance trade events.

2. `GET /api/backtests/{backtest_id}`:
   - Fetches stored backtest results and parsed JSON history from `backtests` table.

3. `POST /api/backtests/compare`:
   - Accepts a list of strategy configurations, runs them over the identical portfolio and time horizon, and returns combined time-series curves and side-by-side metric tables.
```
