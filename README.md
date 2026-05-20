# LocalHire - Job Portal Platform 

LocalHire is a modern, full-stack job recruitment platform built on the MERN stack. Designed with a strict Domain-Driven Architecture, it serves two distinct user groups—**Aspirants** (Job Seekers) and **Organizations** (Employers)—offering tailored dashboards, smart job recommendations, and seamless profile management.

---

<img width="1920" height="1280" alt="Shots_so_002" src="https://github.com/user-attachments/assets/7374291a-18a4-4682-a07f-1bb9d1da1b0b" />

---

## Core Features

*   **Role-Based Dashboards:** Completely isolated experiences for Aspirants and Companies.
*   **Smart Recommendation Engine:** Dynamically suggests jobs to Aspirants based on a matching algorithm evaluating their skills, preferred job type, and location.
*   **Application Tracking System:** Real-time tracking of job applications (Applied, Under Review, Shortlisted, Hired, Rejected).
*   **Centralized Activity Logging:** Asynchronous, non-blocking logging system tracking user actions (logins, profile updates, applications) for the "Recent Activity" timeline.
*   **Dynamic Profile Wizard:** A multi-step, state-preserving profile builder featuring responsive Material UI components.
*   **Security First:** Rate-limited endpoints, robust JWT HttpOnly cookies, NoSQL injection protection, and comprehensive role-based route guards.

---

## 🛠 Tech Stack

**Frontend:** React 18 (Vite), Material-UI (MUI), Axios, React Router, date-fns, React Hot Toast.
**Backend:** Node.js, Express.js, JWT, bcrypt.
**Database:** MongoDB, Mongoose (with highly optimized compound indexes).

---

##  Quick Start (Local Development)

Think you can run this in 5 minutes? You can.

### Prerequisites
*   Node.js (v16+)
*   MongoDB Instance (Local or Atlas)

### 1. Clone & Install
```bash
git clone https://github.com/HarshUserethe/LocalHire-Jobs-Portal.git
cd LocalHire-Jobs-Portal

# Install server dependencies
cd server
npm install

# Install client dependencies
cd ../client
npm install
```

### 2. Environment Variables
Create a `.env` file in the **`/server`** directory based on `.env.example`:
```env
PORT=5000
MONGO_URI=mongodb+srv://<username>:<password>@cluster.mongodb.net/localhire
SECRET=your_super_secret_jwt_key
CLIENT_URL=http://localhost:5173
NODE_ENV=development
```
Create a `.env` file in the **`/client`** directory:
```env
VITE_FETCH_URI=http://localhost:5000
```

### 3. Run the App
Start both servers simultaneously (or run in separate terminals):
```bash
# Terminal 1 (Backend)
cd server
npm run dev

# Terminal 2 (Frontend)
cd client
npm run dev
```
The app will be running at `http://localhost:5173`.

---

## Project Structure

The codebase strictly follows Domain-Driven Design (DDD) to keep concerns separated and maintainable.

```text
/client/src
 ├── /aspirant        # Job seeker specific pages/components (Dashboard, Profile)
 ├── /organization    # Company specific pages/components
 ├── /shared          # Reusable UI, AuthContext, Layouts, Utilities
 └── App.jsx          # Root routing logic

/server/src
 ├── /controllers     # Business logic (authController, dashboardController)
 ├── /models          # Mongoose Schemas (user, job, application, log)
 ├── /routes          # Express API definitions
 ├── /services        # Abstraction layer for DB ops (logService, userService)
 └── /utils           # Global error handlers and async catchers
```

---

##  Error Handling & Validation

*   **Global Catchers:** Every backend controller is wrapped in a `catchAsync` utility, entirely eliminating unhandled promise rejections.
*   **Sanitization:** Sensitive fields (like `password`, `role`) are explicitly stripped from direct `req.body` updates to prevent privilege escalation.
*   **Graceful Fallbacks:** The frontend handles missing profile data gracefully without crashing, utilizing loading spinners and skeleton states.

---

##  API Overview

All routes are prefixed with `/api/v1/`.

| Endpoint | Method | Protected | Description |
| :--- | :--- | :--- | :--- |
| `/auth/login` | `POST` | No | Authenticates user, issues HttpOnly JWT cookie |
| `/auth/me` | `GET` | Yes | Retrieves current session user |
| `/users/:id/profile` | `PATCH` | Yes | Granular role-based profile updates |
| `/dashboard/aspirant/stats` | `GET` | Yes | Aggregates application metrics |
| `/dashboard/aspirant/recommended-jobs` | `GET` | Yes | Runs `$or` constraint matching engine |

---

## Feature Deep Dive: The Recommendation System

To avoid showing users empty dashboards, the recommendation system relies on a dynamic MongoDB `$or` query constraint rather than a rigid intersection constraint. 

1.  **The Trigger:** When the Dashboard mounts, it pings the backend.
2.  **The Evaluation:** The controller pulls the user's `aspirantProfile`. It constructs a query that looks for `{ status: 'active' }`.
3.  **The Match:** It appends conditions looking for matches in `skills` (using `$in`), `jobcity` (Regex partial matching), or `jobType`.
4.  **The Result:** A highly optimized compound index on the `Jobs` collection ensures this query returns the top 5 most relevant jobs in milliseconds.

---

##  UI/UX Considerations

*   **Minimalism & Padding:** Heavy reliance on MUI's `<Paper>` and `<Container>` elements with deliberate whitespace mapping to create a "breathing" UI.
*   **Wizard Patterns:** Profile updates are handled via a step-by-step interactive wizard rather than an intimidating 50-field mega-form.
*   **Feedback Loops:** Every action (save, apply, delete) triggers instant Toast notifications via `react-hot-toast`.

---

## 🔐 Security Standards

1.  **JWT Handling:** Tokens are NEVER stored in `localStorage`. They are transmitted via secure, `HttpOnly` cookies.
2.  **Rate Limiting:** The `/api` endpoints are strictly limited (e.g., 10 profile updates per 15 mins) to prevent spam and abuse.
3.  **Data Sanitization:** `express-mongo-sanitize` is implemented to prevent NoSQL query injection attacks.

---
