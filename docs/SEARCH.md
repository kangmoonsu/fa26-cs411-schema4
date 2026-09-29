# SEARCH — Stock and ETF Search

> **Related file:** `FEATURE_SPEC.md`  
> **Owner:** Hakeem Muritala (Backend), Moonsu Kang (Frontend UI)

---

## Purpose

Users need to find stocks and ETFs to add them to their portfolios.  
The search feature lets users look up securities by **ticker symbol** or **company name**, and view basic information before adding them.

---

## Where Search Is Used

Search appears in multiple places throughout the app:

| Location | Use Case |
|----------|----------|
| Add Holding form | Find and select a stock/ETF to add to a portfolio |
| Add Transaction (Buy) form | Find a stock to record a buy transaction |
| Standalone Search Page (optional) | Browse or explore stocks |

---

## Pages & Screens

### 1. Search Input (Inline / Modal)

A search input field with live suggestions shown as a dropdown.

**User Flow:**
1. User types a ticker or company name (e.g., "AAPL" or "Apple")
2. As the user types, a dropdown appears with matching results
3. Each result shows: **Ticker Symbol**, **Company Name**, **Exchange** (e.g., NASDAQ)
4. User clicks a result → the ticker is selected and filled into the form field

**Minimum characters to trigger search:** 2

**Empty search state:**  
> "Type a ticker or company name to search."

**No results state:**  
> "No results found for '[query]'. Try a different ticker or name."

---

### 2. Stock Detail View (optional popup or page)

When a user selects a stock from search results, show a small info card:

| Info | Example |
|------|---------|
| Ticker | AAPL |
| Company Name | Apple Inc. |
| Exchange | NASDAQ |
| Sector | Technology |
| Latest Closing Price | $189.50 |
| 52-Week High | $199.62 |
| 52-Week Low | $143.90 |

**Actions:**
- Click **Add to Portfolio** → fills the ticker into the Add Holding form
- Click **Close** → close the popup

---

### 3. Standalone Search Page (`/search`) — Optional

A dedicated page for exploring stocks and ETFs.

**Features:**
- Search bar at the top
- Results shown as a list of cards
- Click any card to see the Stock Detail View

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/stocks/search?q={query}` | Search stocks and ETFs by ticker or name |
| `GET` | `/stocks/:ticker` | Get detailed info for a specific ticker |
| `GET` | `/stocks/:ticker/price?start={date}&end={date}` | Get historical price data for a ticker |

---

## Data Model

```
Stock (market data, pre-loaded from yfinance)
├── ticker          (VARCHAR(10), PK)
├── name            (VARCHAR(100), NOT NULL)
├── exchange        (VARCHAR(20), NULL)
├── sector          (VARCHAR(50), NULL)
├── asset_type      (ENUM('STOCK','ETF'), NOT NULL)
└── last_updated    (DATETIME, DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP)

PriceHistory
├── price_id        (PK, INT, AUTO_INCREMENT)
├── ticker          (VARCHAR(10), FK → Stock.ticker, NOT NULL)
├── date            (DATE, NOT NULL)
├── open            (DECIMAL(15,4))
├── close           (DECIMAL(15,4))
├── high            (DECIMAL(15,4))
├── low             (DECIMAL(15,4))
└── volume          (BIGINT)
```

---

## Notes

- Stock data is pre-loaded into the database using yfinance — it is not fetched live during search.
- The search is performed against the `ticker` and `name` columns in the `Stock` table.
- Search should be **case-insensitive** (e.g., "aapl" finds "AAPL").
- If the database does not contain the requested ticker, the backend can optionally fetch fresh data from yfinance on demand.
- For the CS 411 project scope, it is acceptable to pre-load a fixed list of popular stocks and ETFs (e.g., S&P 500 components).
