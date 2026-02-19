# Expense Tracker — Project Overview & Tech Deep Dive

A full-stack Expense Tracker web application built with a Vite + React frontend and a Node.js + Express backend using MongoDB for persistence. The app provides secure user authentication, income and expense CRUD operations, receipt uploads, and a dashboard with charts and summaries.

## Quick summary
- Authentication: JWT-based sign up / login with protected REST endpoints.
- Data: Create, read, update, delete expenses and incomes with optional receipt uploads.
- UX: Responsive React UI, chart visualizations for insights, client-side routing.

## Major features
- Secure user accounts (signup/login) with password hashing and JWT tokens.
- CRUD for expenses and incomes with categories, amounts, dates, and optional notes.
- Upload receipts/images stored on the server under `server/uploads`.
- Dashboard views: total balances, recent transactions, last-30-days chart, custom legends and tooltips.

## Tech stack (emphasis on technical details)

### Frontend
- Framework: React (JSX) — component-based UI architecture with functional components and hooks.
- Tooling: Vite for fast dev server, HMR (see `client/vite.config.js`).
- State & data flow: localized component state + context (`client/context/UserContext.jsx`) for auth state; components use hooks from `client/hooks/`.
- UI & styling: modular components under `client/src/components/` and global styles in `client/index.css`.
- Charts: custom chart components (`CustomBarChart`, `CustomLineChart`, `CustomPieChart`) using a charting library (see `client/src/components/Charts`).
- Assets & static: `client/public` for static images; build output served from `dist`.

### Backend
- Runtime: Node.js (server entry `server/server.js`) with Express for routing and middleware.
- Authentication: bcrypt for password hashing and JSON Web Tokens (JWT) for stateless sessions (auth middleware at `server/middleware/authMiddleware.js`).
- Database: MongoDB (connection in `server/config/db.js`) using Mongoose ODM for schemas and models (`server/models/*.js`).
- File upload: middleware using `multer` to accept multipart/form-data, store files under `server/uploads`, and validate file types/sizes (`server/middleware/uploadMiddleware.js`).
- Controllers & routes: clear separation — controllers in `server/controllers/` implement business logic; routes in `server/routes/` map endpoints.
- Environment & secrets: `.env` for `MONGO_URI`, `JWT_SECRET`, and other env-specific values — never commit secrets.

### Dev & Tooling
- Linting: ESLint config present at `client/eslint.config.js`.
- Package managers: `npm` (check `client/package.json` and `server/package.json` for scripts). Dev servers: `npm run dev` (client) and `npm start` / `nodemon` for server.
- Local development: run frontend on Vite dev server and backend on a separate Node process; frontend calls backend REST APIs via configured base URL in `client/src/utils/axiosInstance.js`.

## Architecture & data flow (concise)
1. User interacts with React UI → actions (create expense, upload receipt) call frontend API helpers.
2. Frontend sends HTTP requests to Express REST endpoints (auth, expenses, incomes, dashboard).
3. Express middleware validates and authenticates requests, file uploads are handled by `multer` middleware.
4. Controller logic performs validation and persists data to MongoDB via Mongoose models.
5. Responses return JSON; frontend updates UI and charts accordingly.

## Important files & paths
- Frontend entry: `client/src/main.jsx` and top-level `client/src/App.jsx`.
- Auth & user state: `client/context/UserContext.jsx`, `client/hooks/useUserAuth.jsx`.
- Backend entry: `server/server.js`.
- DB connector: `server/config/db.js`.
- Auth controller: `server/controllers/authController.js`.
- Expense & income controllers: `server/controllers/expenseController.js`, `server/controllers/incomeController.js`.
- Models: `server/models/user.js`, `server/models/expense.js`, `server/models/income.js`.
- Middleware: `server/middleware/authMiddleware.js`, `server/middleware/uploadMiddleware.js`.

## Setup & run (local)
1. Backend

```bash
cd server
npm install
# create a .env with MONGO_URI and JWT_SECRET
npm run dev # or npm start / nodemon server.js
```

2. Frontend

```bash
cd client
npm install
npm run dev
```

Notes
- Frontend expects the backend base URL configured in `client/src/utils/axiosInstance.js` — update during local dev if backend runs on a non-default port.
- Use `nodemon` for auto-restarting the server during development; Vite handles frontend HMR.

## Deployment notes
- Production build: `cd client && npm run build` to create `dist`.
- Serving options: host `dist` via CDN or configure Express to serve static files from the client `dist` folder for a single hosting target.
- Security: enforce HTTPS, set strong `JWT_SECRET`, sanitize uploads, and set appropriate CORS policy.

## API snapshot (key endpoints)
- POST `/api/auth/signup` — register user.
- POST `/api/auth/login` — returns JWT.
- GET `/api/expenses` — list user expenses (protected).
- POST `/api/expenses` — create expense (supports multipart for receipt upload).
- PUT `/api/expenses/:id` — update expense.
- DELETE `/api/expenses/:id` — delete expense.

## Next steps I can take
- Run linters or tests, or update README further with environment variable examples and sample requests.