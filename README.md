<div align="center">

# 🛍️ V2005 — E-Commerce Platform

**A modern, full-stack e-commerce monorepo built with Next.js & Laravel.**  
Customer storefront · Admin dashboard · REST API backend

[![Laravel](https://img.shields.io/badge/Laravel-13-FF2D20?style=for-the-badge&logo=laravel&logoColor=white)](https://laravel.com)
[![Next.js](https://img.shields.io/badge/Next.js-15-000000?style=for-the-badge&logo=next.js&logoColor=white)](https://nextjs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5-3178C6?style=for-the-badge&logo=typescript&logoColor=white)](https://www.typescriptlang.org)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-14+-336791?style=for-the-badge&logo=postgresql&logoColor=white)](https://www.postgresql.org)
[![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-3-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white)](https://tailwindcss.com)

</div>

---

## 📋 Table of Contents

- [Architecture](#-architecture)
- [Project Structure](#-project-structure)
- [Tech Stack](#-tech-stack)
- [Prerequisites](#-prerequisites)
- [Getting Started](#-getting-started)
- [Environment Variables](#-environment-variables)
- [Deployment](#-deployment)
- [Design System](#-design-system)
- [Development Roadmap](#-development-roadmap)
- [License](#-license)

---

## 🏗️ Architecture

```
┌─────────────────────────┐     ┌──────────────────────────┐
│   user-frontend          │     │   admin-frontend          │
│   Next.js  :3000         │     │   Next.js  :3001          │
└──────────┬──────────────┘     └────────────┬─────────────┘
           │  REST /api/v1                   │  REST /api/v1
           └──────────────┬──────────────────┘
                          ▼
              ┌───────────────────────┐
              │  backend (Laravel)    │
              │  :8000                │
              └──────────┬────────────┘
                         ▼
              ┌───────────────────────┐
              │  PostgreSQL           │
              │  v2005_data           │
              └───────────────────────┘
```

---

## 📁 Project Structure

```
V2005/
├── user-frontend/      # Public customer storefront (Next.js)
├── admin-frontend/     # Admin dashboard (Next.js)
└── backend/            # Laravel REST API
    ├── Dockerfile      # FrankenPHP production image
    ├── railway.toml    # Railway deployment config
    └── docker/
        ├── entrypoint.sh   # Migrate + serve on $PORT
        └── Caddyfile       # Laravel front controller
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **User Frontend** | Next.js · React · TypeScript · Tailwind CSS · Zustand · React Hook Form · Zod · Lucide |
| **Admin Frontend** | Next.js · React · TypeScript · Tailwind CSS · Zustand · React Hook Form · Zod · Lucide |
| **Backend API** | Laravel 13 · Sanctum · PostgreSQL |
| **Media Storage** | Cloudinary (product images) |
| **Production DB** | Neon (serverless PostgreSQL) |
| **Deployment** | Railway (backend) · Vercel (frontends) |

---

## ✅ Prerequisites

Ensure the following are installed before getting started:

| Requirement | Version |
|---|---|
| Node.js | 20+ |
| PHP | 8.4+ |
| Composer | 2+ |
| PostgreSQL | 14+ |

> **PHP Extensions required:** `pdo_pgsql`, `pgsql`

---

## 🚀 Getting Started

### 1. Clone the Repository

```bash
git clone https://github.com/kalapak-team/V2005.git
cd v2005
```

### 2. Create the Database

Open `psql` or pgAdmin and run:

```sql
CREATE DATABASE v2005_data;
```

### 3. Backend Setup

```bash
cd backend
copy .env.example .env
# Edit .env — set DB_USERNAME and DB_PASSWORD to match your PostgreSQL credentials
php artisan key:generate
php artisan migrate
php artisan serve
```

✅ API health check: [http://localhost:8000/api/v1/health](http://localhost:8000/api/v1/health)

### 4. User Frontend

```bash
cd user-frontend
copy .env.example .env.local
npm install
npm run dev
```

✅ App: [http://localhost:3000](http://localhost:3000)

### 5. Admin Frontend

```bash
cd admin-frontend
copy .env.example .env.local
npm install
npm run dev
```

✅ App: [http://localhost:3001](http://localhost:3001)

### 6. Root Helper Scripts

Run all services from the repository root:

```bash
npm run dev:user    # Start user storefront
npm run dev:admin   # Start admin dashboard
npm run dev:api     # Start Laravel backend
```

---

## 🔐 Environment Variables

> ⚠️ **Never commit real secrets to Git.** Use `.env.example` files as templates only.

### Frontend (user-frontend & admin-frontend)

```env
NEXT_PUBLIC_API_URL=http://localhost:8000/api/v1
```

### Backend — Local (`.env`)

```env
DB_CONNECTION=pgsql
DB_HOST=127.0.0.1
DB_PORT=5432
DB_DATABASE=v2005_data
DB_USERNAME=postgres
DB_PASSWORD=your_password
DB_SSLMODE=prefer

CLOUDINARY_CLOUD_NAME=v2005
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
CLOUDINARY_FOLDER=v2005/products
```

### Backend — Production / Neon (`.env.production`)

```env
DB_CONNECTION=pgsql
DB_URL=postgresql://neondb_owner:PASSWORD@HOST/neondb?sslmode=require
DB_SSLMODE=require

CLOUDINARY_CLOUD_NAME=v2005
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
CLOUDINARY_FOLDER=v2005/products
```

After the first deploy, run: `php artisan migrate --force`

---

## ☁️ Deployment

### Backend → Railway (Docker)

The `backend/` directory is fully configured for Railway deployment via Docker.

| File | Purpose |
|---|---|
| `Dockerfile` | FrankenPHP (PHP 8.4) concurrent server image |
| `railway.toml` | Health check endpoint `/up` |
| `docker/entrypoint.sh` | Runs `migrate --force` then serves on `$PORT` |
| `docker/Caddyfile` | Laravel public front controller |

**Step-by-step deployment:**

1. Push this repository to GitHub.
2. Go to [Railway](https://railway.app) → **New Project** → **Deploy from GitHub** → select `v2005`.
3. Under Service settings, set **Root Directory** to `backend` (Railway auto-detects the `Dockerfile`).
4. Add a public domain under **Settings → Networking**.
5. Set the following environment variables in the **Variables** tab:

```env
APP_NAME=V2005
APP_ENV=production
APP_DEBUG=false
APP_KEY=base64:GENERATE_WITH_php_artisan_key_generate_--show
APP_URL=https://YOUR-SERVICE.up.railway.app
LOG_CHANNEL=stderr

DB_CONNECTION=pgsql
DB_URL=postgresql://neondb_owner:PASSWORD@ep-...neon.tech/neondb?sslmode=require
DB_HOST=ep-your-host.ap-southeast-1.aws.neon.tech
DB_PORT=5432
DB_DATABASE=neondb
DB_USERNAME=neondb_owner
DB_PASSWORD=YOUR_NEON_PASSWORD
DB_SSLMODE=require
DB_PERSISTENT=true
DB_CONNECT_TIMEOUT=10

CLOUDINARY_CLOUD_NAME=v2005
CLOUDINARY_API_KEY=your_api_key
CLOUDINARY_API_SECRET=your_api_secret
CLOUDINARY_FOLDER=v2005/products

FRONTEND_USER_URL=https://your-storefront.vercel.app
FRONTEND_ADMIN_URL=https://your-admin.vercel.app
SANCTUM_STATEFUL_DOMAINS=your-storefront.vercel.app,your-admin.vercel.app

SESSION_DRIVER=file
CACHE_STORE=file
QUEUE_CONNECTION=sync
```

> **💡 Tip:** Choose the Railway region closest to your Neon region to minimize latency. This project uses `ap-southeast-1` (Singapore). Cross-region connections can add ~2s per database call on cold queries.

6. **Redeploy.** The entrypoint will run `php artisan migrate --force` automatically, then start FrankenPHP.
7. Verify health endpoints:
   - `https://YOUR-SERVICE.up.railway.app/up`
   - `https://YOUR-SERVICE.up.railway.app/api/v1/health`

**Generate `APP_KEY` locally:**

```bash
cd backend && php artisan key:generate --show
```

---

## 🎨 Design System

V2005 uses a consistent design token system with full **light / dark mode** support.

| Token | Light | Dark |
|---|---|---|
| Background | `#F8FAFC` | `#0F172A` |
| Card | `#FFFFFF` | `#1E293B` |
| Text | `#0F172A` | `#F8FAFC` |
| Primary | `#2563EB` | `#60A5FA` |
| Accent (sale) | `#F97316` | `#F97316` |
| Navy | `#0F172A` | — |

---

## 🗺️ Development Roadmap

| Phase | Description | Status |
|---|---|---|
| **Phase 1** | Project initialization & monorepo scaffold | ✅ Complete |
| **Phase 2** | Database & Laravel foundation | 🔲 Planned |
| **Phase 3** | Authentication (Sanctum) | 🔲 Planned |
| **Phase 4** | Product system | 🔲 Planned |
| **Phase 5** | User frontend — catalog & cart | 🔲 Planned |
| **Phase 6** | Checkout & orders | 🔲 Planned |
| **Phase 7** | Admin frontend | 🔲 Planned |
| **Phase 8** | Advanced features | 🔲 Planned |
| **Phase 9** | UI/UX polish | 🔲 Planned |
| **Phase 10** | Testing & deployment | 🔲 Planned |

**Phase 1 Completed:**
- ✅ Monorepo scaffold (`user-frontend`, `admin-frontend`, `backend`)
- ✅ Tailwind design tokens (light/dark)
- ✅ Environment variable templates
- ✅ Laravel Sanctum installed
- ✅ Versioned API (`/api/v1/health`)
- ✅ CORS configured for both frontends
- ✅ PostgreSQL connection targeting `v2005_data`

---

## 📄 License

This is a **private project** — all rights reserved.  
© 2026 V2005. Unauthorized use, reproduction, or distribution is prohibited.
