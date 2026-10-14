# Team Execution Order & Collaboration Roadmap

> **Project:** RebalanceLab (Portfolio Rebalancing Strategy Simulator)  
> **Course:** CS 411 — University of Illinois Urbana-Champaign  
> **Purpose:** Sequential execution guide for the 4 team members. Follow these phases so dependencies (database tables, APIs, UI components) are built in the right order.

---

## 🗺️ Master Dependency Map

```
[ Phase 1: Foundations ]
├── Hakeem (Database): Run DDL on GCP Cloud SQL (tables must exist first)
├── Moonsu (UI/UX): Build Toss design tokens, Dark/Light theme, common UI components
├── Minsuk (Backtest): Build yfinance data ingestion & price_history caching
└── John (Visualization): Setup Recharts, Backtest form shell, TypeScript interfaces

[ Phase 2: Core Logic & CRUD ]
├── Hakeem: Implement Auth & Portfolio CRUD APIs with native parameterized SQL
├── Moonsu: Build Portfolio List & Detail pages connected to Hakeem's APIs
├── Minsuk: Implement 3 Rebalancing Strategies (Threshold, Calendar, Hybrid) + XIRR
└── John: Build interactive performance & allocation drift charts

[ Phase 3: Integration & Advanced Features ]
├── Minsuk: Expose /api/backtests/run & save simulation results to GCP MySQL
├── John: Connect Backtest Setup & Comparison Dashboard to Minsuk's API
├── Hakeem: Implement Stock Search API (/api/stocks/search) with native SQL
└── Moonsu & John: Integrate Stock Search Modal & finalize end-to-end UI polish
```

---

## 📅 Member-by-Member Execution Phases

### 1. Hakeem Muritala (Database Backend Lead)
*Prompt file:* `docs/prompts/HAKEEM_DATABASE.md`
- **Order 1 (Highest Priority):** Run DDL initialization script (`init_db.py`) against GCP Cloud SQL to create all 8 tables and seed starter stocks (`AAPL`, `SPY`, etc.). *Team cannot store data until this is done.*
- **Order 2:** Implement user authentication (register, login, JWT) and portfolio/holdings repository using parameterized native SQL.
- **Order 3:** Implement transactions repository and stock search endpoint (`/api/stocks/search`).

---

### 2. Moonsu Kang (PM & UI/UX Lead)
*Prompt file:* `docs/prompts/MOONSU_UI_UX.md`
- **Order 1:** Build the Toss Securities design system in `frontend/`: dark/light theme context, colors, typography, and reusable primitives (`Card`, `Button`, `Modal`, `Input`, `Badge`).
- **Order 2:** Build application layout shell (Navbar, links, theme switch, user profile).
- **Order 3:** Build `PortfolioListPage` and `PortfolioDetailPage` (holdings table, drift indicator, cash flow modal) and connect to Hakeem's portfolio APIs.

---

### 3. Minsuk Choi (Backtesting Backend Lead)
*Prompt file:* `docs/prompts/MINSUK_BACKTEST.md`
- **Order 1:** Build market data service using `yfinance` to fetch historical stock prices and FRED API for Treasury benchmark rates; implement caching into MySQL `price_history`.
- **Order 2:** Implement the 3 rebalancing algorithms (`threshold.py`, `calendar.py`, `hybrid.py`) and financial metrics calculator (XIRR, Max Drawdown, Turnover).
- **Order 3:** Build FastAPI backtest endpoints (`POST /api/backtests/run`, `POST /api/backtests/compare`) and save results to `backtests` table.

---

### 4. John Cha (Frontend Visualization Lead)
*Prompt file:* `docs/prompts/JOHN_FRONTEND_VIS.md`
- **Order 1:** Install `recharts` and build the Backtest configuration form using Moonsu's UI components (`BacktestSetup.tsx`).
- **Order 2:** Build the performance comparison dashboard: time-series line chart, allocation drift area chart, rebalance event feed, and metric cards.
- **Order 3:** Connect the dashboard to Minsuk's `/api/backtests/run` endpoint and build the `StockSearchModal.tsx` connected to Hakeem's search API.
