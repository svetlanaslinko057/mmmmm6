# Y-Store Marketplace - PRD

## Original Problem Statement
Y-Store e-commerce маркетплейс з повним функціоналом управління поверненнями, payment risk protection та Fondy payment gateway.

## Architecture
- **Frontend**: React 19, TailwindCSS, Recharts
- **Backend**: FastAPI, MongoDB (Motor)
- **Bot**: Aiogram 3.x @YStore_a_bot
- **Payments**: Fondy (PRODUCTION - Merchant ID: 1558123)

## What's Been Implemented

### Session 2026-02-20 (Latest)

#### Production Fondy Integration ✅
- **Files**: `/app/backend/modules/payments/fondy_*.py`, `/app/backend/modules/payments/providers/fondy/`
- Signature verification (SHA-1)
- Payment creation with checkout URL
- Webhook handler with anti-replay, idempotency
- Status check by order_id
- **Webhook URL**: `https://smart-payment-core.preview.emergentagent.com/api/v2/payments/webhook/fondy`

#### Frontend Login Fix ✅
- Fixed "Invalid Host Header" in craco.config.js
- Added admin password_hash to database
- Cookie/language modal handled properly

---

### O20.3 Return Management Engine ✅
- Return detection from NP statuses
- Ledger losses (SHIP_COST_OUT, RETURN_COST_OUT, SALE_LOST)
- CRM counters + auto RISK/BLOCK_COD segmentation
- Telegram alerts
- API: /returns/summary, /list, /run, /resolve, /find, /trend

### O20.4 Return Dashboard UI ✅
- `/app/frontend/src/pages/admin/ReturnsDashboard.jsx`
- KPI cards, trend charts, reasons/cities bars, returns table
- Tab "Повернення" in AdminPanel

### O20.5 Return Policy Engine ✅
- Rules: COD refusals → BLOCK_COD, Returns → REQUIRE_PREPAID
- City-level policies (return rate >= 15%)
- Approval queue + Telegram alerts
- VIP softening

### O20.6 Policy Control Center ✅
- Admin UI for pending approvals
- City policies management, manual override
- History + Audit log, Tab "Policy" in AdminPanel

---

### D-Mode: Smart Payment Flow (Revenue Protection) ✅

#### D/Step 1: Payment Policy Decider
- `PaymentPolicyDecider` - determines FULL_PREPAID / SHIP_DEPOSIT / COD_ALLOWED
- API: `POST /api/v2/payments/policy/preview`

#### D/Step 2: Smart Retry Flow
- Auto-reminders: 15min, 60min, 24h auto-cancel
- Scheduler: every 5 minutes
- API: `POST /api/v2/admin/payments/retry/run`

#### D/Step 3: Payment Resume Page
- Frontend: `/payment/resume/:orderId`
- Auto-redirect to payment if `?auto=1`
- API: `/api/v2/payments/resume/{orderId}`, `/recreate`

#### D/Step 4: Conversion Booster Pack
- One-click retry (recreate payment)
- Soft urgency countdown
- Freeze order option

#### D/Step 5: Recovery Analytics
- Track orders saved by retry/resume flow
- API: `/api/v2/admin/payments/recovery/summary`, `/trend`

#### D/Step 6: Payment Reconciliation
- Polls payment provider for stuck orders
- Scheduler: every 10 minutes
- API: `/api/v2/admin/payments/reconciliation/run`

---

## Key API Endpoints

### Fondy Payments
- `GET /api/v2/payments/webhook/fondy/health` - Health check
- `POST /api/v2/payments/webhook/fondy` - Webhook callback
- `POST /api/v2/payments/checkout` - Create payment checkout
- `GET /api/v2/payments/status/{order_id}` - Get payment status

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

---

## Test Credentials
- **Admin**: admin@ystore.ua / admin123

## Environment
- **Backend**: localhost:8001
- **Frontend**: localhost:3000
- **MongoDB**: localhost:27017
- **Preview URL**: https://smart-payment-core.preview.emergentagent.com

---

## Next Action Items (P1)
1. ✅ ~~Production Fondy Integration~~
2. Payment Health Dashboard (admin panel)
3. Enable Prepaid Discount (env vars PREPAID_DISCOUNT_MODE, PREPAID_DISCOUNT_VALUE)

## Backlog (P2)
- Fraud Shield (scoring system)
- Dynamic COD Limit
- Auto-approve COD for trusted customers
- City Risk Heatmap UI
- Telegram broadcast_wizard bug fix
