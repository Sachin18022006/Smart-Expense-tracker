# SpendSense — Smart Expense Tracker

SpendSense is a full-stack expense management app that helps you take control of your
finances. Track income and expenses, visualize spending patterns through interactive
charts, filter by month, and export your data — all behind a secure, authenticated
account.

> **Live demo:** https://expense-tracker-fawn-seven-45.vercel.app
> **GitHub:** https://github.com/Sachin18022006/Smart-Expense-tracker

---

## 1. Project overview

SpendSense lets users register and log in securely, then manage their complete
financial picture in one place. Every transaction — income or expense — is stored
against the authenticated user's account. An analytics dashboard visualizes spending
across Pie, Bar, and Line charts. Transactions can be filtered by month, exported as
CSV for use in spreadsheets, and passwords can be reset via a secure token-based
email flow. The UI is clean, responsive, and works across desktop and mobile.

## 2. Features

- User registration and login with JWT authentication
- Forgot password and reset password via secure token
- Add, delete, and manage income and expense transactions
- Analytics dashboard with Pie, Bar, and Line charts (Recharts)
- Monthly filtering of transactions
- Export transaction data as CSV
- Secure password hashing with Bcrypt
- Fully responsive and mobile-friendly UI

## 3. Tech stack

| Layer | Technology |
|---|---|
| Frontend | React.js + React Router + Axios + Recharts + CSS |
| Backend | Node.js + Express.js |
| Database | MongoDB Atlas (Mongoose ODM) |
| Auth | JWT + Bcrypt |
| Deployment | Vercel (frontend) + Render (backend) |

## 4. Project structure

```
SpendSense/
├── frontend/          # React frontend
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   └── utils/
│   └── package.json
├── backend/           # Express backend
│   ├── models/
│   ├── routes/
│   ├── middleware/
│   ├── controllers/
│   └── server.js
└── README.md
```

## 5. Local setup

### Prerequisites
- Node.js 18+ and npm
- A MongoDB Atlas connection string
- An email service (for password reset emails)

### Backend
```bash
cd backend
npm install
```

Create a `.env` file inside the `backend` folder:
```
MONGO_URI=your_mongodb_connection_string
JWT_SECRET=your_long_random_secret
```

Run the backend:
```bash
npx nodemon server.js   # development (auto-restarts on file changes)
npm start               # production
```

Backend runs on `http://localhost:5000`.

### Frontend
```bash
cd frontend
npm install
npm start              # starts on http://localhost:3000
```

## 6. API endpoints

### Auth routes
| Method | Endpoint | Description |
|---|---|---|
| POST | /api/auth/register | Register a new user |
| POST | /api/auth/login | Log in, receive JWT |
| POST | /api/auth/forgot-password | Request a password reset token |
| POST | /api/auth/reset-password/:token | Reset password using token |

### Expense routes
| Method | Endpoint | Description |
|---|---|---|
| GET | /api/expenses | Get all transactions for the logged-in user |
| POST | /api/expenses | Add a new income or expense |
| DELETE | /api/expenses/:id | Delete a transaction |

All expense routes require `Authorization: Bearer <token>` in the request header.

## 7. High-level architecture

```
React Client
  ├─ Auth state: JWT stored in localStorage
  ├─ Axios instance with Authorization header injection
  └─ Pages: /login , /register , /dashboard , /analytics , /reset-password
        │
        │  REST (JSON), JWT in Authorization header
        ▼
Express API (Node.js)
  ├─ /api/auth      → register, login, forgot/reset password
  ├─ /api/expenses  → CRUD for transactions (protected)
  └─ Auth middleware → verifies JWT, attaches req.user.id
        │
        ▼
MongoDB Atlas
  ├─ Users     { name, email, password (hashed), resetToken }
  └─ Expenses  { userId, type, category, amount, date, description }
```

## 8. Authentication & authorization

- Passwords are hashed with Bcrypt before being stored — plaintext is never saved.
- On login, a JWT is signed with `JWT_SECRET` and returned to the client, which
  stores it in localStorage and sends it as `Authorization: Bearer <token>` on
  every subsequent request.
- Auth middleware on the backend decodes the token and attaches `req.user.id` to
  the request. Every expense query filters on this ID, so users can only ever see
  and modify their own data.
- Password reset uses a short-lived token sent to the user's email. The token is
  hashed and stored in the database; on reset, it is verified before the new
  password is saved.

## 9. Key design decisions & trade-offs

- **Recharts for visualization** — lightweight, React-native charting library that
  integrates cleanly without a separate canvas or D3 setup. Trade-off: less
  customizable than D3 for complex charts, but covers Pie, Bar, and Line charts
  with minimal boilerplate.
- **CSV export client-side** — transaction data is already in the browser after
  fetching, so CSV generation happens in the client without a round-trip to the
  server. Keeps the backend focused on data, not formatting.
- **Monthly filtering on the frontend** — transactions are fetched once and filtered
  in state, keeping the API simple. At scale, server-side date filtering with query
  params would be more efficient.
- **JWT in localStorage** — straightforward across separately-deployed frontend and
  backend origins. A production hardening step would move to httpOnly cookies with
  CSRF protection to mitigate XSS exposure.

## 10. Known limitations

- No recurring transaction support (e.g. monthly rent auto-entry).
- No multi-currency support — all amounts are in a single currency.
- Monthly filtering is client-side; large datasets would benefit from server-side
  pagination and date-range query params.
- No budget goal setting or spending alerts.

## 11. Deployment

| Service | Platform |
|---|---|
| Frontend | Vercel |
| Backend | Render |
| Database | MongoDB Atlas |

Environment variables (`MONGO_URI`, `JWT_SECRET`) are set in each platform's
dashboard — never committed to the repository.

---

**Author:** Sachin BS — [github.com/Sachin18022006](https://github.com/Sachin18022006)
