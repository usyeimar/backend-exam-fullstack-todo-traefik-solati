# Fullstack Todo with Traefik

> Full-stack Todo application with Laravel 11 backend and Vue 3 frontend, orchestrated via Docker Compose behind Traefik as reverse proxy. Includes MinIO for attachments and Mailpit for email testing. Technical assessment for **Solati**.

<p>
  <img src="https://img.shields.io/badge/Laravel-FF2D20?logo=laravel&logoColor=white" />
  <img src="https://img.shields.io/badge/PHP-777BB4?logo=php&logoColor=white" />
  <img src="https://img.shields.io/badge/Vue.js-4FC08D?logo=vue.js&logoColor=white" />
  <img src="https://img.shields.io/badge/TypeScript-3178C6?logo=typescript&logoColor=white" />
  <img src="https://img.shields.io/badge/Tailwind-06B6D4?logo=tailwindcss&logoColor=white" />
  <img src="https://img.shields.io/badge/Traefik-24A1C1?logo=traefikproxy&logoColor=white" />
  <img src="https://img.shields.io/badge/PostgreSQL-4169E1?logo=postgresql&logoColor=white" />
  <img src="https://img.shields.io/badge/MinIO-C72E49?logo=minio&logoColor=white" />
  <img src="https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white" />
</p>

---

## Overview

A complete full-stack Todo platform: REST API in Laravel 11, SPA in Vue 3 + Vite, S3-compatible attachments via MinIO, and all services routed through Traefik on a single port so cross-site cookies and auth flows behave like production. The whole stack comes up with one `docker compose up`.

The point of using Traefik instead of mapping multiple ports is to expose every service under a single domain (e.g. `todo-app.localhost`, `todo-app.localhost/api`). Browsers treat this as one origin, which removes a whole class of cookie / CORS / auth quirks that appear when frontend and backend live on different ports.

## Features

### Backend
- 🔐 **OAuth2 authentication** with Laravel Passport
- ✅ **Tasks module** — CRUD, ownership, filtering via Spatie Query Builder
- 📎 **Attachments module** — file uploads stored in MinIO (S3-compatible)
- 👤 **User module** — profile management
- 📘 **OpenAPI / Stoplight Elements** documentation
- 🛣️ **Versioned API** under `/api/v1`

### Frontend
- ⚡ **Vue 3 + Vite + TypeScript**
- 🏪 **Pinia** for state management
- 🛣️ **Vue Router** with auth guards
- 📝 **VeeValidate + Yup** for form validation
- 🎨 **Tailwind CSS + Flowbite** UI components
- 🔔 **Toast notifications** via vue3-toastify
- 💾 **File download support** via file-saver
- 🧪 **Vitest** test runner

### Infrastructure
- 🌀 **Traefik** reverse proxy — single domain for all services
- 🐘 **PostgreSQL** + **Adminer** for DB management
- 📦 **MinIO** for S3-compatible object storage
- 📬 **Mailpit** for capturing outbound emails during dev

## Architecture

```
                       ┌─────────────────┐
   browser  ──HTTP──▶  │     Traefik     │  :80
                       └────────┬────────┘
                                │  routes by host/path
              ┌─────────────────┼─────────────────┐
              ▼                 ▼                 ▼
  todo-app.localhost   todo-app.localhost/api   todo-app.mail.localhost
       (Vue SPA)         (Laravel API)              (Mailpit)
                                │
                ┌───────────────┼─────────────────┐
                ▼               ▼                 ▼
            PostgreSQL        MinIO            Mailpit
            (Adminer UI)      (attachments)    (SMTP capture)
```

### Project layout

```
.
├── backend/              Laravel 11 API
│   ├── routes/
│   │   ├── auth/         Login, register, password reset
│   │   ├── tasks/        Tasks v1 routes
│   │   ├── attachments/  File upload routes
│   │   └── user/         Profile routes
│   └── ...
├── frontend/             Vue 3 + Vite + TypeScript SPA
├── traefik/              Reverse proxy configuration + ACME storage
├── nginx/                Static asset serving config
├── docs/                 Diagrams, walkthroughs, screenshots
└── docker-compose.yml    Full stack orchestration
```

## Quick start

> Requires Docker and Docker Compose. [Install guide](https://docs.docker.com/desktop/install/windows-install/).

```bash
# Clone
git clone https://github.com/usyeimar/backend-exam-fullstack-todo-traefik-solati.git
cd backend-exam-fullstack-todo-traefik-solati

# Install dependencies
cd backend && composer install && cd ..
cd frontend && pnpm install && cd ..

# Spin up everything
docker compose up -d
```

### Available services

| Service | URL |
| --- | --- |
| Frontend (Vue) | http://todo-app.localhost |
| API (Laravel) | http://todo-app.localhost/api |
| Mailpit (email inbox) | http://todo-app.mail.localhost |
| Adminer (Postgres UI) | configured in `docker-compose.yml` |
| MinIO console | configured in `docker-compose.yml` |
| Traefik dashboard | configured in `docker-compose.yml` |

### API documentation

- 📮 **Postman collection:** [Run in Postman](https://app.getpostman.com/run-collection/14969501-31fac7be-60ad-4188-a53d-2863a977eab5?action=collection%2Ffork&source=rip_markdown&collection-url=entityId%3D14969501-31fac7be-60ad-4188-a53d-2863a977eab5%26entityType%3Dcollection%26workspaceId%3D08af0b8c-1618-460b-a1d3-f902ca38ca53)
- 📘 **Online docs:** [Postman Documenter](https://documenter.getpostman.com/view/14969501/2sA3JDiks9)
- ✨ **Stoplight Elements** rendered from the OpenAPI spec is also available under the running API

### Tests

```bash
# Backend
cd backend && php artisan test

# Frontend
cd frontend && pnpm test:unit
```

## Key design decisions

- **Single-origin via Traefik** to avoid SameSite / CORS pitfalls between SPA and API.
- **MinIO instead of local disk** so attachment code matches what would run against S3 in production.
- **Versioned routes (`/api/v1`)** from day one — cheap insurance for breaking changes.
- **Spatie Query Builder** in the backend so list endpoints expose safe, declarative filtering/sorting without hand-rolled query strings.
- **Pinia** on the frontend for predictable auth + tasks state across components.

## What I'd improve next

- Real-time updates with Laravel Reverb + WebSocket subscriptions
- Background jobs for heavy attachment processing (image thumbs, virus scan)
- End-to-end tests with Playwright across the Traefik-served stack
- Production deploy recipe (separate domains, ACME via Let's Encrypt)

## Author

**Yeimar Yecid Lemus Romaña** — [GitHub](https://github.com/usyeimar) · [LinkedIn](https://linkedin.com/in/usyeimar)
