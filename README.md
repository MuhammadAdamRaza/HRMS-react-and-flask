# HRMS: Human Resource Management System

A web-based HR management system with a React frontend and a Flask API backend. It supports employee and admin dashboards, leave management, task management, a shared calendar, document uploads, real-time notifications, audit logs, and role-based permissions. The interface is available in English and Urdu.

## Features

- **Authentication:** Register, login, logout, refresh tokens, profile updates, change password, and forgot/reset password by email.
- **Role-based access:** Create roles, assign permissions, and assign roles to users through the permission middleware.
- **User management:** Admins can create, view, update, and delete users.
- **Leave management:** Employees request leave. Admins approve or reject it. Leave dates are checked before submission.
- **Task management:** Create, assign, update, and delete tasks.
- **Calendar:** Calendar events and summaries for leaves and tasks.
- **Documents:** Upload and download files. Files are stored with Cloudinary.
- **Notifications:** Real-time notifications through Socket.IO, with mark-as-read and clear options.
- **Audit logs:** Records of important actions, viewable by admins.
- **Analytics:** Admin and employee dashboard data.
- **Multilingual:** English and Urdu translations, with a language switcher.
- **Installable:** PWA support with a service worker and manifest.
- **API documentation:** Swagger UI at `/apidocs/`.

## Tech Stack

**Frontend (`hr_frontend/`)**

- React with Vite
- Tailwind CSS and Radix UI components
- FullCalendar for the calendar view
- i18next for translations
- Vitest and Testing Library for tests

**Backend (`hr_backend/`)**

- Python with Flask 3.1
- SQLAlchemy with PostgreSQL (Neon)
- Flask-JWT-Extended for authentication
- Flask-SocketIO for real-time updates
- Flask-Mail for email, Flask-Limiter for rate limiting, Flask-Babel for translations
- Cloudinary for file storage
- Gunicorn for production serving, pytest for tests

## Project Structure

```
HRMS/
├── .github/workflows/ci.yml   # Frontend test and build workflow
├── hr_backend/
│   ├── requirements.txt
│   ├── migrate_to_neon.py     # Database migration helper
│   ├── tests/                 # pytest suite
│   ├── translations/          # Babel translation files
│   └── src/
│       ├── main.py            # Flask app entry point
│       ├── config.py
│       ├── seed_data.py
│       ├── socket_events.py
│       ├── models/            # User, Role, Leave, Task, Document, Notification, AuditLog, tokens
│       ├── routes/            # auth, user, role, leave, task, calendar, document, notification, analytics, audit_log, lang
│       ├── utils/             # permissions, notifications, password validation, audit logging
│       └── static/            # Built frontend served by Flask
└── hr_frontend/
    ├── package.json
    ├── vite.config.js
    ├── src/
    │   ├── App.jsx
    │   ├── api/               # API calls
    │   ├── components/
    │   │   ├── auth/          # Login, register, forgot/reset password
    │   │   ├── dashboard/     # Dashboards, leave, tasks, users, roles, calendar, audit logs
    │   │   └── ui/            # Reusable UI components
    │   ├── contexts/          # Auth and notification state
    │   ├── locales/           # en and ur translations
    │   └── tests/             # Vitest tests
    └── public/                # Icons, manifest, service worker
```

## Getting Started

### Prerequisites

- Python 3.11 or newer (recommended)
- Node.js 20 and pnpm 9
- A PostgreSQL database (Neon is used in this project)

### 1. Backend setup

```bash
cd hr_backend
python -m venv venv
venv\Scripts\activate        # Windows
# source venv/bin/activate   # macOS/Linux
pip install -r requirements.txt
```

Create `hr_backend/.env`:

```env
SQLALCHEMY_DATABASE_URI=postgresql://USER:PASSWORD@HOST/DBNAME
SECRET_KEY=change-me
JWT_SECRET_KEY=change-me-too
MAIL_USERNAME=your-email@gmail.com
MAIL_PASSWORD=your-app-password
MAIL_DEFAULT_SENDER_NAME=HRMS
MAIL_DEFAULT_SENDER_EMAIL=your-email@gmail.com
CLOUDINARY_CLOUD_NAME=your-cloud-name
CLOUDINARY_API_KEY=your-api-key
CLOUDINARY_API_SECRET=your-api-secret
FRONTEND_URL=http://localhost:5173
```

Start the server:

```bash
python src/main.py
```

The API runs at http://localhost:5000, and Swagger docs are at http://localhost:5000/apidocs/.

### 2. Frontend setup

```bash
cd hr_frontend
pnpm install
pnpm dev
```

Open http://localhost:5173.

### Run tests

Backend:

```bash
cd hr_backend
pytest
```

Frontend:

```bash
cd hr_frontend
pnpm vitest --run
```

### Build the frontend

```bash
cd hr_frontend
pnpm build
```

## CI

The GitHub Actions workflow in `.github/workflows/ci.yml` runs on pushes and pull requests to `main`. It installs dependencies with pnpm, runs the frontend tests, and builds the frontend on Windows.

## Deployment

The app is deployed on Railway. The backend serves the built React app from `hr_backend/src/static/`, so the frontend build must be copied there before deployment. Set the environment variables above in Railway's dashboard.

## Notes Before Production

- Replace the default secrets. The code falls back to `dev-secret`, `dev-jwt-secret`, and `supersecretkey` when the variables are not set.
- Remove committed database files (`hr_backend/database/app.db`, `hr_backend/src/database/app.db`, `hr_backend/src/database/new_app.db`) and the `.env` file from the repository if they contain real data.
- Keep credentials out of the repository. Rotate any secrets that were committed.
- The rate limiter uses in-memory storage by default. Set `REDIS_URL` for multi-instance deployments.
- The `dist/` folders and the copies in `hr_backend/src/static/` are build outputs. Rebuild them rather than editing them.
