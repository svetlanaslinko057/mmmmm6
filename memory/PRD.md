# Y-Store Marketplace - PRD

## Original Problem Statement
Підняти Y-Store e-commerce маркетплейс, імплементувати:
- O20.3-O20.6 Return Management Engine + Policy Control
- D-Mode Smart Payment Flow (Revenue Protection)

## Architecture
- **Frontend**: React 19, TailwindCSS, Recharts
- **Backend**: FastAPI, MongoDB (Motor)
- **Bot**: Aiogram 3.x @YStore_a_bot
- **Payments**: Fondy (mock ready for production)

## What's Been Implemented

### Session 2026-02-20

#### O20.3 Return Management Engine ✅
- Return detection from NP statuses
- Ledger losses (SHIP_COST_OUT, RETURN_COST_OUT, SALE_LOST)
- CRM counters + auto RISK/BLOCK_COD segmentation
- Telegram alerts
- API: /returns/summary, /list, /run, /resolve, /find, /trend

#### O20.4 Return Dashboard UI ✅
- `/app/frontend/src/components/admin/ReturnsDashboard.js`
- KPI cards, trend charts, reasons/cities bars, returns table
- Tab "Повернення" in AdminPanel

#### O20.5 Return Policy Engine ✅
- Rules: COD refusals → BLOCK_COD, Returns → REQUIRE_PREPAID
- City-level policies (return rate >= 15%)
- Approval queue + Telegram alerts
- VIP softening

#### O20.6 Policy Control Center ✅
- Admin UI for pending approvals
- City policies management, manual override
- History + Audit log, Tab "Policy" in AdminPanel

---

### D-Mode: Smart Payment Flow (Revenue Protection) ✅

#### D/Step 1: Payment Policy Decider
- `PaymentPolicyDecider` - determines FULL_PREPAID / SHIP_DEPOSIT / COD_ALLOWED
- Evaluates: customer segment, returns_60d, cod_refusals_30d, city policy, amount
- API: `POST /api/v2/payments/policy/preview`

#### D/Step 2: Smart Retry Flow
- Auto-reminders: 15min, 60min, 24h auto-cancel
- Channels: Telegram → SMS → Email
- Idempotent via dedupe keys
- API: `POST /api/v2/admin/payments/retry/run`
- Scheduler: every 5 minutes

#### D/Step 3: Payment Resume Page
- Frontend: `/payment/resume/:orderId`
- Auto-redirect to payment if `?auto=1`
- Recreate expired payment intents
- Countdown urgency timer
- API: `/api/v2/payments/resume/{orderId}`, `/recreate`

#### D/Step 4: Conversion Booster Pack
- One-click retry (recreate payment)
- Soft urgency countdown
- Freeze order option

#### D/Step 5: Recovery Analytics
- Track orders saved by retry/resume flow
- revenue_recovered, recovery_rate
- API: `/api/v2/admin/payments/recovery/summary`, `/trend`

#### D/Step 6: Payment Reconciliation
- Polls payment provider for stuck orders
- Fixes missed webhooks
- Scheduler: every 10 minutes
- API: `/api/v2/admin/payments/reconciliation/run`

---

## D-Mode Decision Matrix

| Signal | Result |
|--------|--------|
| segment == BLOCK_COD | FULL_PREPAID |
| cod_refusals_30d >= 2 | FULL_PREPAID |
| returns_60d >= 3 | FULL_PREPAID |
| city.require_prepaid | FULL_PREPAID |
| returns_60d >= 2 | SHIP_DEPOSIT |
| NEW + amount >= 8000 | SHIP_DEPOSIT |
| VIP | Soften 1 level |
| else | COD_ALLOWED |

---

## API Endpoints Summary

### Returns
- `GET /api/v2/admin/returns/summary`
- `GET /api/v2/admin/returns/trend`
- `GET /api/v2/admin/returns/list`
- `POST /api/v2/admin/returns/resolve`
- `POST /api/v2/admin/returns/run`

### Policy
- `GET /api/v2/admin/returns/policy/pending`
- `GET /api/v2/admin/returns/policy/cities`
- `POST /api/v2/admin/returns/policy/approve`
- `POST /api/v2/admin/returns/policy/reject`
- `POST /api/v2/admin/returns/policy/run`

### D-Mode Payments
- `POST /api/v2/payments/policy/preview`
- `POST /api/v2/payments/deposit/create`
- `POST /api/v2/payments/full/create`
- `GET /api/v2/payments/resume/{order_id}`
- `POST /api/v2/payments/resume/{order_id}/recreate`
- `POST /api/v2/admin/payments/retry/run`
- `GET /api/v2/admin/payments/recovery/summary`
- `POST /api/v2/admin/payments/reconciliation/run`

---

## Bot Commands
- `/returns_today`, `/returns_risk`, `/return_find <ttn>`
- `/pickup_today`, `/pickup_risk`, `/pickup_find <ttn>`
- Policy inline buttons (approve/reject)

## Environment
- Backend: localhost:8001
- Frontend: localhost:3000
- MongoDB: localhost:27017
- Bot: @YStore_a_bot

## Next Action Items
1. Integrate real Fondy payment provider
2. Add Prepaid Discount (1-2% for online payment)
3. Payment Health Dashboard in Admin

## Backlog
- Fraud Shield (scoring system)
- COD Dynamic Limit by amount
- City Risk Heatmap
- Viber/SMS fallback notifications
