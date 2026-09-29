# TRANSACTIONS — Transaction History

> **Related file:** `FEATURE_SPEC.md`  
> **Owner:** Moonsu Kang (Frontend UI), Hakeem Muritala (Backend)

---

## Purpose

A **transaction** records money movement or stock activity inside a portfolio.  
There are four types of transactions:

| Type | Description |
|------|-------------|
| **Deposit** | Adding cash into the portfolio |
| **Withdrawal** | Taking cash out of the portfolio |
| **Buy** | Purchasing shares of a stock or ETF |
| **Sell** | Selling shares of a stock or ETF |

Transactions affect the portfolio's **cash balance** and are also used as inputs for the **backtesting engine**.

---

## Pages & Screens

### 1. Transaction List Page (`/portfolios/:id/transactions`)

Shows all transactions for a given portfolio in a table, sorted by date (newest first).

**Columns:**

| Column | Description |
|--------|-------------|
| Date | Date the transaction occurred |
| Type | Deposit / Withdrawal / Buy / Sell |
| Ticker | Stock symbol (for Buy/Sell only; blank for cash transactions) |
| Shares | Number of shares (Buy/Sell only) |
| Price per Share | Price at time of transaction (Buy/Sell only) |
| Amount | Total dollar amount of the transaction |
| Notes | Optional user note |

**Filters (optional):**
- Filter by transaction type (All / Deposit / Withdrawal / Buy / Sell)
- Filter by date range

**Actions per row:**
- Click **Edit** → open Edit Transaction modal
- Click **Delete** → show confirmation dialog

---

### 2. Add Transaction (modal or `/portfolios/:id/transactions/new`)

Let the user record a new transaction.

**Step 1 — Choose Transaction Type**  
Show four large buttons: **Deposit**, **Withdrawal**, **Buy**, **Sell**

---

**Step 2a — Deposit or Withdrawal**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| Amount ($) | Number | ✅ | Must be greater than $0 |
| Date | Date picker | ✅ | Cannot be a future date |
| Notes | Text | ❌ | Optional memo |

- Deposit → increases cash balance
- Withdrawal → decreases cash balance; cannot exceed current cash balance

---

**Step 2b — Buy or Sell**

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| Ticker | Text + Search | ✅ | Must be a valid ticker in the portfolio (for Sell) or search (for Buy) |
| Number of Shares | Number | ✅ | Must be greater than 0 |
| Price per Share ($) | Number | ✅ | Price at the time of transaction |
| Date | Date picker | ✅ | Cannot be a future date |
| Notes | Text | ❌ | Optional memo |

- **Buy** → total cost (Shares × Price) is deducted from cash balance
- **Sell** → total proceeds are added to cash balance

**Validation for Sell:**
- Cannot sell more shares than currently held
- Date of sell cannot be before the first buy date of that stock

**Validation for Buy:**
- Total cost cannot exceed current cash balance (or show a warning)

---

### 3. Edit Transaction (modal)

Let the user correct a previously recorded transaction.

**Editable Fields:**
- Date
- Amount / Shares / Price per Share
- Notes

**Actions:**
- Click **Save** → send `PATCH /portfolios/:id/transactions/:tx_id`
- Click **Cancel** → discard

> **Note:** Editing a transaction will recalculate the cash balance accordingly.

---

### 4. Delete Transaction

Show confirmation: "Delete this transaction? The portfolio balance will be updated."

- Click **Delete** → send `DELETE /portfolios/:id/transactions/:tx_id`

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET`    | `/portfolios/:id/transactions` | Get all transactions for a portfolio |
| `POST`   | `/portfolios/:id/transactions` | Create a new transaction |
| `PATCH`  | `/portfolios/:id/transactions/:tx_id` | Edit a transaction |
| `DELETE` | `/portfolios/:id/transactions/:tx_id` | Delete a transaction |

---

## Data Model

```
Transaction
├── transaction_id    (PK, INT, AUTO_INCREMENT)
├── portfolio_id      (INT, FK → Portfolio.portfolio_id, NOT NULL)
├── type              (ENUM('DEPOSIT','WITHDRAWAL','BUY','SELL'), NOT NULL)
├── ticker            (VARCHAR(10), FK → Stock.ticker, NULL)  -- only for BUY/SELL
├── shares            (DECIMAL(15,6), NULL)                   -- only for BUY/SELL
├── price_per_share   (DECIMAL(15,4), NULL)                   -- only for BUY/SELL
├── amount            (DECIMAL(15,2), NOT NULL)
├── date              (DATE, NOT NULL)
├── notes             (TEXT, NULL)
├── created_at        (DATETIME, DEFAULT CURRENT_TIMESTAMP)
└── updated_at        (DATETIME, DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP)
```

---

## Notes

- `amount` for Buy = `shares × price_per_share` (auto-calculated)
- `amount` for Sell = `shares × price_per_share` (auto-calculated)
- `amount` for Deposit/Withdrawal = the value the user enters directly
- Cash balance is updated whenever a transaction is added, edited, or deleted
- Transaction history is a key input for the backtesting engine — do not allow transactions to be in an inconsistent state (e.g., selling more than owned)
