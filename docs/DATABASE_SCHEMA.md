# DATABASE_SCHEMA — MySQL Database Schema

> **Related file:** `FEATURE_SPEC.md`  
> **Owner:** Hakeem Muritala (Database Backend)  
> **Database:** MySQL 8.0+  
> **Character Set:** `utf8mb4`  
> **Collation:** `utf8mb4_unicode_ci`

---

## Overview

This document defines the complete MySQL database schema for the **Portfolio Rebalancing Strategy Simulator**.  
All tables, columns, data types, constraints, and relationships are listed here.

### Entity Relationship Summary

```
User
 └── Portfolio (one user → many portfolios)
       ├── Holding (one portfolio → many holdings)
       ├── Transaction (one portfolio → many transactions)
       └── Backtest (one portfolio → many backtests)

Stock
 ├── Holding (many holdings reference one stock)
 ├── Transaction (many transactions reference one stock)
 └── PriceHistory (one stock → many daily prices)

BenchmarkRate (standalone — from FRED API)
```

---

## Table Definitions

### 1. `users`

Stores user account information.

```sql
CREATE TABLE users (
    user_id       INT           NOT NULL AUTO_INCREMENT,
    username      VARCHAR(20)   NOT NULL,
    email         VARCHAR(255)  NOT NULL,
    password_hash VARCHAR(255)  NOT NULL,
    created_at    DATETIME      NOT NULL DEFAULT CURRENT_TIMESTAMP,

    PRIMARY KEY (user_id),
    UNIQUE KEY uq_username (username),
    UNIQUE KEY uq_email    (email)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

| Column | Type | Notes |
|--------|------|-------|
| `user_id` | INT AUTO_INCREMENT | Primary key |
| `username` | VARCHAR(20) | Unique, 3–20 characters |
| `email` | VARCHAR(255) | Unique, valid email format |
| `password_hash` | VARCHAR(255) | bcrypt hash, never plain text |
| `created_at` | DATETIME | Auto-set on insert |

---

### 2. `portfolios`

Stores each user's portfolios.

```sql
CREATE TABLE portfolios (
    portfolio_id  INT             NOT NULL AUTO_INCREMENT,
    user_id       INT             NOT NULL,
    name          VARCHAR(50)     NOT NULL,
    description   VARCHAR(200)    NULL,
    cash_balance  DECIMAL(15, 2)  NOT NULL DEFAULT 0.00,
    created_at    DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at    DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP
                                           ON UPDATE CURRENT_TIMESTAMP,

    PRIMARY KEY (portfolio_id),
    CONSTRAINT fk_portfolio_user
        FOREIGN KEY (user_id) REFERENCES users(user_id)
        ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

| Column | Type | Notes |
|--------|------|-------|
| `portfolio_id` | INT AUTO_INCREMENT | Primary key |
| `user_id` | INT | FK → users, cascades on delete |
| `name` | VARCHAR(50) | Required, max 50 characters |
| `description` | VARCHAR(200) | Optional |
| `cash_balance` | DECIMAL(15,2) | Current available cash; default $0.00 |
| `created_at` | DATETIME | Auto-set on insert |
| `updated_at` | DATETIME | Auto-updated on any change |

---

### 3. `stocks`

Stores stock and ETF metadata. Pre-loaded from yfinance.

```sql
CREATE TABLE stocks (
    ticker       VARCHAR(10)  NOT NULL,
    name         VARCHAR(100) NOT NULL,
    exchange     VARCHAR(20)  NULL,
    sector       VARCHAR(50)  NULL,
    asset_type   ENUM('STOCK', 'ETF') NOT NULL,
    last_updated DATETIME     NOT NULL DEFAULT CURRENT_TIMESTAMP
                                       ON UPDATE CURRENT_TIMESTAMP,

    PRIMARY KEY (ticker)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

| Column | Type | Notes |
|--------|------|-------|
| `ticker` | VARCHAR(10) | Primary key (e.g., 'AAPL', 'VTI') |
| `name` | VARCHAR(100) | Full company or fund name |
| `exchange` | VARCHAR(20) | e.g., 'NASDAQ', 'NYSE' |
| `sector` | VARCHAR(50) | e.g., 'Technology' |
| `asset_type` | ENUM | 'STOCK' or 'ETF' |
| `last_updated` | DATETIME | Auto-updated when record changes |

---

### 4. `price_history`

Stores historical daily prices for each stock/ETF. Loaded from yfinance.

```sql
CREATE TABLE price_history (
    price_id  INT             NOT NULL AUTO_INCREMENT,
    ticker    VARCHAR(10)     NOT NULL,
    date      DATE            NOT NULL,
    open      DECIMAL(15, 4)  NULL,
    close     DECIMAL(15, 4)  NULL,
    high      DECIMAL(15, 4)  NULL,
    low       DECIMAL(15, 4)  NULL,
    volume    BIGINT          NULL,

    PRIMARY KEY (price_id),
    UNIQUE KEY uq_ticker_date (ticker, date),
    CONSTRAINT fk_price_stock
        FOREIGN KEY (ticker) REFERENCES stocks(ticker)
        ON DELETE CASCADE,
    INDEX idx_price_date (date)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

| Column | Type | Notes |
|--------|------|-------|
| `price_id` | INT AUTO_INCREMENT | Primary key |
| `ticker` | VARCHAR(10) | FK → stocks |
| `date` | DATE | Trading date |
| `open` | DECIMAL(15,4) | Opening price |
| `close` | DECIMAL(15,4) | Closing price (most used) |
| `high` | DECIMAL(15,4) | Daily high |
| `low` | DECIMAL(15,4) | Daily low |
| `volume` | BIGINT | Number of shares traded |

---

### 5. `holdings`

Stores each stock/ETF that a user holds inside a portfolio.

```sql
CREATE TABLE holdings (
    holding_id         INT             NOT NULL AUTO_INCREMENT,
    portfolio_id       INT             NOT NULL,
    ticker             VARCHAR(10)     NOT NULL,
    shares             DECIMAL(15, 6)  NOT NULL DEFAULT 0.000000,
    avg_cost_per_share DECIMAL(15, 4)  NOT NULL DEFAULT 0.0000,
    target_allocation  DECIMAL(5, 2)   NOT NULL DEFAULT 0.00,

    created_at         DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at         DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP
                                               ON UPDATE CURRENT_TIMESTAMP,

    PRIMARY KEY (holding_id),
    UNIQUE KEY uq_portfolio_ticker (portfolio_id, ticker),
    CONSTRAINT fk_holding_portfolio
        FOREIGN KEY (portfolio_id) REFERENCES portfolios(portfolio_id)
        ON DELETE CASCADE,
    CONSTRAINT fk_holding_stock
        FOREIGN KEY (ticker) REFERENCES stocks(ticker)
        ON DELETE RESTRICT,
    CONSTRAINT chk_target_allocation
        CHECK (target_allocation >= 0.00 AND target_allocation <= 100.00)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

| Column | Type | Notes |
|--------|------|-------|
| `holding_id` | INT AUTO_INCREMENT | Primary key |
| `portfolio_id` | INT | FK → portfolios |
| `ticker` | VARCHAR(10) | FK → stocks |
| `shares` | DECIMAL(15,6) | Number of shares held |
| `avg_cost_per_share` | DECIMAL(15,4) | Average buy price per share |
| `target_allocation` | DECIMAL(5,2) | 0.00–100.00 % |
| `created_at` / `updated_at` | DATETIME | Timestamps |

> **Note:** `UNIQUE KEY uq_portfolio_ticker` ensures a stock can only appear once per portfolio.

---

### 6. `transactions`

Records all cash and trade activity in a portfolio.

```sql
CREATE TABLE transactions (
    transaction_id  INT             NOT NULL AUTO_INCREMENT,
    portfolio_id    INT             NOT NULL,
    type            ENUM('DEPOSIT', 'WITHDRAWAL', 'BUY', 'SELL') NOT NULL,
    ticker          VARCHAR(10)     NULL,
    shares          DECIMAL(15, 6)  NULL,
    price_per_share DECIMAL(15, 4)  NULL,
    amount          DECIMAL(15, 2)  NOT NULL,
    date            DATE            NOT NULL,
    notes           TEXT            NULL,
    created_at      DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at      DATETIME        NOT NULL DEFAULT CURRENT_TIMESTAMP
                                             ON UPDATE CURRENT_TIMESTAMP,

    PRIMARY KEY (transaction_id),
    CONSTRAINT fk_tx_portfolio
        FOREIGN KEY (portfolio_id) REFERENCES portfolios(portfolio_id)
        ON DELETE CASCADE,
    CONSTRAINT fk_tx_stock
        FOREIGN KEY (ticker) REFERENCES stocks(ticker)
        ON DELETE RESTRICT,
    INDEX idx_tx_portfolio_date (portfolio_id, date)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

| Column | Type | Notes |
|--------|------|-------|
| `transaction_id` | INT AUTO_INCREMENT | Primary key |
| `portfolio_id` | INT | FK → portfolios |
| `type` | ENUM | 'DEPOSIT', 'WITHDRAWAL', 'BUY', or 'SELL' |
| `ticker` | VARCHAR(10) | FK → stocks; NULL for DEPOSIT/WITHDRAWAL |
| `shares` | DECIMAL(15,6) | NULL for DEPOSIT/WITHDRAWAL |
| `price_per_share` | DECIMAL(15,4) | NULL for DEPOSIT/WITHDRAWAL |
| `amount` | DECIMAL(15,2) | Total dollar value of the transaction |
| `date` | DATE | Date the transaction happened |
| `notes` | TEXT | Optional user memo |

---

### 7. `backtests`

Stores backtest configuration and results for each run.

```sql
CREATE TABLE backtests (
    backtest_id  INT      NOT NULL AUTO_INCREMENT,
    portfolio_id INT      NOT NULL,
    start_date   DATE     NOT NULL,
    end_date     DATE     NOT NULL,
    strategies   JSON     NOT NULL,
    status       ENUM('PENDING', 'RUNNING', 'DONE', 'FAILED') NOT NULL DEFAULT 'PENDING',
    results      JSON     NULL,
    created_at   DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
    completed_at DATETIME NULL,

    PRIMARY KEY (backtest_id),
    CONSTRAINT fk_backtest_portfolio
        FOREIGN KEY (portfolio_id) REFERENCES portfolios(portfolio_id)
        ON DELETE CASCADE,
    INDEX idx_backtest_portfolio (portfolio_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

| Column | Type | Notes |
|--------|------|-------|
| `backtest_id` | INT AUTO_INCREMENT | Primary key |
| `portfolio_id` | INT | FK → portfolios |
| `start_date` | DATE | Backtest start date |
| `end_date` | DATE | Backtest end date |
| `strategies` | JSON | Array of strategy configs (see below) |
| `status` | ENUM | Current run status |
| `results` | JSON | Full simulation output; NULL until DONE |
| `completed_at` | DATETIME | NULL until DONE or FAILED |

**`strategies` JSON example:**
```json
[
  { "type": "THRESHOLD", "drift_threshold": 5.0 },
  { "type": "CALENDAR",  "frequency": "QUARTERLY" },
  { "type": "HYBRID",    "drift_threshold": 3.0 }
]
```

---

### 8. `benchmark_rates`

Stores risk-free rate data from the FRED API (used as a comparison in backtest charts).

```sql
CREATE TABLE benchmark_rates (
    rate_id    INT            NOT NULL AUTO_INCREMENT,
    date       DATE           NOT NULL,
    rate_type  VARCHAR(30)    NOT NULL,
    rate_value DECIMAL(8, 4)  NOT NULL,

    PRIMARY KEY (rate_id),
    UNIQUE KEY uq_date_type (date, rate_type),
    INDEX idx_benchmark_date (date)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

| Column | Type | Notes |
|--------|------|-------|
| `rate_id` | INT AUTO_INCREMENT | Primary key |
| `date` | DATE | Date of the rate |
| `rate_type` | VARCHAR(30) | e.g., 'DGS10' (10-year Treasury yield) |
| `rate_value` | DECIMAL(8,4) | Annual rate as a percentage (e.g., 4.5250) |

---

## Relationships Diagram

```
users ──────────────────── portfolios
                               │
               ┌───────────────┼─────────────────┐
               │               │                 │
           holdings       transactions        backtests
               │               │
               └───────┬───────┘
                       │
                    stocks
                       │
                  price_history

benchmark_rates  (standalone, no FK)
```

---

## Cascade Rules Summary

| Parent Table | Child Table | On Delete |
|-------------|------------|-----------|
| `users` | `portfolios` | CASCADE — deleting a user removes all their portfolios |
| `portfolios` | `holdings` | CASCADE — deleting a portfolio removes all its holdings |
| `portfolios` | `transactions` | CASCADE — deleting a portfolio removes all its transactions |
| `portfolios` | `backtests` | CASCADE — deleting a portfolio removes all its backtests |
| `stocks` | `holdings` | RESTRICT — cannot delete a stock that is in use |
| `stocks` | `transactions` | RESTRICT — cannot delete a stock that is referenced |
| `stocks` | `price_history` | CASCADE — deleting a stock removes all its price data |

---

## Indexes Summary

| Table | Index Name | Columns | Purpose |
|-------|-----------|---------|---------|
| `users` | `uq_username` | `username` | Fast lookup by username |
| `users` | `uq_email` | `email` | Fast lookup by email |
| `price_history` | `uq_ticker_date` | `ticker, date` | Prevent duplicate entries |
| `price_history` | `idx_price_date` | `date` | Fast date-range queries |
| `holdings` | `uq_portfolio_ticker` | `portfolio_id, ticker` | One entry per stock per portfolio |
| `transactions` | `idx_tx_portfolio_date` | `portfolio_id, date` | Fast portfolio transaction queries |
| `backtests` | `idx_backtest_portfolio` | `portfolio_id` | Fast backtest lookup by portfolio |
| `benchmark_rates` | `uq_date_type` | `date, rate_type` | Prevent duplicate rate entries |
| `benchmark_rates` | `idx_benchmark_date` | `date` | Fast date range queries |

---

## Notes

- All tables use `InnoDB` engine to support foreign keys and transactions.
- All tables use `utf8mb4` character set to support all Unicode characters.
- MySQL 8.0+ is required for native `JSON` column support.
- Passwords are always stored as bcrypt hashes — never in plain text.
- The `cash_balance` in `portfolios` is updated by the backend every time a transaction is created, edited, or deleted.
- `price_history` and `benchmark_rates` are loaded in bulk by backend scripts using yfinance and the FRED API — they are not written by user actions.
