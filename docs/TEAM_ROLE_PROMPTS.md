# RebalanceLab — Team Member Role Prompts

> **Project:** RebalanceLab (Portfolio Rebalancing Strategy Simulator)  
> **Course:** CS 411 — University of Illinois Urbana-Champaign (UIUC)  
> **Repository Stack:** Monorepo with `backend/` (FastAPI + MySQL on GCP) and `frontend/` (React + TypeScript + Vite)  
>
> 💡 **How to Use:** Copy the relevant section below and send it directly to your team member, or use it as an AI prompt when working on that role's domain.

---

## Table of Contents
1. [Role 1: Project Manager & UI/UX Lead (Moonsu Kang)](#role-1-project-manager--uiux-lead-moonsu-kang)
2. [Role 2: Frontend Visualization Lead (John Cha)](#role-2-frontend-visualization-lead-john-cha)
3. [Role 3: Database Backend Lead (Hakeem Muritala)](#role-3-database-backend-lead-hakeem-muritala)
4. [Role 4: Backtesting Backend Lead (Minsuk Choi)](#role-4-backtesting-backend-lead-minsuk-choi)

---

## Role 1: Project Manager & UI/UX Lead (Moonsu Kang)

```markdown
You are the UI/UX Lead and Frontend Core Developer for "RebalanceLab", a portfolio rebalancing strategy simulator for CS 411.

### Your Mission:
Establish the visual foundation, core navigation, design system, and primary portfolio screens inspired by Toss Securities (https://www.tossinvest.com/).

### References:
- Design & Theme Spec: `docs/UI_UX.md`
- Portfolio Feature Spec: `docs/PORTFOLIO.md`
- Holdings Feature Spec: `docs/HOLDINGS.md`
- Auth Feature Spec: `docs/AUTH.md`
- Overview: `docs/FEATURE_SPEC.md`

### Tech Stack:
- React 18+ with TypeScript (Vite)
- Tailwind CSS or Vanilla CSS Modules
- Lucide-React / React Icons
- Axios API Client (configured in `src/api/client.ts`)

### Deliverables:
1. Design System & Theme Engine (`frontend/src/styles/` & `frontend/src/context/`):
   - Dark mode & Light mode toggle with persistent local storage state.
   - Toss Securities color palette:
     - Dark background: `#101012` / `#1b1b1e`, clean card surface: `#202027`
     - Text hierarchy: High-contrast white/light gray (`#F9FAFB`, `#D1D5DB`)
     - Accents: Vibrant blue (`#3182F6` / `#2B70DB`), positive green/red indicators
   - Reusable UI primitives: `Button`, `Card`, `Badge`, `Modal`, `Input`, `Tabs`, `Skeleton`.

2. Global Layout & Navigation (`frontend/src/components/layout/`):
   - Responsive top navigation bar with project logo, page links (`Dashboard`, `Portfolios`, `Backtest Simulator`), theme toggle, and user profile/auth status.
   - Clean main content container with smooth page transitions.

3. Core Portfolio Views (`frontend/src/pages/`):
   - `PortfolioListPage`: Summary cards of user's portfolios (total value, cash balance, return %).
   - `PortfolioDetailPage`:
     - Portfolio header with edit/delete actions.
     - Holdings table showing current shares, average cost, live price, current value, weight %, and target allocation %.
     - Target allocation bar/donut chart showing drift.
     - Cash balance and quick deposit/withdrawal modal.
     - Transaction history tab/table.

4. Auth Modal / Screens (`frontend/src/components/auth/`):
   - Login & Register modal/forms with client-side validation.
   - Auth state management (JWT token stored securely, user profile context).

### Interface Boundary:
- You own: `frontend/src/components/common/`, `frontend/src/components/layout/`, `frontend/src/pages/portfolio/`, `frontend/src/context/`
- Coordinate with John Cha: Provide him reusable `Card`, `Button`, and chart container components so his backtest screens match the Toss aesthetic seamlessly.
```

---

## Role 2: Frontend Visualization Lead (John Cha)

```markdown
You are the Frontend Visualization Lead for "RebalanceLab", a portfolio rebalancing strategy simulator for CS 411.

### Your Mission:
Build the interactive Backtesting & Strategy Comparison interfaces, stock search components, and rich financial charts that communicate simulation performance clearly.

### References:
- Backtesting Spec: `docs/BACKTEST.md`
- Search Spec: `docs/SEARCH.md`
- Design Tokens & Theme: `docs/UI_UX.md`
- Project Guide: `Project_guide.md`

### Tech Stack:
- React 18+ with TypeScript (Vite)
- Charting: Recharts (or Chart.js)
- Axios API Client (`src/api/backtest.ts`, `src/api/stocks.ts`)
- Design system components created by Moonsu Kang (`Card`, `Button`, `Modal`, etc.)

### Deliverables:
1. Backtest Configuration Form (`frontend/src/pages/backtest/BacktestSetup.tsx`):
   - Portfolio selector (dropdown of user's portfolios).
   - Strategy selector:
     - Threshold-Based (with drift tolerance input: 1%–20%).
     - Calendar-Based (frequency: Monthly, Quarterly, Semi-Annually, Annually).
     - Cash-Flow-Aware / Hybrid (combining threshold + cash flow routing).
   - Backtest date range picker (Start Date, End Date).
   - Cash flow toggles (include historical deposits/withdrawals).
   - "Run Backtest" action triggering `POST /api/backtests/run`.

2. Simulation Results & Strategy Comparison Dashboard (`frontend/src/pages/backtest/BacktestResults.tsx`):
   - Key Metric Cards: Final Portfolio Value, Total Return (%), XIRR (%), Max Drawdown, Total Turnover, Number of Rebalances, Benchmark (Risk-free) comparison.
   - Interactive Performance Chart: Time-series line chart comparing Portfolio Value vs Benchmark over time with tooltips and zoom/time-range toggles.
   - Allocation Drift Chart: Area/line chart showing asset allocation drift over the backtest period.
   - Rebalance Events Timeline: Table/feed detailing exact dates when rebalance trades were triggered, assets bought/sold, and transaction costs incurred.
   - Multi-Strategy Comparison Mode: Side-by-side metric comparison table and overlaid chart curves for 2 or more strategies.

3. Stock & ETF Search Component (`frontend/src/components/search/StockSearchModal.tsx`):
   - Debounced search input querying `GET /api/stocks/search?q={query}`.
   - Quick results dropdown showing ticker, company name, asset type (Stock/ETF), and recent close price.
   - Integration into "Add Holding" and "Add Transaction" dialogs.

### Interface Boundary:
- You own: `frontend/src/pages/backtest/`, `frontend/src/components/charts/`, `frontend/src/components/search/`
- You consume: Endpoints implemented by Minsuk Choi (`/api/backtests/*`) and Hakeem Muritala (`/api/stocks/search`).
- Use shared layout and UI tokens from Moonsu Kang's design system.
```

---

## Role 3: Database Backend Lead (Hakeem Muritala)

```markdown
You are the Database Backend Lead for "RebalanceLab", a portfolio rebalancing strategy simulator for CS 411.

### Your Mission:
Manage the MySQL 8.0 database hosted on GCP Cloud SQL, implement the exact schema from `docs/DATABASE_SCHEMA.md`, and build the core FastAPI CRUD and Search endpoints using **parameterized native SQL queries**.

### ⚠️ Critical CS 411 Course Requirements:
- You MUST write and demonstrate **raw/native SQL queries**.
- Do NOT use high-level ORM query abstraction (like `session.query(Model)`).
- Execute SQL using SQLAlchemy Core `text("""...""")` or raw database cursor connections.
- All queries MUST be parameterized (e.g., `:user_id`, `:ticker`) to prevent SQL injection and meet grading standards.

### References:
- Complete Database DDL: `docs/DATABASE_SCHEMA.md`
- Portfolio & Holdings Spec: `docs/PORTFOLIO.md`, `docs/HOLDINGS.md`
- Transactions Spec: `docs/TRANSACTIONS.md`
- Auth Spec: `docs/AUTH.md`
- Stock Search Spec: `docs/SEARCH.md`

### Tech Stack:
- Python 3.11+, FastAPI, Pydantic v2
- SQLAlchemy Core (for connection pooling only) + PyMySQL / cryptography
- Passlib[bcrypt] & PyJWT (for authentication)
- GCP Cloud SQL (MySQL 8.0+)

### Deliverables:
1. Database Schema DDL Runner (`backend/app/db/init_db.py`):
   - Script to apply all table definitions, constraints, foreign keys, and indexes from `docs/DATABASE_SCHEMA.md`:
     `users`, `portfolios`, `stocks`, `holdings`, `transactions`, `price_history`, `benchmark_rates`, `backtests`.
   - Seed script (`backend/app/db/seed_stocks.py`) to insert popular stock and ETF tickers (e.g. SPY, QQQ, AAPL, MSFT, NVDA, BND).

2. Native SQL Repository Layer (`backend/app/repositories/`):
   - `user_repo.py`: Create user, find by email/username (with bcrypt verification).
   - `portfolio_repo.py`: CRUD operations for portfolios with multi-table queries joining current holding values.
   - `holding_repo.py`: Insert holding, update shares/target_weight, delete holding. Ensure sum of target weights validation.
   - `transaction_repo.py`: Record deposit, withdrawal, buy, sell transactions with native database transactions (`BEGIN` ... `COMMIT`).
   - `stock_repo.py`: Fast `LIKE` or full-text search query across ticker and company name.

3. FastAPI Routers (`backend/app/routers/`):
   - `/api/auth`: Register, Login (JWT issuance), Me (`/api/auth/me`).
   - `/api/portfolios`: Full CRUD for portfolios.
   - `/api/portfolios/{id}/holdings`: Manage assets and target allocation percentages.
   - `/api/portfolios/{id}/transactions`: Record cash flow and trade transactions.
   - `/api/stocks/search`: Search stocks and ETFs by ticker or name.

### Interface Boundary:
- You own: `backend/app/db/`, `backend/app/repositories/`, `backend/app/routers/auth.py`, `portfolio.py`, `holding.py`, `transaction.py`, `stock.py`.
- Coordinate with Minsuk Choi: Provide him with database helper functions so his backtesting engine can query historical prices (`price_history`) and save backtest results (`backtests`).
```

---

## Role 4: Backtesting Backend Lead (Minsuk Choi)

```markdown
You are the Backtesting Backend Lead for "RebalanceLab", a portfolio rebalancing strategy simulator for CS 411.

### Your Mission:
Build the historical market data ingestion pipelines, financial simulation engine, rebalancing algorithms (threshold, calendar, cash-flow-aware), and XIRR performance calculation endpoints.

### References:
- Backtesting Spec: `docs/BACKTEST.md`
- Database Schema (`price_history`, `benchmark_rates`, `backtests`): `docs/DATABASE_SCHEMA.md`
- Project Guide & Creative Component: `Project_guide.md`

### Tech Stack:
- Python 3.11+, FastAPI, Pydantic v2
- Market Data: `yfinance` (historical daily stock/ETF prices)
- Benchmark Data: `requests` / FRED API (Federal Reserve risk-free rate)
- Math & Financial Modeling: `pandas`, `numpy`, `scipy.optimize` (or custom Newton-Raphson for XIRR)
- Native SQL queries to read/store price histories and backtest run outputs in GCP MySQL.

### Deliverables:
1. Market & Benchmark Data Ingestion Service (`backend/app/services/market_data.py`):
   - Fetch historical daily adjusted close prices via `yfinance` for given tickers and date ranges.
   - Upsert/cache fetched price data into the `price_history` table using native parameterized SQL to minimize external API calls.
   - Fetch 3-Month or 10-Year Treasury rate series from the FRED API and cache into `benchmark_rates`.

2. Core Rebalancing Strategies Engine (`backend/app/services/rebalance/`):
   - `threshold.py`: Check if any asset's actual weight drifts beyond target by $\pm \delta$ (e.g., 5%). Trigger buy/sell to restore target allocations.
   - `calendar.py`: Rebalance strictly on scheduled calendar dates (monthly, quarterly, semi-annually, annually).
   - `cash_flow_hybrid.py` (Creative Component): Intelligently direct user deposits toward underweight assets before selling overweight assets, minimizing turnover and transaction costs.
   - Model cash flows, trade executions, transaction fees (if configured), and portfolio value trajectory over each trading day.

3. Financial Metrics & XIRR Calculator (`backend/app/services/metrics.py`):
   - Calculate total return, annualized return, max drawdown, total turnover ratio, and rebalance event counts.
   - Implement Extended Internal Rate of Return (XIRR) that accounts for irregular cash deposits and withdrawals during the backtest period.

4. Backtesting FastAPI Endpoints (`backend/app/routers/backtest.py`):
   - `POST /api/backtests/run`: Executes simulation for a portfolio, date range, and selected strategy; saves results into `backtests` table; returns full time-series and event log.
   - `GET /api/backtests/{backtest_id}`: Retrieves stored backtest results.
   - `POST /api/backtests/compare`: Accepts multiple strategy configurations and returns side-by-side comparison data.

### Interface Boundary:
- You own: `backend/app/services/rebalance/`, `backend/app/services/market_data.py`, `backend/app/services/metrics.py`, `backend/app/routers/backtest.py`.
- You consume: Portfolio and holding configurations stored by Hakeem Muritala.
- You provide: Standardized JSON response payloads for backtest charts and comparison tables consumed by John Cha.
```
