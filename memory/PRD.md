# Y-Store Marketplace - PRD

## Original Problem Statement
Клонувати репозиторій https://github.com/svetlanaslinko057/bbbbbb, підняти фронт/бек/MongoDB, вивчити структуру, продовжити доробку:
- O20.3 Return Management Engine (✅ DONE)
- O20.4 Return Dashboard UI (✅ DONE)  
- O20.5 Return Policy Engine (✅ DONE)
- O20.6 Policy Control Center (✅ DONE)

## Architecture
- **Frontend**: React 19, TailwindCSS, Radix UI, Recharts
- **Backend**: FastAPI (Python 3.11), modular architecture
- **Database**: MongoDB (Motor async driver)
- **Bot**: Aiogram 3.x Telegram bot @YStore_a_bot
- **Integrations**: Nova Poshta API, AI (OpenAI via Emergent)

## What's Been Implemented

### Session 2026-02-20

#### O20.3 Return Management Engine ✅
- `/app/backend/modules/returns/return_types.py` - Types
- `/app/backend/modules/returns/return_mapping.py` - NP status mapping  
- `/app/backend/modules/returns/return_repo.py` - Repository
- `/app/backend/modules/returns/return_engine.py` - Detection engine
- `/app/backend/modules/returns/return_analytics.py` - KPI calculations
- `/app/backend/modules/returns/return_routes.py` - API endpoints

API Endpoints:
- `POST /api/v2/admin/returns/run` - Manual trigger
- `GET /api/v2/admin/returns/list` - List returns
- `GET /api/v2/admin/returns/summary` - KPIs
- `GET /api/v2/admin/returns/trend` - Daily trend for charts
- `GET /api/v2/admin/returns/risk-customers` - High-risk customers
- `POST /api/v2/admin/returns/resolve` - Mark resolved

Bot Commands: `/returns_today`, `/returns_risk`, `/return_find <ttn>`

#### O20.4 Return Dashboard UI ✅
- `/app/frontend/src/components/admin/ReturnsDashboard.js`
- KPI cards (returns today, 30d, rate, losses)
- Trend charts (returns, losses)
- Top reasons/cities bar charts
- Returns table with resolve action
- Tab "Повернення" in AdminPanel

#### O20.5 Return Policy Engine ✅
- `/app/backend/modules/returns/policy_types.py` - PolicyDecision types
- `/app/backend/modules/returns/policy_repo.py` - Repository + audit
- `/app/backend/modules/returns/policy_engine.py` - Rule engine
- `/app/backend/modules/returns/policy_scheduler.py` - Scheduler (30 min)

Rules:
- cod_refusals_30d >= 2 → BLOCK_COD_CUSTOMER
- returns_60d >= 2 → REQUIRE_PREPAID_CUSTOMER
- returns_60d >= 3 → BLOCK_COD_CUSTOMER
- city_return_rate_30d >= 15% (30+ orders) → REQUIRE_PREPAID_CITY

#### O20.6 Policy Control Center ✅
- `/app/backend/modules/returns/policy_routes.py`
- `/app/frontend/src/components/admin/PolicyDashboard.js`

API Endpoints:
- `POST /api/v2/admin/returns/policy/run` - Run engine
- `GET /api/v2/admin/returns/policy/pending` - Pending approvals
- `GET /api/v2/admin/returns/policy/history` - History
- `GET /api/v2/admin/returns/policy/cities` - City policies
- `POST /api/v2/admin/returns/policy/approve` - Approve action
- `POST /api/v2/admin/returns/policy/reject` - Reject action
- `POST /api/v2/admin/returns/policy/manual` - Manual override
- `GET /api/v2/admin/returns/policy/customer/{phone}` - Customer policy
- `DELETE /api/v2/admin/returns/policy/city/{city}` - Remove city policy

Bot Callbacks: `policy:approve:*`, `policy:reject:*`

## Ops Dashboard
`/api/v2/admin/ops/dashboard` now includes:
- `returns` block with all KPIs

## Bot Commands
- `/start`, `/menu` - Main menu
- `/returns_today` - Return KPIs
- `/returns_risk` - Risk customers
- `/return_find <ttn>` - Find return
- `/pickup_today`, `/pickup_risk` - Pickup control
- Policy inline buttons (approve/reject)

## Environment
- Backend: localhost:8001
- Frontend: localhost:3000 (proxy to backend)
- MongoDB: localhost:27017
- Telegram Bot: @YStore_a_bot (running)

## Next Action Items (P0)
1. Frontend login fix (proxy added, may need full rebuild)
2. Seed test return data for demo
3. Production deployment setup

## Backlog (P1)
- Return Policy Engine refinements (VIP exclusions, LTV thresholds)
- City Risk Heatmap visualization
- Viber/SMS notifications
- Auto-cancel 30+ day orders
- Predictive return probability (ML)

## Known Issues
- Frontend login may need manual rebuild (`yarn build`)
- External preview URL routing depends on K8s ingress config
