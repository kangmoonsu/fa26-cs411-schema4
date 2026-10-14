# Hakeem Muritala — Database Backend Prompts

> **Role:** Database Backend Lead  
> **Course:** CS 411 — University of Illinois Urbana-Champaign  
> **Assigned Branch:** `git checkout -b feat/hakeem/gcp-schema-and-crud`  
> **Execution Priority:** **Phase 1 (Start First)** — Teammates need tables in GCP MySQL before storing data.

---

## 📌 Instructions for Hakeem:
You have 4 sequential prompts below. Feed them one by one into your AI coding assistant (or implement them in this exact order). Test each step before proceeding to the next.

---

### 🔹 Prompt 1: GCP MySQL DDL Runner & Table Initialization
*Copy and paste the prompt below:*

```markdown
You are the Database Backend Lead for RebalanceLab (CS 411).
Your task is to create the database initialization script and execute our MySQL 8.0 schema on our Google Cloud Platform (GCP) Cloud SQL instance.

### Requirements:
1. Create `backend/app/db/init_db.py` that connects to MySQL using environment variables (`DB_HOST`, `DB_PORT`, `DB_USER`, `DB_PASSWORD`, `DB_NAME`).
2. The script must execute the following complete DDL statements to create all 8 tables with proper foreign keys and indices:

```sql
-- 1. Users Table
CREATE TABLE IF NOT EXISTS users (
    user_id       INT           NOT NULL AUTO_INCREMENT,
    username      VARCHAR(20)   NOT NULL,
    email         VARCHAR(255)  NOT NULL,
    password_hash VARCHAR(255)  NOT NULL,
    created_at    DATETIME      NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (user_id),
    UNIQUE KEY uq_username (username),
    UNIQUE KEY uq_email (email)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 2. Portfolios Table
CREATE TABLE IF NOT EXISTS portfolios (
    portfolio_id  INT            NOT NULL AUTO_INCREMENT,
    user_id       INT            NOT NULL,
    name          VARCHAR(100)   NOT NULL,
    initial_cash  DECIMAL(15,2)  NOT NULL DEFAULT 0.00,
    cash_balance  DECIMAL(15,2)  NOT NULL DEFAULT 0.00,
    created_at    DATETIME       NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at    DATETIME       NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (portfolio_id),
    FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 3. Stocks Table
CREATE TABLE IF NOT EXISTS stocks (
    ticker      VARCHAR(10)   NOT NULL,
    name        VARCHAR(100)  NOT NULL,
    asset_type  ENUM('STOCK', 'ETF') NOT NULL DEFAULT 'STOCK',
    exchange    VARCHAR(20)   NULL,
    currency    VARCHAR(10)   NOT NULL DEFAULT 'USD',
    is_active   BOOLEAN       NOT NULL DEFAULT TRUE,
    PRIMARY KEY (ticker)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 4. Holdings Table
CREATE TABLE IF NOT EXISTS holdings (
    holding_id        INT            NOT NULL AUTO_INCREMENT,
    portfolio_id      INT            NOT NULL,
    ticker            VARCHAR(10)    NOT NULL,
    current_shares    DECIMAL(12,4)  NOT NULL DEFAULT 0.0000,
    average_buy_price DECIMAL(10,2)  NOT NULL DEFAULT 0.00,
    target_weight     DECIMAL(5,2)   NOT NULL,
    created_at        DATETIME       NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at        DATETIME       NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (holding_id),
    UNIQUE KEY uq_portfolio_ticker (portfolio_id, ticker),
    FOREIGN KEY (portfolio_id) REFERENCES portfolios(portfolio_id) ON DELETE CASCADE,
    FOREIGN KEY (ticker) REFERENCES stocks(ticker) ON UPDATE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 5. Transactions Table
CREATE TABLE IF NOT EXISTS transactions (
    transaction_id   INT            NOT NULL AUTO_INCREMENT,
    portfolio_id     INT            NOT NULL,
    ticker           VARCHAR(10)    NULL,
    type             ENUM('DEPOSIT', 'WITHDRAWAL', 'BUY', 'SELL') NOT NULL,
    shares           DECIMAL(12,4)  NULL,
    price_per_share  DECIMAL(10,2)  NULL,
    total_amount     DECIMAL(15,2)  NOT NULL,
    transaction_date DATETIME       NOT NULL DEFAULT CURRENT_TIMESTAMP,
    notes            TEXT           NULL,
    PRIMARY KEY (transaction_id),
    FOREIGN KEY (portfolio_id) REFERENCES portfolios(portfolio_id) ON DELETE CASCADE,
    FOREIGN KEY (ticker) REFERENCES stocks(ticker) ON UPDATE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 6. Historical Price History Table
CREATE TABLE IF NOT EXISTS price_history (
    ticker      VARCHAR(10)   NOT NULL,
    date        DATE          NOT NULL,
    open_price  DECIMAL(10,2) NOT NULL,
    high_price  DECIMAL(10,2) NOT NULL,
    low_price   DECIMAL(10,2) NOT NULL,
    close_price DECIMAL(10,2) NOT NULL,
    volume      BIGINT        NULL,
    PRIMARY KEY (ticker, date),
    FOREIGN KEY (ticker) REFERENCES stocks(ticker) ON UPDATE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 7. Benchmark Rates Table
CREATE TABLE IF NOT EXISTS benchmark_rates (
    date         DATE          NOT NULL,
    rate_type    VARCHAR(20)   NOT NULL DEFAULT 'DGS3MO',
    annual_rate  DECIMAL(6,4)  NOT NULL,
    PRIMARY KEY (date, rate_type)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 8. Backtests Table
CREATE TABLE IF NOT EXISTS backtests (
    backtest_id          INT            NOT NULL AUTO_INCREMENT,
    portfolio_id         INT            NOT NULL,
    strategy_type        ENUM('THRESHOLD', 'CALENDAR', 'HYBRID') NOT NULL,
    start_date           DATE           NOT NULL,
    end_date             DATE           NOT NULL,
    initial_value        DECIMAL(15,2)  NOT NULL,
    final_value          DECIMAL(15,2)  NOT NULL,
    total_return         DECIMAL(8,4)   NOT NULL,
    xirr                 DECIMAL(8,4)   NOT NULL,
    max_drawdown         DECIMAL(8,4)   NOT NULL,
    total_turnover       DECIMAL(15,2)  NOT NULL,
    rebalance_count      INT            NOT NULL,
    result_summary_json  JSON           NOT NULL,
    created_at           DATETIME       NOT NULL DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (backtest_id),
    FOREIGN KEY (portfolio_id) REFERENCES portfolios(portfolio_id) ON DELETE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

3. Create a seed data script `backend/app/db/seed_stocks.py` to insert starter tickers into `stocks`:
   - Stocks: `AAPL`, `MSFT`, `GOOGL`, `AMZN`, `NVDA`, `TSLA`
   - ETFs: `SPY`, `QQQ`, `VTI`, `BND`, `GLD`
4. Add verification commands in the terminal to confirm table creation on GCP Cloud SQL.
```

---

### 🔹 Prompt 2: User Authentication & JWT with Native SQL
*Copy and paste the prompt below:*

```markdown
You are the Database Backend Lead for RebalanceLab (CS 411).
Your task is to implement User Registration, Login, and JWT Token Authentication using **parameterized native SQL queries**.

### ⚠️ Strict Rule:
Do NOT use an ORM query builder. Write raw SQL using SQLAlchemy Core `text("""...""")` with parameters like `:username`.

### Requirements:
1. Install dependencies if needed: `passlib[bcrypt]`, `python-jose` (or `PyJWT`), `python-multipart`.
2. Create `backend/app/repositories/user_repo.py`:
   - `create_user(username, email, password_hash)`:
     ```sql
     INSERT INTO users (username, email, password_hash) VALUES (:username, :email, :password_hash);
     ```
   - `get_user_by_username(username)`:
     ```sql
     SELECT user_id, username, email, password_hash, created_at FROM users WHERE username = :username;
     ```
   - `get_user_by_id(user_id)`:
     ```sql
     SELECT user_id, username, email, created_at FROM users WHERE user_id = :user_id;
     ```
3. Create `backend/app/core/security.py`:
   - `hash_password(password: str) -> str`
   - `verify_password(plain_password: str, hashed_password: str) -> bool`
   - `create_access_token(data: dict) -> str`
   - FastAPI dependency `get_current_user(token: str)` returning the authenticated `user_id`.
4. Create `backend/app/routers/auth.py` with endpoints:
   - `POST /api/auth/register` (body: `username`, `email`, `password`)
   - `POST /api/auth/login` (OAuth2 / JSON body: `username`, `password` -> returns `access_token`, `token_type`)
   - `GET /api/auth/me` (requires Bearer token -> returns user profile)
5. Register the router in `backend/app/main.py`.
```

---

### 🔹 Prompt 3: Portfolio, Holdings & Transactions CRUD (Native SQL)
*Copy and paste the prompt below:*

```markdown
You are the Database Backend Lead for RebalanceLab (CS 411).
Your task is to implement the CRUD endpoints for Portfolios, Holdings, and Transactions using **parameterized native SQL queries**.

### Requirements:
1. Create `backend/app/repositories/portfolio_repo.py`:
   - `list_portfolios(user_id: int)`:
     ```sql
     SELECT p.portfolio_id, p.name, p.initial_cash, p.cash_balance, p.created_at,
            COUNT(h.holding_id) AS total_holdings
     FROM portfolios p
     LEFT JOIN holdings h ON p.portfolio_id = h.portfolio_id
     WHERE p.user_id = :user_id
     GROUP BY p.portfolio_id, p.name, p.initial_cash, p.cash_balance, p.created_at;
     ```
   - `create_portfolio(user_id: int, name: str, initial_cash: float)`
   - `get_portfolio(portfolio_id: int, user_id: int)`
   - `update_portfolio(portfolio_id: int, user_id: int, name: str)`
   - `delete_portfolio(portfolio_id: int, user_id: int)`

2. Create `backend/app/repositories/holding_repo.py`:
   - `get_holdings(portfolio_id: int)`:
     ```sql
     SELECT h.holding_id, h.portfolio_id, h.ticker, s.name AS stock_name,
            h.current_shares, h.average_buy_price, h.target_weight
     FROM holdings h
     JOIN stocks s ON h.ticker = s.ticker
     WHERE h.portfolio_id = :portfolio_id;
     ```
   - `upsert_holding(portfolio_id: int, ticker: str, target_weight: float, current_shares: float, avg_price: float)`
   - `delete_holding(portfolio_id: int, holding_id: int)`

3. Create `backend/app/repositories/transaction_repo.py`:
   - `add_transaction(portfolio_id: int, type: str, total_amount: float, ticker: str = None, shares: float = None, price: float = None, notes: str = None)`:
     - Wrap in a native SQL transaction (`conn.begin()`):
       - Insert into `transactions`.
       - If `type == 'DEPOSIT'`, increment `cash_balance`.
       - If `type == 'WITHDRAWAL'`, decrement `cash_balance` (ensure cash >= 0).
   - `get_transactions(portfolio_id: int)`

4. Create `backend/app/routers/portfolio.py` exposing:
   - `GET /api/portfolios` & `POST /api/portfolios`
   - `GET /api/portfolios/{id}`, `PUT /api/portfolios/{id}`, `DELETE /api/portfolios/{id}`
   - `GET /api/portfolios/{id}/holdings` & `POST /api/portfolios/{id}/holdings`
   - `POST /api/portfolios/{id}/transactions` & `GET /api/portfolios/{id}/transactions`
```

---

### 🔹 Prompt 4: Stock & Security Search API
*Copy and paste the prompt below:*

```markdown
You are the Database Backend Lead for RebalanceLab (CS 411).
Your task is to implement the Stock and ETF Search endpoint using **parameterized native SQL**.

### Requirements:
1. Create `backend/app/repositories/stock_repo.py`:
   - `search_stocks(query_str: str, limit: int = 10)`:
     ```sql
     SELECT ticker, name, asset_type, exchange, currency
     FROM stocks
     WHERE is_active = TRUE
       AND (ticker LIKE :pattern OR name LIKE :pattern)
     ORDER BY (ticker = :exact) DESC, ticker ASC
     LIMIT :limit;
     ```
     Pass `pattern = f"%{query_str}%"` and `exact = query_str.upper()`.
2. Create `backend/app/routers/stocks.py`:
   - `GET /api/stocks/search?q={query}`
   - Return list of matching stocks/ETFs formatted as JSON.
3. Test with queries like `AAPL`, `Apple`, `SPY`, `Index`.
```
