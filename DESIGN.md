# 🧾 Design Document — Crypto Portfolio Tracker Web App

## Overview

This project is a web application built with ASP.NET Core (.NET 8) that allows users to manage a personalized cryptocurrency portfolio. Users can register and log in, add or remove crypto holdings, and view up-to-date pricing and performance metrics fetched from the CoinGecko API.

---

## Goals

- ✅ Allow users to securely create accounts and manage their crypto portfolios
- ✅ Enable adding, editing, and deleting currency holdings
- ✅ Display real-time market data (price, % change) for tracked currencies
- ✅ Store user data persistently using Azure SQL Database
- ✅ Deploy the app for public demo access on Azure App Service

---

## Non-Goals

- ❌ No actual buying/selling or wallet integration
- ❌ No support for hundreds of currencies—limited to ~10–20 popular coins
- ❌ No mobile app interface or PWA support

---

## Tech Stack

| Layer                | Technology                                        |
|---------------------|---------------------------------------------------|
| Backend Framework   | ASP.NET Core MVC (v8.0)                           |
| Authentication      | ASP.NET Identity                                  |
| Frontend            | Razor Views + Bootstrap                           |
| Database            | Azure SQL Database (Serverless Free Tier)         |
| External API        | [CoinGecko API](https://www.coingecko.com/en/api) |
| ORM                 | Entity Framework Core (Code First)                |
| Hosting             | Azure App Service                                 |

---

## Key Features

### 1. User Authentication
- Register / Login / Logout using ASP.NET Identity
- Password hashing and security best practices

### 2. Portfolio Management
- Add currency + quantity
- Edit quantity
- Delete holding
- View total portfolio value

### 3. Market Data
- Fetch daily price and percentage change via CoinGecko API
- Show updated prices alongside user holdings

---

## Database Schema

### Users
- `Id` (PK)
- `Email`
- `PasswordHash` *(ASP.NET Identity)*

### Currencies
- `Id` (PK)
- `Symbol` (e.g., BTC, ETH)
- `Name` (e.g., Bitcoin)
- `ApiId` *(used for CoinGecko API query)*

### UserHoldings
- `Id` (PK)
- `UserId` (FK → Users)
- `CurrencyId` (FK → Currencies)
- `Quantity`

---

## API Integration

**CoinGecko API**
- Endpoint: `https://api.coingecko.com/api/v3/simple/price`
- Params:
  - `ids=bitcoin,ethereum,...`
  - `vs_currencies=usd`
  - `include_24hr_change=true`
- Caching layer may be added to reduce API calls (optional)

---

## UI Pages

| Page                  | Route                 | Description                              |
|-----------------------|-----------------------|------------------------------------------|
| Login/Register        | `/Identity/Account/...` | Built-in Identity templates              |
| Portfolio Dashboard   | `/` or `/Portfolio`   | Displays user's coins, quantity, price   |
| Add Currency          | `/Portfolio/Add`      | Form to select currency & quantity       |
| Edit/Delete Holding   | `/Portfolio/Edit/{id}` | Update or delete existing record         |
| Error/Fallback        | `/Error`              | Friendly error handling                  |

---

## Design Decisions

- **Razor Pages vs MVC**: Chose MVC for clearer separation of controller logic and views.
- **EF Core Code First**: Simplifies development and migrations.
- **Azure SQL Free Tier**: Sufficient for demo scale with up to 32GB and 100k vCore-seconds.
- **ASP.NET Identity**: Provides secure, out-of-the-box user management.
- **CoinGecko API**: Reliable, free, and does not require API keys (for most endpoints).

---

## Future Improvements

- Add dark mode toggle and charting (e.g., price history with Chart.js)
- Enable portfolio export (CSV, PDF)
- Add categories (DeFi, Layer 1, etc.)
- Notifications for price alerts

---

## Known Limitations

- CoinGecko has rate limits; performance may degrade without caching
- Only supports a fixed list of cryptocurrencies
- No support for currency conversion other than USD
- Not optimized for mobile

---

## Diagram

```text
[User] → [Web App (ASP.NET Core MVC)] → [EF Core] → [Azure SQL Database]
                            ↓
                 [CoinGecko API Service]
