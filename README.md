# Finora - AI-Powered Personal Finance & Wealth Management

![Status](https://img.shields.io/badge/status-active-success.svg)
![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)

> **Finora** is a comprehensive full-stack personal finance application that empowers users to take complete control of their financial health. Combining intelligent expense tracking, AI-driven insights, and goal-based financial planning, Finora transforms how users manage wealth.

---

## 🎯 Features

### Core Capabilities
- **💸 Intelligent Expense Tracking** - Categorize and monitor daily expenses with dynamic form systems
- **🧾 Receipt Scanning** - OCR-powered receipt capture and expense categorization (EasyOCR)
- **🎯 Financial Goal Architect** - Set, edit, and visualize savings targets with deposit tracking and auto-completion
- **📈 Investment Management** - Track holdings, live price quotes, price history charts, technical indicators (RSI/MACD/EMA), and portfolio analytics
- **🧠 Neural Coach (AI)** - On-device-server AI chat providing spending analysis and financial recommendations (no external LLM required)
- **💰 Salary Reality Check** - Analyze income across ~50 countries using PPP-based cost-of-living tiers with live exchange rates

---

## 🛠️ Tech Stack

### Frontend (`Finora/`)
- **Framework**: React Native 0.81.5 + React 19 (New Architecture enabled)
- **Routing**: Expo Router ~6.0.23 (file-based routing, typed routes)
- **State Management**: Zustand + TanStack React Query v5 (server cache)
- **Secure Storage**: expo-secure-store (JWT tokens persisted encrypted)
- **HTTP Client**: Axios ^1.13.6 (with automatic token refresh interceptor)
- **Charting**: Victory Native ^41 + @shopify/react-native-skia
- **UI**: Expo Vector Icons, Reanimated ~4.1, Linear Gradients, Gifted Chat

### Backend (`finora-backend/`)
- **Framework**: Django ≥6.0.1 + Django REST Framework ≥3.16
- **Authentication**: JWT via SimpleJWT (1-day access / 30-day refresh, rotation enabled)
- **AI Engine**: Local hybrid ML pipeline — TF-IDF/LogisticRegression → PyTorch MLP (`FinoraNet`) → regex fallback for intent classification; rule-based response generation; yfinance for live market data
- **Database**: SQLite (default) — swap to PostgreSQL for production
- **Async Tasks**: Celery + Redis (optional; auto price refresh every 15 min)
- **API Structure**: Modular apps (users, transactions, goals, investments, salary_reality, ai_coach)

---

## 🚀 Getting Started

### Prerequisites
- Node.js 18+ (frontend)
- Python 3.10+ (backend)
- npm (frontend)
- pip (backend)

### Frontend Setup

```bash
# Navigate to frontend
cd Finora

# Install dependencies
npm install

# Run development server
npm start

# Platform-specific
npm run android  # Android build/dev
npm run ios      # iOS build/dev
npm run web      # Web preview
```

> **API Base URL**: Configured automatically in `services/api.js` — uses your LAN IP on native (derived from the Metro packager host), `127.0.0.1:8000` on web, and `10.0.2.2:8000` on the Android emulator. Make sure the backend is running and reachable from your device.

### Backend Setup

```bash
# Navigate to backend
cd finora-backend

# Create virtual environment
python -m venv venv
venv\Scripts\activate        # Windows
source venv/bin/activate     # macOS/Linux

# Install dependencies
pip install -r requirements.txt

# Optional: receipt OCR support
pip install easyocr

# Database setup
python manage.py migrate

# Create superuser
python manage.py createsuperuser

# Run development server
python manage.py runserver
```

### Environment Variables (optional)

No `.env` file is required for development — sensible defaults are used. Override in production:

| Variable | Default | Description |
|---|---|---|
| `DEBUG` | `True` | Set to `False` in production |
| `SECRET_KEY` | dev fallback key | **Must** set a strong unique key in production |
| `EXCHANGE_RATE_API_URL` | exchangerate-api.com | Live FX rates source |
| `REDIS_URL` | local fallback | Celery/cache broker |

---

## 📁 Project Structure

```
Finora-App/
├── Finora/                             # Frontend (React Native + Expo Router)
│   ├── app/
│   │   ├── _layout.jsx                 # Root stack (React Query + Auth providers)
│   │   ├── index.jsx                   # Welcome / onboarding gate
│   │   ├── login.jsx                   # Email/password login
│   │   ├── register.jsx                # Registration
│   │   ├── (tabs)/
│   │   │   ├── _layout.jsx             # Floating pill tab bar
│   │   │   ├── index.jsx               # Dashboard (charts, overview)
│   │   │   ├── expenses.jsx            # Transaction list + analytics
│   │   │   ├── goals.jsx               # Goal management
│   │   │   ├── invest.jsx              # Holdings + portfolio analytics
│   │   │   ├── salary-reality.jsx      # Salary analysis form/results
│   │   │   ├── ai.jsx                  # Neural Coach chat
│   │   │   └── profile.jsx             # Profile (hidden tab, routable)
│   │   ├── add-expense.jsx             # Modal: new expense + receipt scan
│   │   ├── add-goal.jsx                # Modal: new goal
│   │   ├── add-investment.jsx          # Modal: new holding
│   │   ├── edit-expense.jsx            # Edit transaction
│   │   ├── edit-goal.jsx               # Edit goal
│   │   ├── edit-investment.jsx         # Edit holding
│   │   └── investment-detail/[id].jsx  # Price chart + add units
│   ├── services/
│   │   ├── api.js                      # Axios client, JWT refresh flow, all API groups
│   │   └── queryClient.js              # React Query configuration
│   ├── context/
│   │   └── AuthContext.js              # Auth state (SecureStore-backed session)
│   ├── store/
│   │   └── useFinanceStore.js          # Zustand dashboard cache
│   ├── theme.js                        # Design tokens (colors, spacing, typography)
│   ├── app.json
│   └── package.json
│
├── finora-backend/                     # Backend (Django REST Framework)
│   ├── settings.py                     # Project settings (root-level)
│   ├── urls.py                         # Root URL routing (/api/...)
│   ├── config/                         # Celery application wiring
│   ├── users/                          # Custom User model, auth, dashboard
│   │   ├── models.py                   # User(AbstractUser), email login
│   │   ├── views.py                    # Register/login/refresh/profile/dashboard
│   │   ├── serializers.py
│   │   └── urls.py                     # /api/auth/
│   ├── transactions/                   # Expense/income tracking + OCR
│   │   ├── models.py                   # Transaction (UUID PK, 13 categories)
│   │   ├── views.py                    # CRUD, scan_receipt (EasyOCR), analytics
│   │   └── urls.py                     # /api/transactions/
│   ├── goals/                          # Savings goals
│   │   ├── models.py                   # Goal, GoalDeposit (auto-progress)
│   │   ├── views.py
│   │   └── urls.py                     # /api/goals/
│   ├── investments/                    # Portfolio tracking
│   │   ├── models.py                   # Asset, Holding, PriceHistory (+indicators)
│   │   ├── views.py                    # CRUD, quotes, search, chart, analytics
│   │   ├── tasks.py                    # Celery price updates
│   │   ├── continuous_pipeline.py      # Price collection + indicator computation
│   │   └── urls.py                     # /api/investments/
│   ├── salary_reality/                 # PPP cost-of-living engine
│   │   ├── models.py                   # SalaryProfile, SalarySnapshot
│   │   ├── salary_logic.py             # Tier analysis, PPP factors, FX rates
│   │   └── urls.py                     # /api/salary/
│   ├── ai_coach/                       # Neural Coach AI
│   │   ├── ai_logic.py                 # Intent cascade + rule-based responses
│   │   ├── ml_model.py                 # FinoraNet (PyTorch MLP)
│   │   ├── intent_sklearn.py           # TF-IDF + LogisticRegression classifier
│   │   ├── ai_model/                   # Trained weights (.pth, .joblib)
│   │   └── urls.py                     # /api/ai/
│   ├── manage.py
│   └── requirements.txt
├── .gitignore
└── README.md
```

---

## 🔌 API Endpoints

Base URL: `http://<host>:8000/api/`

### Authentication
```
POST   /api/auth/register/           - User registration
POST   /api/auth/login/              - User login (returns JWT pair)
POST   /api/auth/refresh/            - Refresh access token
GET    /api/auth/profile/            - Get user profile
PUT    /api/auth/profile/            - Update user profile
GET    /api/auth/dashboard/          - Financial overview (balance, expenses, goals)
```

### Transactions
```
GET    /api/transactions/            - List transactions
POST   /api/transactions/            - Create transaction
GET    /api/transactions/{id}/       - Retrieve transaction
PUT    /api/transactions/{id}/       - Update transaction
DELETE /api/transactions/{id}/       - Delete transaction
POST   /api/transactions/scan/       - OCR receipt scan (multipart upload)
GET    /api/transactions/analytics/  - Spending analytics
```

### Goals
```
GET    /api/goals/                       - List goals
POST   /api/goals/                       - Create goal
GET    /api/goals/{id}/                  - Goal detail
PUT    /api/goals/{id}/                  - Update goal
DELETE /api/goals/{id}/                  - Delete goal
GET    /api/goals/{id}/deposits/         - List deposits for a goal
POST   /api/goals/{id}/deposits/         - Add deposit (auto-updates progress)
```

### Investments
```
GET/POST          /api/investments/assets/            - Asset catalog
GET/POST          /api/investments/holdings/          - List/create holdings
GET/PUT/DELETE    /api/investments/holdings/{id}/     - Holding detail
POST              /api/investments/holdings/{id}/add-units/ - Buy more units
GET               /api/investments/price-history/     - Historical prices
GET               /api/investments/quote/?symbol=     - Live quote (yfinance)
POST              /api/investments/refresh-prices/    - Force price refresh
GET               /api/investments/search/?q=         - Symbol search
GET               /api/investments/chart/             - Chart data series
GET               /api/investments/analytics/         - Portfolio analytics
```

### Salary Reality
```
POST   /api/salary/analyse/          - Analyze salary across locations (PPP tiers)
GET    /api/salary/profile/          - Get saved salary profile
PUT    /api/salary/profile/          - Update salary profile
```

### AI Coach (Neural Coach)
```
GET    /api/ai/insight/              - Proactive spending insights
POST   /api/ai/chat/                 - Send message to AI coach
GET    /api/ai/chat/history/         - Retrieve conversation history
```

All endpoints require `Authorization: Bearer <access_token>` except register/login/refresh.

---

## 🧠 How the Neural Coach Works

The AI Coach runs **entirely locally** — no external LLM API calls, no API keys:

1. **Intent Classification Cascade**
   - Stage 1: TF-IDF + Logistic Regression (confidence ≥ 0.28)
   - Stage 2: PyTorch `FinoraNet` bag-of-words MLP (fallback, confidence ≥ 0.22)
   - Stage 3: Regex keyword matcher (final fallback)
2. **Response Generation** — Rule-based templates personalized with your real data (balance, budgets, goals, portfolio health score)
3. **Live Market Data** — yfinance integration for `$TICKER` queries and cached index/crypto quotes (SPY, BTC, QQQ, Gold; 5-min cache)

Supported intents include budgeting, balance checks, spending analysis, goals, portfolio review, investing basics, debt, tax, crypto, retirement, real estate, and market status.

---

## 🔐 Security Notes

✅ **Implemented:**
- JWT authentication with token rotation
- Tokens stored in device SecureStore (encrypted)
- All data endpoints enforce per-user queryset filtering
- Password validation on registration

⚠️ **Required before production:**
```python
# In finora-backend/settings.py (via environment):
DEBUG = False
SECRET_KEY = <strong unique key>
ALLOWED_HOSTS = ["yourdomain.com"]
CORS_ALLOWED_ORIGINS = ["https://your-app-domain"]
SECURE_SSL_REDIRECT = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
X_FRAME_OPTIONS = 'DENY'
```

> ⚠️ The current defaults (`DEBUG=True`, wildcard hosts/CORS, SQLite) are for **local development only**. Do not deploy without hardening.

---

## 🧪 Testing

> ⚠️ A formal test suite is not yet in place — this is a known gap tracked for upcoming work.

Planned coverage:
- Unit tests for business logic (salary engine, AI intent cascade, goal deposits)
- Integration tests for API endpoints
- E2E tests for critical user flows

---

## 📊 Performance Optimization

### Frontend
- React Query caching (5-min stale time) + pull-to-refresh refetching
- Lazy route loading via Expo Router
- Skia-accelerated chart rendering

### Backend
- Module-level caching for market quotes (5 min) and FX rates (1 hour)
- Celery beat scheduled price updates (15-min interval)
- Planned: pagination for list endpoints, rate limiting for AI chat

---

## 🚢 Deployment

### Backend (Production)
```bash
pip install gunicorn psycopg2-binary
gunicorn --bind 0.0.0.0:8000 config.wsgi:application  # if using config package
# or: gunicorn --bind 0.0.0.0:8000 wsgi:application

# Environment
export DEBUG=False
export SECRET_KEY=your-secret-key
# Configure DATABASE_URL for PostgreSQL and run migrations
```

### Frontend (Production)
```bash
# Build with EAS
eas build --platform all

# Or local builds
npx expo run:ios --configuration Release
npx expo run:android --variant release
```

> **Note:** Before building for production, replace the dev-only base URL logic in `Finora/services/api.js` with your production API host (an `EXPO_PUBLIC_API_URL` environment variable is recommended).

---

## 🤝 Contributing

1. Create a feature branch from `main`
   ```bash
   git checkout -b feature/your-feature-name
   ```

2. Commit with semantic messages
   ```bash
   git commit -m "feat: description of feature"
   git commit -m "fix: description of fix"
   git commit -m "refactor: description of refactor"
   ```

3. Push and create a Pull Request
   ```bash
   git push origin feature/your-feature-name
   ```

4. PR should include:
   - Clear description of changes
   - Tests for new functionality
   - Updated documentation

---

## 📝 Commit Message Guidelines

Follow conventional commits:
```
feat:     New feature
fix:      Bug fix
refactor: Code restructuring
docs:     Documentation
test:     Test additions
chore:    Build/dependencies
```

Example:
```bash
git commit -m "feat: Add salary reality comparison chart

- Implemented location-based salary analysis
- Added tier comparison visualization
- Integrated with salary_reality API endpoints
"
```

---

## 🐛 Bug Reports & Issues

Report issues via GitHub Issues with:
- Clear title and description
- Steps to reproduce
- Expected vs. actual behavior
- Environment details (OS, versions)
- Screenshots/logs if applicable

---

## 📞 Support & Contact

- **Author**: Chaudhary Huzaifa
- **Email**: huzaifa928.fui@gmail.com
- **Repository**: [chhuzaifa928/finora](https://github.com/chhuzaifa928/finora)

---

## 📄 License

This project is licensed under the MIT License - see the LICENSE file for details.

---

## 🙏 Acknowledgments

- Expo team for React Native development tools
- Django REST Framework community
- Contributors and testers

---

**Last Updated**: August 24, 2026
**Status**: Active Development
