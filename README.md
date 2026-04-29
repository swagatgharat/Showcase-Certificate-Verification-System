# CertVerify - Certificate Verification System

CertVerify is a comprehensive, production-grade digital certificate issuance and verification platform. It enables organizations (Issuers) to securely issue tamper-evident certificates that individuals (Holders) can verify, download, and manage.

## 🌟 Project Overview

The system solves the problem of **credential fraud** and **manual verification bottlenecks**. By providing a centralized, secure platform using modern cryptographic practices (JWT, OTP hashing) and background processing, CertVerify ensures that every certificate is authentic and easily verifiable by third parties.

**Target Users:**

- **Issuers**: Schools, universities, or corporate entities that issue certificates.
- **Holders/Applicants**: Students or employees who receive and share certificates.
- **Public/Recruiters**: Anyone needing to verify the authenticity of a credential.
- **Administrators**: Platform owners who manage users and system health.

---

## 🛠️ Tech Stack

### Frontend

- **Core**: React.js (Vite)
- **State Management**: Zustand
- **Styling**: Vanilla CSS (Modern Aesthetics, Glassmorphism)
- **Networking**: Axios (with interceptors for CSRF & Auth Refresh)
- **Components**: Atomic design pattern (Input, Button, Layout)

### Backend

- **Runtime**: Node.js (Express)
- **Database**: MongoDB (Mongoose)
- **Caching**: Upstash Redis
- **Background Tasks**: BullMQ (Redis-backed)
- **Security**:
  - `csrf-csrf`: Double CSRF protection
  - `jsonwebtoken`: Access & Refresh Token flow
  - `bcryptjs`: Password & OTP hashing
  - `helmet`: Security headers
  - `express-rate-limit`: Brute-force protection

---

## 🏗️ Architecture Overview

The project follows a **Modular Layered Architecture**:

1.  **Route Layer**: Handles incoming requests and maps them to controllers.
2.  **Middleware Layer**: Manages security (CSRF, JWT), validation (Joi), and global error handling.
3.  **Controller Layer**: Orchestrates requests; validates input and interacts with services.
4.  **Service Layer**: Contains the core business logic (e.g., `mailService`, `authService`, `cacheService`).
5.  **Model Layer**: Mongoose schemas defining the data (using Discriminators for User roles).
6.  **Queue Layer**: Handles asynchronous tasks like bulk certificate generation via BullMQ.

### Authentication Flow

- **Registration**: User registers -> Pending approval (for Issuers) or Auto-active (Holders).
- **Login**: Credentials -> 2FA OTP (if enabled) -> Identity verification.
- **Tokens**:
  - **Access Token**: Short-lived (15m), stored in HTTPOnly cookie.
  - **Refresh Token**: Long-lived (7d), stored in HTTPOnly cookie and DB.
- **CSRF**: Double Token Pattern ensures every state-changing request (POST/PUT/DELETE) is guarded.

---

## 📂 Folder Structure

```text
├── backend/
│   ├── config/          # Service configurations (DB, Redis, Mailer)
│   ├── controllers/     # Request handlers
│   ├── middlewares/     # Auth, CSRF, Rate-limiting, Error Handling
│   ├── models/          # Mongoose models (User, Certificate, RefreshToken)
│   ├── queues/          # BullMQ background task definitions
│   ├── routes/          # Express route definitions
│   ├── services/        # Business logic & 3rd party integrations
│   ├── schemas/         # Joi validation schemas
│   └── server.js        # Entry point
└── frontend/
    ├── src/
    │   ├── components/  # Atomic & Layout components
    │   ├── context/     # React contexts
    │   ├── store/       # Zustand store (authStore)
    │   ├── pages/       # View components (grouped by feature)
    │   ├── utils/       # Axios instance, helpers, API paths
    │   └── App.jsx      # Main routing & entry
```

---

## 🔐 Security Features

1.  **CSRF Protection**: Uses `csrf-csrf` (Double Cookie-Header pattern). The frontend captures the token and attaches it to all non-GET requests.
2.  **JWT Strategy**: Uses a silent refresh flow. Access tokens are short-lived; refresh tokens reside in secure cookies.
3.  **Rate Limiting**: Applied globally and strictly to sensitive auth routes to prevent credential stuffing.
4.  **Ownership Logic**: Middleware verifies that Issuers can only modify/delete certificates they personally issued.
5.  **2FA**: OTP-based verification for login and sensitive actions like downloading private credentials.

---

## 🚀 Installation & Setup

### Prerequisites

- Node.js (v18+)
- MongoDB Atlas account
- Upstash Redis account
- ImageKit account (for signatures/avatars)

### Backend Setup

1. `cd backend`
2. `npm install`
3. Create a `.env` file (see [Environment Variables](#environment-variables))
4. `npm run dev`

### Frontend Setup

1. `cd frontend`
2. `npm install`
3. Check `src/utils/apiPath.js` for `BASE_URL`.
4. `npm run dev`

---

## 📄 Environment Variables

| Variable                         | Description                             |
| :------------------------------- | :-------------------------------------- |
| `PORT`                           | Backend server port (default 8000)      |
| `MONGO_URI`                      | MongoDB connection string               |
| `JWT_ACCESS_SECRET`              | Secret for Access Token signing         |
| `JWT_REFRESH_SECRET`             | Secret for Refresh Token signing        |
| `CSRF_SECRET`                    | Secret for Double CSRF generation       |
| `EMAIL_USER` / `EMAIL_PASS`      | Nodemailer SMTP credentials             |
| `IMAGEKIT_*`                     | ImageKit public/private keys & endpoint |
| `REDIS_URL`                      | Upstash Redis connection string         |
| `ADMIN_EMAIL` / `ADMIN_PASSWORD` | Default admin credentials               |

---

## 🔌 API Documentation (Excerpt)

| Group      | Method | Route                            | Purpose                                 |
| :--------- | :----- | :------------------------------- | :-------------------------------------- |
| **Auth**   | POST   | `/api/auth/login`                | Credentials validation & OTP initiation |
| **Auth**   | GET    | `/api/auth/csrf-token`           | Fetch initial CSRF token                |
| **Issuer** | POST   | `/api/certificates/upload`       | Single certificate issuance             |
| **Issuer** | POST   | `/api/certificates/upload-excel` | Bulk certificate issuance               |
| **Holder** | GET    | `/api/certificates/:id`          | Public verification of a certificate    |
| **Admin**  | PUT    | `/api/admin/users/:id/approve`   | Approve a new Issuer account            |

---

## 🔮 Future Improvements

- **TypeScript**: Full migration to provide better type safety.
- **Audit Logs**: Database tracking of all admin and issuer modifications.
- **Webhooks**: Notify external systems when a certificate is verified.

---

## 🤝 Contributing

1. Fork the project.
2. Create your feature branch (`git checkout -b feature/AmazingFeature`).
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`).
4. Push to the branch (`git push origin feature/AmazingFeature`).
5. Open a Pull Request.

---

*Designed & developed [Swagat Gharat](https://github.com/swagatgharat)*
