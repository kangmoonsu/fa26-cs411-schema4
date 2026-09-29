# UI_UX — Design System and Style Guide

> **Related file:** `FEATURE_SPEC.md`  
> **Owner:** Moonsu Kang (Project Manager & UI/UX Lead), John Cha (Visualization)  
> **Design Reference:** https://www.tossinvest.com/

---

## Purpose

This document defines the visual design system for the app.  
All screens must follow these rules to make the UI look consistent, clean, and professional.  
The design is inspired by **Toss Securities**, known for its simple, modern, and friendly financial UI.

---

## 1. Dark / Light Mode

The app must support both Dark Mode and Light Mode.  
Users can toggle between modes using a button in the top navigation bar.  
The selected mode should be saved in `localStorage` so it persists between sessions.

### Color Tokens

Use CSS variables so colors can switch automatically.

```css
/* Light Mode */
:root[data-theme="light"] {
  --color-bg-primary:     #FFFFFF;
  --color-bg-secondary:   #F5F6FA;
  --color-bg-card:        #FFFFFF;
  --color-border:         #E8EAED;
  --color-text-primary:   #191F28;
  --color-text-secondary: #6B7684;
  --color-text-disabled:  #ADB5BD;
  --color-accent:         #3182F6;      /* Toss blue */
  --color-positive:       #00B493;      /* Gain / green */
  --color-negative:       #F04452;      /* Loss / red */
  --color-warning:        #FFAB00;
  --color-shadow:         rgba(0,0,0,0.06);
}

/* Dark Mode */
:root[data-theme="dark"] {
  --color-bg-primary:     #121212;
  --color-bg-secondary:   #1E1E1E;
  --color-bg-card:        #252525;
  --color-border:         #333333;
  --color-text-primary:   #F5F6FA;
  --color-text-secondary: #9EA8B3;
  --color-text-disabled:  #555F6D;
  --color-accent:         #4B9CF8;
  --color-positive:       #00C49A;
  --color-negative:       #FF5C67;
  --color-warning:        #FFBB33;
  --color-shadow:         rgba(0,0,0,0.4);
}
```

---

## 2. Typography

Use **Inter** (English-first) from Google Fonts.

```css
@import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');

body {
  font-family: 'Inter', -apple-system, sans-serif;
}
```

### Type Scale

| Name | Size | Weight | Use Case |
|------|------|--------|----------|
| `heading-xl` | 28px | 700 | Page title |
| `heading-lg` | 22px | 700 | Section title |
| `heading-md` | 18px | 600 | Card title, modal title |
| `body-lg` | 16px | 400 | Main content, table rows |
| `body-md` | 14px | 400 | Secondary content, labels |
| `body-sm` | 12px | 400 | Captions, helper text |
| `number-xl` | 32px | 700 | Large portfolio values |
| `number-lg` | 24px | 600 | Medium values |

---

## 3. Layout

### Navigation

- **Top Navigation Bar** — Contains logo, search icon, Dark/Light toggle, and user avatar/logout.
- **Left Side Menu** (on desktop) — Links to: Dashboard, Portfolios, Search, Backtest History.
- **Bottom Tab Bar** (on mobile) — Icons for the same main sections.

### Grid

- Desktop: 12-column grid, max container width 1200px, 24px gutters
- Tablet: 8-column grid
- Mobile: 4-column grid, 16px gutters

---

## 4. Component Patterns (Toss-style)

### Card

```
┌─────────────────────────────────┐
│  Card Title              Label  │
│                                 │
│  $12,345.67                     │
│  ↑ +5.32%  (+$620.00)          │
│                                 │
└─────────────────────────────────┘
```

- Background: `--color-bg-card`
- Border radius: 16px
- Padding: 20px
- Shadow: `0 2px 12px var(--color-shadow)`
- On hover: subtle lift with `transform: translateY(-2px)` + stronger shadow

---

### Buttons

