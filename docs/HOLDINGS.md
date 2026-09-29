# HOLDINGS — Holdings and Target Allocations

> **Related file:** `FEATURE_SPEC.md`  
> **Owner:** Moonsu Kang (Frontend UI), Hakeem Muritala (Backend)

---

## Purpose

A **holding** is a stock or ETF that the user owns inside a portfolio.  
Each holding has a **target allocation** (the percentage of the portfolio the user wants it to be).  
This feature lets users view, add, edit, and remove holdings, and set their target allocation percentages.

---

## Pages & Screens

### 1. Holdings Table (inside Portfolio Detail Page)

Shown as a table on the Portfolio Detail page (`/portfolios/:id`).

**Columns:**

| Column | Description |
|--------|-------------|
| Ticker | Stock symbol (e.g., AAPL, VTI) |
| Company / Fund Name | Full name of the stock or ETF |
| Shares | Number of shares currently held |
| Avg. Cost | Average price paid per share |
| Current Price | Latest available market price |
| Market Value | Shares × Current Price |
| Gain/Loss ($) | Market Value − (Shares × Avg. Cost) |
| Gain/Loss (%) | (Gain/Loss) / (Shares × Avg. Cost) × 100 |
| Current Allocation (%) | Market Value / Total Portfolio Value × 100 |
| Target Allocation (%) | User-defined target percentage |
| Drift (%) | Current Allocation − Target Allocation |

**Color coding for Drift column:**
- Green if the holding is close to its target (within ±2%)
- Yellow if drift is moderate (±2% to ±5%)
- Red if drift is large (more than ±5%)

---

### 2. Add Holding (`/portfolios/:id/holdings/new` or modal)

Let the user add a new stock or ETF to the portfolio.

**Form Fields:**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| Ticker Symbol | Text + Search | ✅ | Use stock search to find and confirm ticker |
| Number of Shares | Number | ✅ | Must be greater than 0 |
| Average Cost per Share | Number | ✅ | The price you originally paid per share |
| Target Allocation (%) | Number | ✅ | 0–100, all targets in the portfolio must sum to ≤ 100% |

**Actions:**
- Click **Add** → send `POST /portfolios/:id/holdings` → update holdings table
- Click **Cancel** → close and discard

**Validation:**
- Ticker must exist in the system (validated by the search)
- If the same ticker already exists in the portfolio, show an error: "This stock is already in your portfolio. Edit the existing holding instead."
- Total target allocations across all holdings + new one must not exceed 100%

---

### 3. Edit Holding (modal from Holdings Table row)

Let the user change the number of shares, average cost, or target allocation.

**Editable Fields:**
- Number of Shares
- Average Cost per Share
- Target Allocation (%)

**Actions:**
- Click **Save** → send `PATCH /portfolios/:id/holdings/:holding_id`
- Click **Cancel** → discard changes

---

### 4. Delete Holding

Triggered from the Holdings Table (three-dot menu or trash icon on each row).

**Behavior:**
- Show confirmation: "Remove **[Ticker]** from this portfolio? This will not delete related transactions."
- Click **Remove** → send `DELETE /portfolios/:id/holdings/:holding_id`
- Click **Cancel** → close

---

### 5. Target Allocation Summary

Below or beside the Holdings Table, show a summary bar or list:

| Info | Value |
|------|-------|
| Total Target Allocation | Sum of all target % |
| Unallocated Cash | 100% − Total Target % (shown as %) |
| Status | "Fully Allocated" ✅ or "Partially Allocated" ⚠️ |

If the total target allocation goes over 100%, highlight it in red with a warning:  
> "Total target allocation exceeds 100%. Please adjust your allocations."

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET`    | `/portfolios/:id/holdings` | Get all holdings for a portfolio |
| `POST`   | `/portfolios/:id/holdings` | Add a new holding |
| `PATCH`  | `/portfolios/:id/holdings/:holding_id` | Update shares, cost, or target allocation |
| `DELETE` | `/portfolios/:id/holdings/:holding_id` | Remove a holding |

---

## Data Model

```
Holding
├── holding_id          (PK, INT, AUTO_INCREMENT)
├── portfolio_id        (INT, FK → Portfolio.portfolio_id, NOT NULL)
├── ticker              (VARCHAR(10), FK → Stock.ticker, NOT NULL)
├── shares              (DECIMAL(15,6), NOT NULL)
├── avg_cost_per_share  (DECIMAL(15,4), NOT NULL)
├── target_allocation   (DECIMAL(5,2), DEFAULT 0.00)  -- 0.00 to 100.00
├── created_at          (DATETIME, DEFAULT CURRENT_TIMESTAMP)
└── updated_at          (DATETIME, DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP)
```

---

## Notes

- Current price is fetched from the backend using the latest stored market data (via yfinance).
- The **number of shares** and **average cost** are managed separately from transactions for simplicity, but can optionally be auto-calculated from buy/sell transaction history.
- All target allocations in a portfolio should ideally sum to exactly 100%. The remaining unallocated percentage is treated as the cash target.
