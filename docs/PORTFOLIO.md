# PORTFOLIO — Portfolio Management

> **Related file:** `FEATURE_SPEC.md`  
> **Owner:** Moonsu Kang (Frontend UI), Hakeem Muritala (Backend)

---

## Purpose

A **portfolio** is the main object in this application.  
Each user can have multiple portfolios. A portfolio holds a list of assets (holdings), a transaction history, and target allocation settings.

---

## Pages & Screens

### 1. Portfolio List Page (`/portfolios`)

Shows all portfolios that belong to the current user.

**What is displayed per portfolio card:**
- Portfolio name
- Total current value (in USD)
- Overall return (%) since creation
- Number of holdings
- Last updated date

**Actions:**
- Click a portfolio card → go to Portfolio Detail page
- Click **+ Create Portfolio** button → open the Create Portfolio modal or page

**Empty state:**  
If the user has no portfolios, show a friendly message:  
> "You don't have any portfolios yet. Create one to get started!"

---

### 2. Create Portfolio (`/portfolios/new` or modal)

Let the user create a new portfolio.

**Form Fields:**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| Portfolio Name | Text | ✅ | Max 50 characters |
| Description | Textarea | ❌ | Optional, max 200 characters |
| Starting Cash Balance | Number | ❌ | Default: $0.00 |

**Actions:**
- Click **Create** → send `POST /portfolios` → redirect to new portfolio's detail page
- Click **Cancel** → close modal / go back

---

### 3. Portfolio Detail Page (`/portfolios/:id`)

Shows all information about one specific portfolio.

**Sections:**

#### 3a. Summary Card (top of page)
| Info | Description |
|------|-------------|
| Total Value | Sum of all holdings + current cash balance |
| Total Return | (Current Value − Total Invested) / Total Invested × 100% |
| XIRR | Extended Internal Rate of Return (time-weighted) |
| Cash Balance | Available cash from deposits/withdrawals |
| Number of Holdings | Count of stocks/ETFs in the portfolio |

#### 3b. Holdings Table
Shows each stock/ETF held in the portfolio.  
→ Described in detail in `HOLDINGS.md`

#### 3c. Allocation Chart
A pie or donut chart showing current allocation vs. target allocation.

#### 3d. Recent Transactions
Shows the 5 most recent transactions with a "View All" link.  
→ Described in detail in `TRANSACTIONS.md`

#### 3e. Quick Actions
- **+ Add Holding** — add a new stock or ETF
- **+ Add Transaction** — record a deposit, withdrawal, buy, or sell
- **Run Backtest** — go to the backtest setup page for this portfolio

---

### 4. Edit Portfolio (`/portfolios/:id/edit` or modal)

Let the user change the portfolio name or description.

**Editable Fields:**
- Portfolio Name
- Description

**Actions:**
- Click **Save Changes** → send `PATCH /portfolios/:id` → update and show success message
- Click **Cancel** → discard changes and go back

---

### 5. Delete Portfolio

Triggered from the Portfolio Detail page or Portfolio List page (three-dot menu).

**Behavior:**
- Show a confirmation dialog: "Are you sure you want to delete **[Portfolio Name]**? This action cannot be undone."
- Click **Delete** → send `DELETE /portfolios/:id` → remove portfolio → redirect to Portfolio List
- Click **Cancel** → close dialog

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET`    | `/portfolios` | Get all portfolios for the current user |
| `POST`   | `/portfolios` | Create a new portfolio |
| `GET`    | `/portfolios/:id` | Get details of one portfolio |
| `PATCH`  | `/portfolios/:id` | Update portfolio name or description |
| `DELETE` | `/portfolios/:id` | Delete a portfolio and all its data |

---

## Data Model

```
Portfolio
├── portfolio_id    (PK, INT, AUTO_INCREMENT)
├── user_id         (INT, FK → User.user_id, NOT NULL)
├── name            (VARCHAR(50), NOT NULL)
├── description     (VARCHAR(200), NULL)
├── cash_balance    (DECIMAL(15,2), DEFAULT 0.00)
├── created_at      (DATETIME, DEFAULT CURRENT_TIMESTAMP)
└── updated_at      (DATETIME, DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP)
```

---

## Notes

- Deleting a portfolio must also delete all related holdings, transactions, target allocations, and backtest results (cascade delete).
- Total portfolio value is calculated on the frontend using the latest available price data from the backend.
- Cash balance changes when deposits, withdrawals, buys, or sells are recorded.
