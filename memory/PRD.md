# Y-Store Marketplace - PRD

## Original Problem Statement
Клонувати репозиторій https://github.com/svetlanaslinko057/bbbbbb
Підняти фронт, бек, базу даних MongoDB
Вивчити структуру коду, архітектуру, адмінку
Продовжити доробку O20.3 Return Management Engine

## Architecture
- **Frontend**: React 19, TailwindCSS, Radix UI
- **Backend**: FastAPI (Python 3.11), modular architecture
- **Database**: MongoDB (Motor async driver)
- **Bot**: Aiogram 3.x Telegram bot
- **Integrations**: Nova Poshta API, RozetkaPay, Fondy, AI (OpenAI)

## Tech Stack
- FastAPI with async/await
- MongoDB with Motor
- JWT authentication
- APScheduler for background jobs
- Aiogram for Telegram bot

## What's Been Implemented

### Session 2026-02-20
- [x] Cloned repository and set up environment
- [x] Configured .env files (backend + frontend)
- [x] Added Emergent LLM Key
- [x] Added Telegram Bot Token
- [x] Added Nova Poshta API Key

### O20.3 Return Management Engine - COMPLETED
- [x] `/app/backend/modules/returns/return_types.py` - Types (ReturnDetection, ReturnStage, ReturnReason)
- [x] `/app/backend/modules/returns/return_mapping.py` - NP status mapping for return detection
- [x] `/app/backend/modules/returns/return_repo.py` - Repository (idempotency, ledger, CRM, alerts)
- [x] `/app/backend/modules/returns/return_engine.py` - Main processing engine
- [x] `/app/backend/modules/returns/return_analytics.py` - KPI calculations
- [x] `/app/backend/modules/returns/return_scheduler.py` - Background scheduler
- [x] `/app/backend/modules/returns/return_routes.py` - API endpoints

### O20.3 API Endpoints
- `POST /api/v2/admin/returns/run` - Manual trigger return engine
- `GET /api/v2/admin/returns/list` - List returns with pagination
- `GET /api/v2/admin/returns/summary` - Return analytics KPIs
- `GET /api/v2/admin/returns/trend` - Daily return trend
- `GET /api/v2/admin/returns/risk-customers` - High-risk customers
- `POST /api/v2/admin/returns/resolve` - Mark return resolved
- `POST /api/v2/admin/returns/find` - Find by TTN
- `POST /api/v2/admin/returns/process-ttn` - Process single TTN

### O20.3 Telegram Bot Commands
- `/returns_today` - Return KPIs summary
- `/returns_risk` - High-risk customers
- `/return_find <ttn>` - Find return by TTN

### O20.4 Ops Dashboard Integration
- [x] Returns block added to `/api/v2/admin/ops/dashboard`
- [x] KPIs: today, 7d, 30d returns
- [x] Return rate, COD refusal rate
- [x] Shipping losses
- [x] Top reasons, top cities

## Core Features (Existing)
- Product catalog with categories
- Shopping cart
- Order management with state machine
- Payment integrations (Stripe, Fondy, RozetkaPay)
- Nova Poshta TTN creation
- CRM with customer segmentation
- Admin panel
- Risk scoring
- Guard (fraud detection)
- Pickup control

## User Personas
1. **Customer** - Browse, buy, track orders
2. **Admin** - Manage orders, products, returns
3. **Operator** - Handle logistics, returns

## Next Action Items (P0)
1. O20.4 - Return KPI Admin UI page
2. Return Policy Engine (auto-block COD)
3. City Risk Heatmap

## Backlog (P1)
- Predictive return probability
- Auto-cancel 30+ days orders
- Viber/SMS fallback notifications
- Return analytics charts in admin

## Known Issues
- External URL routing issue (preview environment config)
- broadcast_wizard has a minor bug with db["bot_keyboards"] call

## Environment
- Backend: `http://localhost:8001`
- Frontend: `http://localhost:3000`
- MongoDB: `mongodb://localhost:27017`
- Telegram Bot: Running (pid exists)
