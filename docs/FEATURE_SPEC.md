# Feature Specification — Portfolio Rebalancing Strategy Simulator

> **Project:** fa26-cs411-team002-schema4  
> **Course:** CS 411 — University of Illinois Urbana-Champaign  
> **UI Reference:** https://www.tossinvest.com/  
> **Theme:** Dark / Light mode

---

## Overview

This is the main index for all feature specifications of the **Portfolio Rebalancing Strategy Simulator**.  
The app lets users create and manage investment portfolios, define target allocations, record transactions, and run historical backtests comparing different rebalancing strategies.

---

## Document Index

| #   | File                                                 | Description                                                 |
| --- | ---------------------------------------------------- | ----------------------------------------------------------- |
| 1   | [AUTH.md](./AUTH.md)                                 | User authentication — sign up, login, logout (**required**) |
| 2   | [PORTFOLIO.md](./PORTFOLIO.md)                       | Portfolio management — create, view, edit, delete           |
| 3   | [HOLDINGS.md](./HOLDINGS.md)                         | Holdings and target allocation management                   |
| 4   | [TRANSACTIONS.md](./TRANSACTIONS.md)                 | Transaction history — deposit, withdrawal, buy, sell        |
| 5   | [BACKTEST.md](./BACKTEST.md)                         | Backtesting engine and strategy comparison                  |
| 6   | [SEARCH.md](./SEARCH.md)                             | Stock and ETF search                                        |
| 7   | [UI_UX.md](./UI_UX.md)                               | UI/UX design system — Dark/Light mode, Toss-style           |
| 8   | [DATABASE_SCHEMA.md](./DATABASE_SCHEMA.md)           | Full MySQL database schema definition                       |
| 9   | [FEATURES_OVERVIEW_KO.md](./FEATURES_OVERVIEW_KO.md) | 🇰🇷 한국어 종합 기능 명세 및 PM 가이드 문서                  |
| 10  | [FEATURES_OVERVIEW_EN.md](./FEATURES_OVERVIEW_EN.md) | 🇺🇸 Comprehensive Feature Specification & PM Guide (English) |

---

## Tech Stack (Planned)

| Layer          | Technology                |
| -------------- | ------------------------- |
| Frontend       | React + TypeScript        |
| Charts         | Recharts or Chart.js      |
| Backend API    | FastAPI (Python)          |
| Database       | MySQL                     |
| Market Data    | yfinance (Python library) |
| Benchmark Data | FRED API                  |
| Auth           | JWT-based session tokens  |

---

## Team Responsibilities

| Member          | Role                                                          |
| --------------- | ------------------------------------------------------------- |
| Moonsu Kang     | Project Manager & UI/UX Lead — Feature Coordination, Toss Design System, Core UI |
| John Cha        | Frontend Visualization — Backtest UI, Charts, API integration |
| Hakeem Muritala | Database Backend — MySQL Schema, CRUD APIs, FastAPI endpoints |
| Minsuk Choi     | Backtesting Backend — Strategy logic, XIRR, Simulation        |

---

## Core Design Principles

1. **Simple and clear** — The UI should be easy to understand, even for users who are not finance experts.
2. **Dark / Light mode** — All screens must support both themes without any visual issues.
3. **Toss-style** — Clean cards, large numbers, smooth transitions, minimal clutter — inspired by Toss Securities.
4. **Helpful feedback** — Loading states, empty states, and error messages must always be shown.
5. **Responsive layout** — Works well on both desktop and mobile screen sizes.
