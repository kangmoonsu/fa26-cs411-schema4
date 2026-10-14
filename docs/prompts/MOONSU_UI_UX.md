# Moonsu Kang — UI/UX Lead & Core Frontend Prompts

> **Role:** Project Manager, UI/UX Lead, & Core Frontend Developer  
> **Course:** CS 411 — University of Illinois Urbana-Champaign  
> **Assigned Branch:** `git checkout -b feat/moonsu/toss-theme-and-portfolio`  
> **Execution Priority:** **Phase 1 (Start First)** — Teammates need your shared layout and UI primitives.

---

## 📌 Instructions for Moonsu:
You have 4 sequential prompts below. Feed them one by one into your AI coding assistant (or implement them in this exact order).

---

### 🔹 Prompt 1: Toss Securities Design System & Common UI Components
*Copy and paste the prompt below:*

```markdown
You are the UI/UX Lead for RebalanceLab (CS 411).
Your task is to establish the Toss Securities design system (inspired by https://www.tossinvest.com/) and build reusable UI primitives with Dark and Light mode support.

### Tech Stack:
- React 18+ with TypeScript (Vite)
- Tailwind CSS
- Lucide-React icons (`npm install lucide-react`)

### Requirements:
1. Setup Dark & Light Mode Theme Engine:
   - Create `src/context/ThemeContext.tsx` with `theme` state (`'dark' | 'light'`) persisted to `localStorage`.
   - Apply `dark` class to `<html>` element.
   - Configure Tailwind color palette:
     - Dark Base: `#101012`
     - Dark Card/Surface: `#1B1B1E` / `#202027`
     - Border/Divider: `#2C2C34`
     - Primary Text: `#FFFFFF`
     - Muted Text: `#8B95A1`
     - Toss Blue Accent: `#3182F6` (Hover `#1B64DA`)
     - Positive Green/Red: `#22C55E` or `#E15241`
     - Negative Blue: `#3182F6` or `#4B89DC`
2. Create reusable components in `src/components/common/`:
   - `Card.tsx`: Rounded-2xl, soft surface background, border, padding, hover scale effect.
   - `Button.tsx`: Variants (`primary` [blue], `secondary` [dark surface], `danger` [red], `ghost`), size variants, loading spinner.
   - `Badge.tsx`: Pill badge for percentages (`+5.4%`) or status (`Balanced`, `Drifted`).
   - `Modal.tsx`: Accessible dialog popup with backdrop blur (`backdrop-blur-md`) and escape-key close.
   - `Input.tsx` & `Select.tsx`: High-contrast dark inputs with blue focus border.
3. Export all components from `src/components/common/index.ts` so teammate John Cha can use them for backtest charts.
```

---

### 🔹 Prompt 2: Global Layout, Navbar & Auth Context
*Copy and paste the prompt below:*

```markdown
You are the UI/UX Lead for RebalanceLab (CS 411).
Your task is to build the global responsive layout, top navigation bar, and authentication modal.

### Requirements:
1. Create `src/context/AuthContext.tsx`:
   - State for `token`, `user` profile (`user_id`, `username`, `email`), `isAuthenticated`.
   - Persist JWT in `localStorage`.
   - Provide `login(token, user)` and `logout()` helper functions.
2. Create `src/components/layout/Navbar.tsx`:
   - Left: RebalanceLab logo with trending icon.
   - Center Navigation Links:
     - `Dashboard` (`/`)
     - `Portfolios` (`/portfolios`)
     - `Backtest Simulator` (`/backtest`)
   - Right Controls:
     - Theme Toggle Button (Sun/Moon icon).
     - User Profile button (or "Login / Sign Up" button if unauthenticated).
3. Create `src/components/auth/AuthModal.tsx`:
   - Toggleable tabs for "Log In" and "Sign Up".
   - Submits credentials to `POST /api/auth/login` and `POST /api/auth/register`.
   - Displays error messages cleanly on failed login.
4. Create `src/components/layout/AppLayout.tsx` wrapping all routes with `Navbar`, responsive container (`max-w-7xl mx-auto px-4 py-8`), and clean footer.
```

---

### 🔹 Prompt 3: Portfolio List Dashboard Page
*Copy and paste the prompt below:*

```markdown
You are the UI/UX Lead for RebalanceLab (CS 411).
Your task is to build the `PortfolioListPage.tsx` where users view, create, and manage their investment portfolios.

### Requirements:
1. Create `src/api/portfolio.ts` calling:
   - `GET /api/portfolios`
   - `POST /api/portfolios` (body: `name`, `initial_cash`)
   - `DELETE /api/portfolios/{id}`
2. Build `src/pages/portfolio/PortfolioListPage.tsx`:
   - Top Header: "My Portfolios", total net worth across all portfolios, and "+ Create Portfolio" button.
   - "Create Portfolio" Modal:
     - Form asking for: Portfolio Name (e.g., "Retirement Core 80/20") and Starting Cash ($).
   - Portfolio Grid Cards:
     - Card displaying:
       - Portfolio Name
       - Total Value ($) and Cash Balance ($)
       - Total Return % with colored badge
       - Holdings Count
       - "View Details" button navigating to `/portfolios/:id`
       - Delete action with confirmation prompt.
   - Clean empty state with illustration/icon if user has no portfolios yet.
```

---

### 🔹 Prompt 4: Portfolio Detail, Holdings & Cash Flow Modal
*Copy and paste the prompt below:*

```markdown
You are the UI/UX Lead for RebalanceLab (CS 411).
Your task is to build `PortfolioDetailPage.tsx` for inspecting holdings, tracking allocation drift, and recording deposits/withdrawals.

### Requirements:
1. Header Section:
   - Portfolio Name (editable), Current Value, Cash Balance.
   - Quick Action Buttons: "+ Deposit / Withdraw", "+ Add Holding", "Run Backtest" (navigates to `/backtest?portfolio_id={id}`).
2. Target Allocation Breakdown:
   - Visual progress bar or mini donut chart showing Current Weight vs Target Allocation %.
   - Warning banner if sum of target weights != 100%.
3. Holdings Table:
   - Columns:
     - Ticker & Company Name
     - Current Shares & Avg Cost
     - Market Price & Total Market Value ($)
     - Current Weight (%) vs Target Weight (%)
     - Drift %: `drift = current_weight - target_weight` (flag Overweight or Underweight in color).
     - Action buttons: Edit Target %, Remove Holding.
4. "Add Holding" Modal:
   - Allows selecting ticker (integrated with stock search), specifying Target Weight %, and initial shares.
5. "Deposit / Withdraw Cash" Modal:
   - Toggle `DEPOSIT` or `WITHDRAWAL`, amount ($), date, and notes. Calls `POST /api/portfolios/{id}/transactions`.
6. Transaction History Tab:
   - Table showing past deposits, withdrawals, buys, and sells.
```
