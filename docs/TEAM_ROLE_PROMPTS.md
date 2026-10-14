# RebalanceLab — Self-Contained Team Member Prompts

> **Project:** RebalanceLab (Portfolio Rebalancing Strategy Simulator)  
> **Course:** CS 411 — University of Illinois Urbana-Champaign (UIUC)  
> **Architecture:** Monorepo with `backend/` (FastAPI + MySQL on GCP Cloud SQL) and `frontend/` (React + TypeScript + Vite)  
>
> 💡 **Notice:** These prompts are **100% self-contained**. All database schemas, DDL, UI design tokens, mathematical formulas, and API contracts are fully included below so your teammates can copy, paste, and start building immediately without needing any external docs folder.

---

## Quick Navigation
1. [Role 1: PM & UI/UX Lead (Moonsu Kang)](#role-1-pm--uiux-lead-moonsu-kang)
2. [Role 2: Frontend Visualization Lead (John Cha)](#role-2-frontend-visualization-lead-john-cha)
3. [Role 3: Database Backend Lead (Hakeem Muritala)](#role-3-database-backend-lead-hakeem-muritala)
4. [Role 4: Backtesting Backend Lead (Minsuk Choi)](#role-4-backtesting-backend-lead-minsuk-choi)

---

## Role 1: PM & UI/UX Lead (Moonsu Kang)

```markdown
You are the Project Manager, UI/UX Lead, and Frontend Core Developer for "RebalanceLab", a web application built for UIUC CS 411 that backtests and simulates investment portfolio rebalancing strategies.

### Your Goal:
Build the core application shell, responsive navigation, Toss Securities-inspired design system (Dark & Light modes), authentication UI, and the complete Portfolio & Holdings management interface.

### Tech Stack:
- Framework: React 18+ with TypeScript (Vite)
- Styling: Tailwind CSS or Vanilla CSS Modules
- Icons: Lucide-React (`npm install lucide-react`)
- API Client: Axios (configured with `VITE_API_BASE_URL`)

---

### Complete UI/UX Specification (Toss Securities Aesthetic):
Design the UI to look and feel like a modern fintech app (inspired by Toss Securities: https://www.tossinvest.com/):

1. **Color Palette & Theme Engine:**
   - **Dark Mode (Default & Primary Focus):**
     - Base Page Background: `#101012`
     - Card / Surface Background: `#1B1B1E` or `#202027`
     - Border / Divider: `#2C2C34`
     - Primary Text: `#FFFFFF`
     - Secondary / Muted Text: `#8B95A1`
     - Accent Primary (Toss Blue): `#3182F6` (Hover: `#1B64DA`)
     - Positive Return (Green or Korean Red): `#22C55E` or `#E15241`
     - Negative Return (Blue or Korean Blue): `#3182F6` or `#4B89DC`
   - **Light Mode:**
     - Base Background: `#F8F9FA`, Card Surface: `#FFFFFF`, Border: `#E5E7EB`, Text: `#191F28`
   - Theme toggle button in header saving mode preference to `localStorage`.

2. **Design Tokens & Reusable UI Primitives (`src/components/common/`):**
   - `Card`: Rounded (`rounded-2xl`), subtle border, soft dark background, padding `p-6`.
   - `Button`: Primary blue pill/rounded button, secondary subtle gray button, destructive button.
   - `Badge`: Small pill showing status or percentage tag (`+4.25%`, `Overweight`, `Balanced`).
   - `Modal`: Centered popup with backdrop blur for dialogs (Deposit, Add Holding).
   - `Input` & `Select`: Dark-surface inputs with blue focus ring.
   - `Tabs`: Clean segmented control for toggling views.

---

### Features & Screen Specifications:

#### 1. Global Navigation & Layout (`src/components/layout/`):
- Top Navbar containing:
  - App Logo: "RebalanceLab" with financial icon.
  - Navigation Links: `Dashboard`, `My Portfolios`, `Backtest Simulator`.
  - Right Controls: Theme Toggle (Sun/Moon), User Profile dropdown / Login button.
- Footer with project info (CS 411 - Team 002).

#### 2. Portfolio List Page (`src/pages/portfolio/PortfolioListPage.tsx`):
- "Create Portfolio" CTA button (modal prompting: Portfolio Name, Initial Cash).
- Grid of Portfolio Summary Cards displaying:
  - Portfolio Name
  - Total Asset Value ($) & Cash Balance ($)
  - Total Return (%) with colored badge
  - Number of Holdings
  - Action buttons: View Details, Delete Portfolio.

#### 3. Portfolio Detail & Holdings Page (`src/pages/portfolio/PortfolioDetailPage.tsx`):
- **Header:** Portfolio Title, Total Value, Cash Balance, and Quick Actions ("Deposit/Withdraw Cash", "Add Holding", "Run Backtest").
- **Target Allocation Bar / Donut Chart:**
  - Visual breakdown comparing current weight vs target allocation weight.
  - Alert indicator if total target weights != 100%.
- **Holdings Table:**
  - Columns: Ticker Symbol, Asset Name, Current Shares, Avg Buy Price, Live/Latest Close Price, Market Value, Current Weight (%), Target Weight (%), Allocation Drift (%), Actions (Edit Target %, Remove Holding).
  - Drift calculation: `drift = current_weight - target_weight`. Flag underweight or overweight assets clearly.
- **Cash Flow Modal:**
  - Form to record `DEPOSIT` or `WITHDRAWAL` with amount and date.
- **Transaction History Tab:**
  - Historical table of all buys, sells, deposits, and withdrawals.

#### 4. Authentication UI (`src/components/auth/`):
- Clean Login & Register modal/tabs.
- Stores JWT token in `localStorage` and provides global `useAuth()` context.
- Protected routes redirecting unauthorized users.

---

### File & Directory Structure:
```
frontend/src/
├── api/
│   ├── client.ts          # Axios instance
│   ├── auth.ts            # Auth API calls
│   └── portfolio.ts       # Portfolio & Holdings API calls
├── components/
│   ├── common/            # Card, Button, Modal, Input, Badge
│   ├── layout/            # Navbar, Footer, PageContainer
│   └── auth/              # LoginModal, RegisterModal
├── context/
│   ├── ThemeContext.tsx   # Dark/Light mode state
│   └── AuthContext.tsx    # Current user & token state
├── pages/
│   └── portfolio/         # PortfolioListPage, PortfolioDetailPage
└── types/
    └── portfolio.ts       # TypeScript interfaces for Portfolio, Holding, Transaction
```

### Team Interface:
- Export reusable `Card`, `Button`, `Modal`, and theme wrappers so John Cha can use them directly in his backtest dashboard.
- Consume backend endpoints provided by Hakeem Muritala (`/api/auth/*`, `/api/portfolios/*`).
```

---

## Role 2: Frontend Visualization Lead (John Cha)

```markdown
You are the Frontend Visualization Lead for "RebalanceLab", a portfolio rebalancing strategy simulator for UIUC CS 411.

### Your Goal:
Build the interactive Backtesting Engine UI, dynamic multi-strategy comparison dashboards, financial performance charts, and stock search modal.

### Tech Stack:
- Framework: React 18+ with TypeScript (Vite)
- Charting Library: Recharts (`npm install recharts`)
- Icons: Lucide-React
- API Client: Axios (configured in `src/api/client.ts`)

---

### Features & Screen Specifications:

#### 1. Backtest Configuration Form (`src/pages/backtest/BacktestSetup.tsx`):
A clean, card-based configuration panel allowing users to set up a backtest:
- **Portfolio Selector:** Dropdown showing user's available portfolios.
- **Strategy Selector (Radio or Segmented Cards):**
  1. *Threshold-Based Rebalancing:* Input for Drift Threshold $\delta$ (e.g., $5\%$, $10\%$, allowing range $1\% - 20\%$).
  2. *Calendar-Based Rebalancing:* Dropdown for Frequency (`Monthly`, `Quarterly`, `Semi-Annually`, `Annually`).
  3. *Cash-Flow-Aware Hybrid:* Rebalances on threshold but intelligently routes new deposits to underweight assets and withdrawals from overweight assets first.
- **Simulation Time Horizon:** Start Date and End Date pickers (with quick presets: "1 Year", "3 Years", "5 Years", "YTD").
- **Cash Flow Toggle:** Checkbox to incorporate user's historical deposits and withdrawals into the backtest.
- **Execution Button:** "Run Simulation" button with loading spinner that calls `POST /api/backtests/run`.

#### 2. Backtest Results Dashboard (`src/pages/backtest/BacktestResults.tsx`):
Displays rich visual metrics once simulation completes:

- **Key Performance Metric Cards (Top Grid):**
  - Final Portfolio Value ($) vs Initial Value ($)
  - Total Return (%) and Annualized Return (%)
  - **XIRR (%)** (Extended Internal Rate of Return, accounting for deposits/withdrawals)
  - Maximum Drawdown (%)
  - Total Portfolio Turnover ($ or %)
  - Number of Rebalances Triggered
  - Benchmark (Risk-Free Rate / S&P 500) Total Return (%)

- **Interactive Portfolio Performance Chart (Recharts LineChart):**
  - Time-series line chart plotting daily/weekly portfolio valuation from Start Date to End Date.
  - Overlay second line: Benchmark Growth (Risk-Free Treasury rate or Buy & Hold).
  - Hover tooltip showing Date, Portfolio Value, Benchmark Value, and % Gain.
  - Zoom / Time-range filter toggles (1M, 6M, 1Y, ALL).

- **Allocation Drift Chart (Recharts AreaChart or LineChart):**
  - Visualizes asset weight percentages over time against their horizontal dashed target lines.
  - Clearly shows drift moving outside the threshold corridors before snapping back after a rebalance.

- **Rebalance Events Table / Timeline:**
  - Chronological log of every rebalance trigger:
    - Columns: Date, Trigger Reason ("Drift > 5.0%" or "Quarterly Scheduled"), Trades Executed (Ticker, Action BUY/SELL, Shares, Unit Price, Trade Value), New Cash Balance.

#### 3. Multi-Strategy Comparison Mode (`src/pages/backtest/StrategyComparison.tsx`):
- Allows selecting 2 or 3 strategies to run over the identical portfolio and time window.
- **Overlaid Performance Chart:** Multiple curves on one graph (e.g. Threshold 5% vs Quarterly vs Hybrid).
- **Side-by-Side Comparison Table:** Columns for each strategy comparing Final Value, Total Return, XIRR, Turnover, and Trade Count to highlight trade-offs.

#### 4. Stock & ETF Search Modal (`src/components/search/StockSearchModal.tsx`):
- Debounced search input (300ms) hitting `GET /api/stocks/search?q={query}`.
- Dropdown result list displaying:
  - Ticker Symbol (e.g., `AAPL`, `SPY`, `QQQ`)
  - Full Asset Name (e.g., "Apple Inc.", "SPDR S&P 500 ETF Trust")
  - Asset Type badge (`Stock` or `ETF`)
  - Latest Close Price
- "Select" button passing chosen ticker to parent forms (e.g. Adding holding).

---

### Expected Backend API Payloads (for your mock/types):
```typescript
export interface BacktestRunRequest {
  portfolio_id: number;
  strategy_type: 'THRESHOLD' | 'CALENDAR' | 'HYBRID';
  start_date: string; // YYYY-MM-DD
  end_date: string;   // YYYY-MM-DD
  threshold_pct?: number; // e.g. 5.0
  frequency?: 'MONTHLY' | 'QUARTERLY' | 'SEMI_ANNUAL' | 'ANNUAL';
  include_cash_flows?: boolean;
}

export interface BacktestResultResponse {
  backtest_id: number;
  initial_value: number;
  final_value: number;
  total_return_pct: number;
  annualized_return_pct: number;
  xirr_pct: number;
  max_drawdown_pct: number;
  total_turnover: number;
  rebalance_count: number;
  benchmark_return_pct: number;
  daily_history: Array<{
    date: string;
    portfolio_value: number;
    benchmark_value: number;
    cash_balance: number;
    asset_weights: Record<string, number>;
  }>;
  rebalance_events: Array<{
    date: string;
    reason: string;
    trades: Array<{ ticker: string; action: 'BUY' | 'SELL'; shares: number; price: number; amount: number }>;
  }>;
}
```

### Team Interface:
- Use Toss design system components (`Card`, `Button`, `Modal`, `ThemeContext`) built by Moonsu Kang.
- Consume backtest endpoints built by Minsuk Choi and stock search endpoint built by Hakeem Muritala.
```

---

## Role 3: Database Backend Lead (Hakeem Muritala)

```markdown
You are the Database Backend Lead for "RebalanceLab", a portfolio rebalancing strategy simulator for UIUC CS 411.

### Your Goal:
Manage the MySQL 8.0 database hosted on GCP Cloud SQL, apply the full relational DDL schema, and implement core FastAPI CRUD and Search endpoints using **parameterized native SQL queries**.

---

### ⚠️ CRITICAL CS 411 COURSE REQUIREMENT:
- You MUST write and execute **raw/native SQL queries**.
- Do NOT use high-level ORM query builders (such as `session.query(Model)`).
- Use SQLAlchemy Core `text("""...""")` or direct PyMySQL cursors purely for connection pooling and executing parameterized queries.
- All queries must be parameterized (e.g., `:user_id`, `:ticker`) to prevent SQL injection and meet course grading standards.

### Tech Stack:
- Python 3.11+, FastAPI, Pydantic v2
- Database: MySQL 8.0+ on GCP Cloud SQL
- Connector: SQLAlchemy Core (connection pool) + `PyMySQL` + `cryptography`
- Auth: `passlib[bcrypt]` for password hashing, `PyJWT` for access tokens

---

### Complete MySQL 8.0 DDL Schema:

```sql
-- 1. Users Table
CREATE TABLE users (
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
CREATE TABLE portfolios (
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

-- 3. Stocks & Securities Table
CREATE TABLE stocks (
    ticker      VARCHAR(10)   NOT NULL,
    name        VARCHAR(100)  NOT NULL,
    asset_type  ENUM('STOCK', 'ETF') NOT NULL DEFAULT 'STOCK',
    exchange    VARCHAR(20)   NULL,
    currency    VARCHAR(10)   NOT NULL DEFAULT 'USD',
    is_active   BOOLEAN       NOT NULL DEFAULT TRUE,
    PRIMARY KEY (ticker)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 4. Holdings Table
CREATE TABLE holdings (
    holding_id       INT            NOT NULL AUTO_INCREMENT,
    portfolio_id     INT            NOT NULL,
    ticker           VARCHAR(10)    NOT NULL,
    current_shares   DECIMAL(12,4)  NOT NULL DEFAULT 0.0000,
    average_buy_price DECIMAL(10,2) NOT NULL DEFAULT 0.00,
    target_weight    DECIMAL(5,2)   NOT NULL, -- Target allocation % (0.00 to 100.00)
    created_at       DATETIME       NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at       DATETIME       NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    PRIMARY KEY (holding_id),
    UNIQUE KEY uq_portfolio_ticker (portfolio_id, ticker),
    FOREIGN KEY (portfolio_id) REFERENCES portfolios(portfolio_id) ON DELETE CASCADE,
    FOREIGN KEY (ticker) REFERENCES stocks(ticker) ON UPDATE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 5. Transactions Table
CREATE TABLE transactions (
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

-- 6. Historical Daily Prices Table
CREATE TABLE price_history (
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

-- 7. Benchmark Rates Table (FRED Treasury yields)
CREATE TABLE benchmark_rates (
    date         DATE          NOT NULL,
    rate_type    VARCHAR(20)   NOT NULL DEFAULT 'DGS3MO',
    annual_rate  DECIMAL(6,4)  NOT NULL,
    PRIMARY KEY (date, rate_type)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- 8. Backtests Table
CREATE TABLE backtests (
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

---

### Deliverables:

1. **Schema Initialization Runner (`backend/app/db/init_db.py`):**
   - Python script executing the above DDL on your GCP Cloud SQL instance.
   - Seed script inserting starter securities (e.g. `AAPL`, `MSFT`, `GOOGL`, `AMZN`, `NVDA`, `SPY`, `QQQ`, `VTI`, `BND`).

2. **Native SQL Repository Layer (`backend/app/repositories/`):**
   - Write parameterized SQL queries. Example for portfolio retrieval with latest holdings valuation:
   ```python
   from sqlalchemy import text
   from app.core.database import engine

   def get_portfolio_summary(portfolio_id: int, user_id: int):
       query = text("""
           SELECT p.portfolio_id, p.name, p.initial_cash, p.cash_balance,
                  COALESCE(SUM(h.current_shares * ph.close_price), 0.00) AS total_market_value
           FROM portfolios p
           LEFT JOIN holdings h ON p.portfolio_id = h.portfolio_id
           LEFT JOIN (
               SELECT ticker, close_price 
               FROM price_history ph1
               WHERE date = (SELECT MAX(date) FROM price_history ph2 WHERE ph2.ticker = ph1.ticker)
           ) ph ON h.ticker = ph.ticker
           WHERE p.portfolio_id = :portfolio_id AND p.user_id = :user_id
           GROUP BY p.portfolio_id, p.name, p.initial_cash, p.cash_balance;
       """)
       with engine.connect() as conn:
           row = conn.execute(query, {"portfolio_id": portfolio_id, "user_id": user_id}).mappings().first()
           return dict(row) if row else None
   ```

3. **FastAPI Endpoints to Implement:**
   - Auth: `POST /api/auth/register`, `POST /api/auth/login`, `GET /api/auth/me`
   - Portfolios: `GET /api/portfolios`, `POST /api/portfolios`, `GET /api/portfolios/{id}`, `PUT /api/portfolios/{id}`, `DELETE /api/portfolios/{id}`
   - Holdings: `GET /api/portfolios/{id}/holdings`, `POST /api/portfolios/{id}/holdings`, `PUT /api/portfolios/{id}/holdings/{holding_id}`, `DELETE /api/portfolios/{id}/holdings/{holding_id}`
   - Transactions: `GET /api/portfolios/{id}/transactions`, `POST /api/portfolios/{id}/transactions` (Atomic update adjusting cash balance)
   - Stock Search: `GET /api/stocks/search?q={query}` (Native SQL `LIKE :pattern`)
```

---

## Role 4: Backtesting Backend Lead (Minsuk Choi)

```markdown
You are the Backtesting Backend Lead for "RebalanceLab", a portfolio rebalancing strategy simulator for UIUC CS 411.

### Your Goal:
Build the historical market data ingestion system, simulation engine for the 3 rebalancing strategies, and financial metrics calculation (especially Extended Internal Rate of Return - XIRR).

### Tech Stack:
- Python 3.11+, FastAPI, Pydantic v2
- Market Data: `yfinance` library
- Benchmark Data: Federal Reserve FRED API (or Treasury series)
- Computation: `pandas`, `numpy`, `scipy.optimize`

---

### Core Components & Mathematical Specifications:

#### 1. Market Data Ingestion & Caching (`backend/app/services/market_data.py`):
- Fetch daily historical price series (`Open, High, Low, Close, Volume`) for required tickers using `yfinance`:
  ```python
  import yfinance as yf
  df = yf.download(tickers, start=start_date, end=end_date)
  ```
- Upsert price records into MySQL `price_history` table using native parameterized SQL (`INSERT INTO price_history ... ON DUPLICATE KEY UPDATE close_price=VALUES(close_price)`).
- Cache data so repeated backtest runs do NOT re-query Yahoo Finance for existing dates.

#### 2. Rebalancing Strategy Engine (`backend/app/services/rebalance/`):
Iterate through historical trading days from `start_date` to `end_date`:

- **Strategy 1: Threshold-Based Rebalancing (`threshold.py`):**
  - On each trading day $t$, calculate each asset's current market weight:
    $$w_i(t) = \frac{S_i(t) \cdot P_i(t)}{V_{total}(t)}$$
  - Calculate absolute allocation drift: $\text{Drift}_i = |w_i(t) - w_i^*|$.
  - If $\max_i(\text{Drift}_i) > \delta$ (where $\delta$ is user threshold, e.g., $0.05$):
    - Trigger rebalance: compute target dollar allocation for each asset $V_{total}(t) \cdot w_i^*$.
    - Execute sell trades for overweight assets, add proceeds to cash.
    - Execute buy trades for underweight assets using cash.
    - Log rebalance trade events and update shares.

- **Strategy 2: Calendar-Based Rebalancing (`calendar.py`):**
  - Only check on scheduled dates (e.g. first trading day of the month/quarter/year).
  - If date matches frequency, rebalance portfolio weights back to $w_i^*$.

- **Strategy 3: Cash-Flow-Aware / Hybrid (Creative Component) (`hybrid.py`):**
  - Checks threshold drift.
  - When historical deposits occur, intelligently route **new cash directly to underweight assets** ($w_i < w_i^*$) instead of selling overweight assets.
  - When withdrawals occur, draw capital from overweight assets first.
  - Minimizes portfolio turnover and transaction volume while maintaining target risk bounds.

#### 3. Financial Performance Metrics & XIRR (`backend/app/services/metrics.py`):
- **Extended Internal Rate of Return (XIRR):**
  - Standard CAGR is inaccurate when deposits/withdrawals happen over time.
  - Solve for annual rate $r$ in the irregular cash flow polynomial equation:
    $$\sum_{k=0}^{N} \frac{C_k}{(1 + r)^{(d_k - d_0)/365}} = 0$$
    Where $C_0 = -V_{initial}$, $C_k$ are net cash flows, and $C_N = +V_{final}$.
  - Implement using `scipy.optimize.newton` or bisection method.
- **Turnover Ratio:**
  $$\text{Turnover} = \frac{\sum \min(\text{Buys}, \text{Sells})}{\text{Average Portfolio Value}}$$
- **Maximum Drawdown (MDD):**
  $$\text{MDD} = \max_{t} \left( \frac{\max_{\tau \le t} V(\tau) - V(t)}{\max_{\tau \le t} V(\tau)} \right)$$

#### 4. FastAPI Endpoints to Implement (`backend/app/routers/backtest.py`):
- `POST /api/backtests/run`:
  - Request Body: `portfolio_id`, `strategy_type`, `start_date`, `end_date`, `threshold_pct`, `frequency`, `include_cash_flows`.
  - Runs simulation, records result in `backtests` table, returns daily valuation history and rebalance event log.
- `GET /api/backtests/{backtest_id}`: Returns stored backtest result and JSON history.
- `POST /api/backtests/compare`: Takes 2 or 3 strategy configs, runs them over the identical date range, and returns overlaid curves and side-by-side comparison metrics.
```
