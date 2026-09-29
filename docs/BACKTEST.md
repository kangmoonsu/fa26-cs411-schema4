# BACKTEST — Backtesting Engine and Strategy Comparison

> **Related file:** `FEATURE_SPEC.md`  
> **Owner:** John Cha (Frontend Visualization), Minsuk Choi (Backend Simulation)

---

## Purpose

The **backtesting engine** is the main creative feature of this application.  
It simulates how different portfolio rebalancing strategies would have performed over a selected historical time period using real market data.

Users can compare up to **three strategies** side by side:
1. **Threshold-Based Rebalancing** — Rebalance when drift exceeds a percentage limit
2. **Calendar-Based Rebalancing** — Rebalance on a fixed schedule (monthly, quarterly, yearly)
3. **Cash-Flow-Aware / Hybrid Rebalancing** — Use new deposits to rebalance before selling assets

---

## Pages & Screens

### 1. Backtest Setup Page (`/portfolios/:id/backtest/new`)

Let the user configure a backtest before running it.

**Section A — Time Period**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| Start Date | Date picker | ✅ | Must be before End Date |
| End Date | Date picker | ✅ | Cannot be a future date |

---

**Section B — Select Strategies to Compare**

The user can select one or more strategies (up to 3):

**Strategy 1: Threshold-Based**
| Field | Type | Required | Notes |
|-------|------|----------|-------|
| Drift Threshold (%) | Number | ✅ | e.g., 5% → rebalance when any asset drifts by more than 5% |

**Strategy 2: Calendar-Based**
| Field | Type | Required | Notes |
|-------|------|----------|-------|
| Rebalancing Frequency | Dropdown | ✅ | Monthly / Quarterly / Yearly |

**Strategy 3: Cash-Flow-Aware / Hybrid**
| Field | Type | Required | Notes |
|-------|------|----------|-------|
| Drift Threshold (%) | Number | ✅ | Only sells if drift cannot be fixed by directing deposits |

---

**Section C — Starting Assumptions**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| Use Portfolio's Actual Transactions | Toggle | ✅ | Yes = use real deposit/withdrawal history; No = simulate with no cash flows |
| Transaction Cost per Trade ($) | Number | ❌ | Optional. Default: $0.00 |

---

**Actions:**
- Click **Run Backtest** → send `POST /portfolios/:id/backtests` → redirect to Results page
- Click **Cancel** → go back to Portfolio Detail

---

### 2. Backtest Results Page (`/portfolios/:id/backtest/:backtest_id`)

Shows the full results of a completed backtest with charts and tables.

---

#### 2a. Summary Comparison Table

Shows high-level results for each strategy side by side:

| Metric | Threshold | Calendar | Cash-Flow-Aware |
|--------|-----------|----------|-----------------|
| Final Portfolio Value | $X | $X | $X |
| Total Return (%) | X% | X% | X% |
| XIRR (%) | X% | X% | X% |
| Number of Rebalances | N | N | N |
| Portfolio Turnover (%) | X% | X% | X% |
| Avg. Allocation Drift (%) | X% | X% | X% |
| Max Allocation Drift (%) | X% | X% | X% |

---

#### 2b. Portfolio Value Over Time Chart

A **line chart** showing portfolio value over the selected period.  
Each strategy is shown as a separate colored line.  
An optional benchmark line (e.g., S&P 500 or risk-free rate from FRED) can be added.

**Chart Interactions:**
- Hover on any point → show tooltip with date, value per strategy
- Click on a rebalance event marker → show which assets were rebalanced and how much

---

#### 2c. Allocation Drift Over Time Chart

A **line chart** showing how far the portfolio drifted from target allocations over time.  
One chart per strategy, or all on the same chart using different colors.

---

#### 2d. Rebalance Events Timeline

A list or timeline showing each rebalance event:

| Date | Strategy | Trigger Reason | Assets Rebalanced | Trade Count |
|------|----------|---------------|-------------------|-------------|
| 2022-03-15 | Threshold | AAPL drifted +6.2% | AAPL, VTI | 2 trades |
| 2022-06-01 | Calendar | Quarterly schedule | All holdings | 4 trades |

---

#### 2e. Per-Asset Return Table

| Ticker | Start Value | End Value | Return (%) | Contribution |
|--------|-------------|-----------|-----------|--------------|
| AAPL | $X | $X | X% | X% |
| VTI | $X | $X | X% | X% |

---

### 3. Backtest List (`/portfolios/:id/backtests`)

Shows a history of all backtests run for a portfolio.

**Columns:**
- Run Date
- Time Period (Start → End)
- Strategies Compared
- Best Performing Strategy (by Total Return)
- Actions: View / Delete

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST`   | `/portfolios/:id/backtests` | Create and run a new backtest |
| `GET`    | `/portfolios/:id/backtests` | List all backtests for a portfolio |
| `GET`    | `/portfolios/:id/backtests/:backtest_id` | Get detailed results of one backtest |
| `DELETE` | `/portfolios/:id/backtests/:backtest_id` | Delete a backtest result |

---

## Data Model

```
Backtest
├── backtest_id       (PK, INT, AUTO_INCREMENT)
├── portfolio_id      (INT, FK → Portfolio.portfolio_id, NOT NULL)
├── start_date        (DATE, NOT NULL)
├── end_date          (DATE, NOT NULL)
├── strategies        (JSON, NOT NULL)                      -- strategy configs as JSON array
├── status            (ENUM('PENDING','RUNNING','DONE','FAILED'), DEFAULT 'PENDING')
├── results           (JSON, NULL)                          -- simulation output stored as JSON
├── created_at        (DATETIME, DEFAULT CURRENT_TIMESTAMP)
└── completed_at      (DATETIME, NULL)
```

---

## Metrics Glossary

| Term | Simple Explanation |
|------|--------------------|
| **Total Return (%)** | How much the portfolio grew (or shrank) in percentage |
| **XIRR** | Like a return rate that accounts for the timing of deposits and withdrawals |
| **Portfolio Turnover (%)** | How much of the portfolio was bought/sold during the period. Lower = fewer trades |
| **Allocation Drift (%)** | How far each asset moved away from its target percentage |
| **Number of Rebalances** | How many times the strategy triggered a rebalancing event |

---

## Strategy Logic Summary

### Threshold-Based
- Check allocation drift after each trading day
- If **any** holding drifts beyond the threshold → trigger a rebalance
- Buy underweight assets, sell overweight assets to return to target

### Calendar-Based
- Trigger a rebalance on a fixed schedule (e.g., first trading day of every quarter)
- Rebalance all holdings back to target regardless of current drift

### Cash-Flow-Aware / Hybrid
- When a deposit arrives → direct new cash toward underweight assets first
- If drift is still beyond threshold after directing the deposit → sell overweight assets
- Goal: minimize the number of sells (and therefore trading costs)

---

## Notes

- Market data is fetched from the stored yfinance dataset — no live data is used during a backtest
- If market data is missing for a specific date, use the most recent available price
- FRED benchmark data (risk-free rate) is used as an optional comparison line in charts
- Backtest computation may take a few seconds — show a progress indicator during processing
