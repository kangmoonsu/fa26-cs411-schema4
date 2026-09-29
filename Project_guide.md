## Project Title

Portfolio Rebalancing Strategy Simulator

## Project Summary

Portfolio Rebalancing Strategy Simulator is a web application that allows users to create and manage investment portfolios using historical stock data. Users can add holdings, define target allocations, and record transactions such as deposits, withdrawals, buys, and sells. The application is designed as an intuitive dashboard rather than a technical trading platform. Its main feature is the ability to backtest and compare multiple portfolio rebalancing strategies, including threshold-based, calendar-based, and cash-flow-aware/hybrid rebalancing. The system will compare results such as portfolio value, returns, allocation drift, portfolio turnover, and the number of rebalances performed.

Unlike a static portfolio simulator, the application will also model changes in cash flow over time. Deposits and withdrawals will be incorporated into the simulation, allowing the system to calculate Extended Internal Rate of Return (XIRR) and show how different rebalancing strategies respond to both market changes and portfolio activity. This gives users a clearer way to compare the tradeoffs between different rebalancing approaches under the same historical conditions.

## Creative Component

The main creative component of our application is an interactive backtesting system that allows users to compare multiple portfolio rebalancing strategies under the same historical market conditions. Instead of only alerting users when their portfolio moves away from its target allocation, the system will simulate threshold-based, calendar-based, and cash-flow-aware/hybrid rebalancing strategies over a selected time period. The application will process historical stock prices, portfolio holdings, target allocations, and transaction history to determine when each strategy would trigger a rebalance and how the portfolio would change over time.

The cash-flow-aware/hybrid strategy adds additional complexity by considering deposits and withdrawals when making rebalancing decisions. For example, new deposits can be directed toward underweight assets before the system recommends selling overweight assets. The results of each strategy will be displayed through charts, tables, and historical rebalance events so users can compare portfolio value, returns, allocation drift, turnover, and the number of rebalances performed. This combination of simulation, data processing, and interactive visualization provides functionality beyond a basic portfolio tracking application.

## Usefulness

Many individual investors set a target asset allocation, but over time their portfolios can drift away from those targets as asset prices change. Our application allows users to create and manage a portfolio, define target allocations, and backtest different rebalancing strategies using real historical market data. This helps users understand how different strategies behave over time and how factors such as trading frequency, allocation drift, and cash flows can affect portfolio performance.

Portfolio trackers and rebalancing-alert applications already exist, but many focus primarily on monitoring current allocations or generating alerts when a portfolio moves beyond a specified threshold. Our application differs by allowing multiple rebalancing strategies to be simulated and compared under the same historical conditions. It also incorporates deposits and withdrawals into the analysis and uses cash-flow-aware performance measures such as XIRR. This gives users a more complete view of how different rebalancing approaches respond to both market movements and changes in portfolio cash flow.

## Realness (Data Sources)

Our primary market dataset will consist of historical stock and ETF price data retrieved through the `yfinance` Python library. The dataset will include information such as ticker symbol, date, opening price, closing price, daily high and low prices, and trading volume. Historical prices will be stored across multiple securities and dates, resulting in well over 1,000 records. This data will serve as the main input for backtesting the different portfolio rebalancing strategies.

Our second external data source will be the Federal Reserve Economic Data (FRED) API. We will use historical Treasury or risk-free interest-rate data as a benchmark when evaluating portfolio performance. FRED provides time-series economic data that can be retrieved programmatically and stored alongside the market data used by the application.

The application will also generate and store its own portfolio data based on user activity. This will include portfolios, holdings, target allocations, deposits, withdrawals, buy and sell transactions, rebalancing strategies, and backtest results. Combining historical market data, benchmark economic data, and user-generated portfolio information will provide the data needed to simulate and compare the supported rebalancing strategies.

## Functionality

Users will be able to create and manage investment portfolios, holdings, target allocations, and transactions through the application. They can also configure rebalancing strategies and run historical backtests to compare how different strategies perform. The application will provide CRUD operations for the main portfolio data as well as search functionality for finding stocks and viewing stored information.

| Operation | Functionality |
|-----------|---------------|
| Create | Create a portfolio |
| Create | Add a stock or ETF holding |
| Create | Add a transaction such as a deposit, withdrawal, buy, or sell |
| Create | Set target allocation percentages |
| Create | Create and run a backtest by selecting a strategy and historical time period |
| Read | View existing portfolios and portfolio details |
| Read | View holdings and current allocations |
| Read | View transaction history |
| Read | View target allocations and strategy settings |
| Read | View backtest results and strategy comparisons |
| Update | Rename or modify a portfolio |
| Update | Edit holdings |
| Update | Change target allocation percentages |
| Update | Change rebalancing strategy settings |
| Update | Edit transactions when necessary |
| Delete | Delete a portfolio |
| Delete | Remove a holding |
| Delete | Delete a transaction |
| Delete | Delete a previous backtest |
| Search | Search for stocks or ETFs by ticker symbol or company name |

The backtesting system will allow users to compare threshold-based, calendar-based, and cash-flow-aware/hybrid rebalancing strategies. Results will include metrics such as portfolio value, returns, XIRR, allocation drift, portfolio turnover, and the number of rebalances performed. Charts and comparison tables will help users understand how each strategy behaved over the selected historical period.

If authentication is included, users will also be able to sign up, log in, log out, and manage their account. Authentication will remain a supporting feature rather than a primary focus of the application.

## Low-Fidelity UI Mockup

The user interface will be inspired by Toss Securities (https://www.tossinvest.com/), featuring a clean card layout, prominent typography, intuitive financial visualizations, and full Dark/Light mode support.

## Project Work Distribution

- **Moonsu Kang — Project Manager & UI/UX:** Responsible for overall project management, defining clear feature scopes, aligning cross-functional workflows, establishing the base UI design system and themes inspired by Toss Securities (https://www.tossinvest.com/), and developing core portfolio interface components.

- **John Cha — Frontend Visualization:** Responsible for the backtesting interface, strategy comparison pages, charts and visualizations, and connecting frontend components to backend API endpoints.

- **Hakeem Muritala — Database Backend:** Responsible for designing the database schema, implementing CRUD and search operations, managing the MySQL database and database integration, and developing FastAPI endpoints for portfolio data.

- **Minsuk Choi — Backtesting Backend:** Responsible for implementing the rebalancing strategies, historical market data processing, backtesting logic, XIRR calculations, and backend endpoints for simulation results.


