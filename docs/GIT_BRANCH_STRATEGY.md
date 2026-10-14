# RebalanceLab — Git Branching & Collaboration Strategy

> **Project:** RebalanceLab (CS 411 Team 002)  
> **Course:** CS 411 — University of Illinois Urbana-Champaign (UIUC)  
> **Goal:** Enable 4 team members to work in parallel on backend and frontend without merge conflicts, broken builds, or lost code.

---

## 1. Branch Architecture Overview

We use an **Agile Feature Branch Workflow** centered around two permanent branches:

```
[ main ]        ========================================== (Stable / Milestone Submissions)
                   ▲
                   │ (Milestone Release PR)
[ develop ]     ───┴───●──────────●──────────●──────────── (Integration Branch)
                       ▲          ▲          ▲
      (Feature PR) ───┘          │          └─── (Feature PR)
                                 │
[ feat/... ]    ─────────────────┴──────────────────────── (Individual Feature Branches)
```

### Permanent Branches:
| Branch | Purpose | Protection Rules |
| :--- | :--- | :--- |
| `main` | Production-ready, stable codebase submitted for course milestones. | **Protected.** Never commit directly. Only merge from `develop` via PR after full team sign-off. |
| `develop` | Active integration branch. All completed features are merged here and tested together. | **Protected.** No direct pushes. All changes come from feature branch PRs. |

---

## 2. Branch Naming Conventions

Every new task MUST have its own dedicated branch cut from the latest `origin/develop`.

### Format:
```
<type>/<role-or-name>/<short-kebab-description>
```

- `<type>`: `feat` (new feature), `fix` (bug fix), `docs` (documentation), `refactor` (code cleanup)
- `<role-or-name>`: Your name or role tag (`moonsu`, `john`, `hakeem`, `minsuk` or `ui`, `vis`, `db`, `backtest`)
- `<short-kebab-description>`: 2–4 hyphenated words describing the specific work

### Examples per Team Member:

| Member | Role | Branch Example |
| :--- | :--- | :--- |
| **Moonsu Kang** | UI/UX Lead | `feat/moonsu/toss-theme-tokens`<br>`feat/moonsu/portfolio-views`<br>`feat/moonsu/auth-modal` |
| **John Cha** | Frontend Visualization | `feat/john/backtest-setup-form`<br>`feat/john/performance-charts`<br>`feat/john/stock-search-modal` |
| **Hakeem Muritala** | Database Backend | `feat/hakeem/gcp-schema-runner`<br>`feat/hakeem/portfolio-crud-sql`<br>`feat/hakeem/auth-jwt-api` |
| **Minsuk Choi** | Backtesting Backend | `feat/minsuk/yfinance-price-ingest`<br>`feat/minsuk/rebalance-engine`<br>`feat/minsuk/xirr-calculator` |

---

## 3. Commit Message Standards (Conventional Commits)

Write clear commit messages so teammates and CS 411 graders can track who implemented what:

### Format:
```
<type>(<scope>): <short imperative summary>
```

### Commit Types:
- `feat`: A new user-facing feature or API endpoint (e.g., `feat(api): add portfolio CRUD endpoints with native SQL`)
- `fix`: A bug fix (e.g., `fix(rebalance): correct threshold drift calculation formula`)
- `docs`: Documentation updates (e.g., `docs(readme): add GCP MySQL proxy setup steps`)
- `refactor`: Code restructuring with no behavior change (e.g., `refactor(db): organize repository queries`)
- `style`: Formatting, CSS styling, UI polish (e.g., `style(ui): apply Toss dark mode card shadows`)
- `test`: Adding or updating unit tests (e.g., `test(xirr): add test case for irregular cash flows`)

---

## 4. Step-by-Step Developer Workflow

### Step 1: Start from the Latest `develop`
Never start a feature from an old local branch or `main`:
```bash
git checkout develop
git pull origin develop
git checkout -b feat/hakeem/portfolio-crud-sql
```

### Step 2: Develop & Commit Locally
Make small, atomic commits as you work:
```bash
git add backend/app/repositories/portfolio_repo.py
git commit -m "feat(portfolio): implement native SQL select queries"
```

### Step 3: Keep Your Branch Updated with `develop`
Before creating a Pull Request, incorporate any new work merged by your teammates:
```bash
git fetch origin
git rebase origin/develop
```
*(If there are conflicts, resolve them in your code editor, run `git add <resolved-files>`, and continue with `git rebase --continue`.)*

### Step 4: Push Branch & Open a Pull Request (PR)
```bash
git push -u origin feat/hakeem/portfolio-crud-sql
```
- Go to GitHub and open a Pull Request targeting **`base: develop`** (NOT `main`).
- Fill in the PR description template:
  ```markdown
  ### Summary of Changes:
  - Implemented parameterized native SQL queries for portfolio CRUD
  - Added FastAPI router at `/api/portfolios`

  ### Related Spec:
  - `docs/PORTFOLIO.md` & `docs/DATABASE_SCHEMA.md`

  ### Testing Done:
  - Tested locally with GCP MySQL; returned status 200 and expected JSON payload.
  ```

### Step 5: Peer Review & Merge
- Require at least **1 teammate approval** before merging.
- Select **"Squash and merge"** on GitHub to keep the `develop` history clean and readable.
- Delete the remote feature branch after successful merge.

---

## 5. Conflict Prevention Strategy for RebalanceLab

Because 4 members are working across `backend/` and `frontend/`, follow these boundary rules:

1. **Avoid Central File Collisions:**
   - Instead of all backend members adding code directly inside `backend/app/main.py`, create isolated router files (e.g., `routers/portfolio.py`, `routers/backtest.py`) and only add a single `app.include_router(...)` line in `main.py`.
   - In frontend, create dedicated folders under `src/pages/` and `src/components/` rather than editing `App.tsx` simultaneously.

2. **API Contract First:**
   - Frontend developers (John, Moonsu) and Backend developers (Hakeem, Minsuk) should agree on request/response JSON schemas (as defined in `docs/FEATURE_SPEC.md` and feature docs) before writing client/server code.
   - Use TypeScript interfaces matching the backend Pydantic models.

3. **Never Commit Secrets:**
   - Never commit `.env` containing GCP Cloud SQL passwords or API keys.
   - Only commit `.env.example` with blank/sample placeholder values.

---

## 6. Fast-Reference Terminal Cheat Sheet

| Task | Git Command |
| :--- | :--- |
| **Switch to develop & pull latest** | `git checkout develop && git pull origin develop` |
| **Create new feature branch** | `git checkout -b feat/<name>/<feature>` |
| **Check changed files** | `git status` |
| **Commit staged changes** | `git commit -m "feat(scope): message"` |
| **Sync branch with latest develop** | `git fetch origin && git rebase origin/develop` |
| **Push new branch to GitHub** | `git push -u origin <branch-name>` |
| **Discard local unstaged changes** | `git restore .` |
| **Delete merged local branch** | `git branch -d <branch-name>` |