| Type | Style | Use Case |
|------|-------|----------|
| Primary | Filled blue (`--color-accent`), white text | Main action |
| Secondary | Outlined, blue border, blue text | Secondary action |
| Danger | Filled red (`--color-negative`), white text | Delete / destructive action |
| Ghost | No border, blue text | Low-priority action |

- All buttons: border-radius 10px, height 44px, font-weight 600
- Active state: scale down to 0.97 with `transform`
- Disabled state: `opacity: 0.4`, `cursor: not-allowed`

---

### Number Display

Numbers should be formatted based on their type:

| Type | Format Example |
|------|---------------|
| Portfolio Value | $12,345.67 |
| Gain/Loss (positive) | +$620.00 (shown in green) |
| Gain/Loss (negative) | −$230.00 (shown in red) |
| Return Percentage | +5.32% or −1.87% |
| XIRR | 8.45% / year |
| Shares | 12.5000 |

---

### Tags / Badges

Used for transaction types, strategy names, status labels:

| Tag | Color |
|-----|-------|
| DEPOSIT | Blue |
| WITHDRAWAL | Orange |
| BUY | Green |
| SELL | Red |
| DONE | Green |
| RUNNING | Blue (animated) |
| FAILED | Red |

- Style: small pill shape (border-radius: 100px), padded, small font

---

### Tables

- Alternating row background: subtle difference between odd/even rows
- Sticky header when scrolling
- Hover row: highlight with `--color-bg-secondary`
- Right-align all number columns
- Left-align text columns

---

### Charts

Use **Recharts** or **Chart.js** with custom theming:

| Chart Type | Use Case |
|------------|----------|
| Line chart | Portfolio value over time, drift over time |
| Pie / Donut chart | Current vs. target allocation |
| Bar chart | Return comparison across strategies |

Chart colors should use the accent palette and adapt to Dark/Light mode.  
All charts must have **tooltips** on hover showing exact values.

---

## 5. Micro-Interactions and Animations

| Action | Animation |
|--------|-----------|
| Page load | Fade in + slide up (200ms) |
| Card hover | Lift up + shadow grow (150ms ease) |
| Button click | Scale down 0.97 (100ms) |
| Modal open | Slide up from bottom + fade in (250ms) |
| Modal close | Fade out + slide down (200ms) |
| Number change | Animated count-up from previous value |
| Theme toggle | Smooth color transition (300ms ease) |
| Loading spinner | Spinning ring with brand color |

---

## 6. Key Screens Summary

| Screen | URL | Notes |
|--------|-----|-------|
| Login | `/login` | Centered card, minimal layout |
| Sign Up | `/signup` | Same style as login |
| Dashboard | `/dashboard` | Portfolio summary cards, quick stats |
| Portfolio List | `/portfolios` | Card grid layout |
| Portfolio Detail | `/portfolios/:id` | Summary + holdings table + chart |
| Holdings View | (inside Portfolio Detail) | Table with drift indicators |
| Transaction List | `/portfolios/:id/transactions` | Filterable table |
| Backtest Setup | `/portfolios/:id/backtest/new` | Step-by-step form |
| Backtest Results | `/portfolios/:id/backtest/:id` | Charts + comparison table |
| Search | `/search` | Search input + result cards |

---

## 7. Accessibility

- Minimum color contrast ratio: **4.5:1** for normal text, **3:1** for large text
- All interactive elements must be reachable by keyboard (Tab key)
- Buttons and form elements must have clear `focus` styles
- All images and icons must have `alt` text or `aria-label`
- Form error messages must be associated with their input using `aria-describedby`

---

## 8. Empty and Error States

Every page and section must handle all three states:

| State | What to Show |
|-------|-------------|
| **Loading** | Skeleton screen (gray animated placeholder blocks) |
| **Empty** | Icon + friendly message + call-to-action button |
| **Error** | Error icon + message + "Try again" button |

**Example empty state message:**
> "No portfolios yet — create your first one to get started! [+ Create Portfolio]"

**Example error state message:**
> "Something went wrong while loading your data. Please try again."
