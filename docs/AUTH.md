# AUTH — User Authentication

> **Related file:** `FEATURE_SPEC.md`  
> **Owner:** All members (core feature)

---

## Purpose

Authentication is a **required core feature** of this application.  
Every user must have an account to use the app. All portfolio data, holdings, transactions, and backtests are tied to a logged-in user.  
Users must sign up before they can do anything in the app.

---

## Pages

### 1. Sign Up Page (`/signup`)

Let new users create an account.

**Form Fields:**

| Field | Type | Required | Rules |
|-------|------|----------|-------|
| Username | Text | ✅ | 3–20 characters, letters and numbers only |
| Email | Email | ✅ | Must be a valid email format |
| Password | Password | ✅ | Minimum 8 characters |
| Confirm Password | Password | ✅ | Must match the password field |

**Actions:**
- Click **Sign Up** → validate form → send `POST /auth/signup` → redirect to Dashboard
- Click **"Already have an account? Log in"** → navigate to Login page

**Error Handling:**
- Show inline error under each field that fails validation
- If email/username is already taken, show a clear error message

---

### 2. Login Page (`/login`)

Let existing users sign in to their account.

**Form Fields:**

| Field | Type | Required |
|-------|------|----------|
| Email | Email | ✅ |
| Password | Password | ✅ |

**Actions:**
- Click **Log In** → send `POST /auth/login` → store JWT token → redirect to Dashboard
- Click **"Don't have an account? Sign up"** → navigate to Sign Up page

**Error Handling:**
- Show a single error message: "Email or password is incorrect"
- Do not tell the user which field is wrong (for security reasons)

---

### 3. Logout

- A **Log Out** button is always available in the top navigation bar or side menu.
- Clicking it clears the JWT token from local storage and redirects to the Login page.

---

## API Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| `POST` | `/auth/signup` | Create a new user account |
| `POST` | `/auth/login` | Log in and receive a JWT token |
| `POST` | `/auth/logout` | Invalidate the current session |
| `GET`  | `/auth/me` | Get current logged-in user info |

---

## Data Model

```
User
├── user_id        (PK, INT, AUTO_INCREMENT)
├── username       (VARCHAR(20), UNIQUE, NOT NULL)
├── email          (VARCHAR(255), UNIQUE, NOT NULL)
├── password_hash  (VARCHAR(255), NOT NULL)  -- hashed with bcrypt
└── created_at     (DATETIME, DEFAULT CURRENT_TIMESTAMP)
```

---

## Notes

- Passwords must be hashed using **bcrypt** — never store plain text passwords.
- JWT tokens should expire after a reasonable time (e.g., 7 days).
- All protected API routes require an `Authorization: Bearer <token>` header.
- If the token is missing or expired, the backend should return `401 Unauthorized`.
- The frontend should redirect the user to `/login` when a `401` response is received.
