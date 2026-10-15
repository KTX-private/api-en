# Changelog

## 2026-07-07 v4.2 — New Contracts Summary Endpoint + Full Java Examples

### New Endpoint

| Endpoint | Method | Path | Description |
|----------|--------|------|-------------|
| Contracts Summary | GET | /v1/pu/contracts | Get contract open interest, funding rate, index price and order book summary |

### Full Java Code Examples Added (43 code blocks)

* Uses JDK 11+ built-in APIs (`java.net.http.HttpClient` / `WebSocket`), no third-party dependencies
* All authenticated endpoints include HMAC-SHA256 signing helper method
* Keys use `YOUR_API_KEY` / `YOUR_SECRET_KEY` placeholders

| File | Endpoints | Type |
|------|-----------|------|
| index.html.md | 1 | Authentication example (GET with signature) |
| _market_rest.md | 10 | Public GET (ping, time, coins, products, positionTier, order_book, candles, trades, ticker, contracts) |
| _market_ws.md | 1 | Market Data WebSocket subscription |
| _user_rest.md | 17 | Private endpoints (assets, deposit, withdraw, transfer, ledger, order, query, history, pending, cancel, leverage, margin, positions, fills) |
| _user_ws.md | 1 | User WebSocket LOGIN authentication |
| _prediction.md | 13 | Prediction market (events, markets, ticker, order_book, order, query, cancel, positions, split/merge, fills) |

### Security Fix

* Fixed hardcoded API Key / Secret in `_user_ws.md` JavaScript and Python examples (4 occurrences)

---

## 2026-07-06 v4.1 — Documentation Quality Improvements

### Code Example Fixes (Category A — Affects Execution)

* Fixed Python `requests.get()` missing `params=` parameter (_user_rest.md 7 occurrences, _prediction.md 5 occurrences, index.html.md 1 occurrence)
* Fixed _market_rest.md Get Server Time example URL and path errors
* Fixed 3 missing commas, 1 extra `}`, and 1 trailing comma in _market_ws.md JSON examples
* Added 2 missing Response examples in _user_rest.md

### Spelling / Format Fixes (Category B — No Runtime Impact)

* Fixed `exprieTime` → `expireTime` (_user_rest.md 47 occurrences, _prediction.md 22 occurrences, index.html.md several occurrences)
* Fixed `upload failed` error message text (_user_rest.md 15 occurrences, _prediction.md 13 occurrences, _market_rest.md 5 occurrences, index.html.md 1 occurrence)

### Security Fixes (Category C)

* Replaced hardcoded API Key / Secret with `YOUR_API_KEY` / `YOUR_SECRET_KEY` placeholders (_user_rest.md 34 occurrences, _prediction.md 34 occurrences, index.html.md several occurrences)

### Description Consistency Fixes (Category D)

* Updated API description: from "spot market" to "supporting spot, USDT-M perpetual and prediction market"
* Error format now uses JSON code block (replacing HTML `<br/>`)
* Added missing error codes -20006 (CPU usage limit exceeded) and -20007 (Access frequency limit exceeded) to _errors.md

---

## 2026-07-05 v4.0 — Modular Documentation Structure

### File Split

Split single file `index.html.md` (5111 lines) into modular structure:

| New File | Content | Lines |
|----------|---------|-------|
| index.html.md | General info, rate limits, authentication | ~200 |
| _market_rest.md | Market Data REST API | ~670 |
| _market_ws.md | Market Data WebSocket | ~495 |
| _user_rest.md | User Data REST API | ~2080 |
| _user_ws.md | User Data WebSocket | ~280 |
| _prediction.md | Prediction Market API | ~1350 |
| _errors.md | Error code reference | ~50 |

### Frontmatter Update

```yaml
includes:
- market_rest
- market_ws
- user_rest
- user_ws
- prediction
- errors
- changelog
```
