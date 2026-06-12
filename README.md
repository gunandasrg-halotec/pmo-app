# Barumun PMO — Project Management App

Full-stack web application built with **Laravel 11** (REST API) + **React 18 + TypeScript** (SPA).

## Stack

- **Backend:** Laravel 11, Laravel Sanctum, MySQL
- **Frontend:** React 18, TypeScript, Vite, TanStack Query v5, Recharts

## Features

- Multi-project management with role-based access control (5 roles)
- WBD (Work Breakdown Dictionary) versioning with Direksi approval workflow
- Progress entry and cost transaction approval workflows
- Analytics: Dashboard KPI, S-Curve, Gantt Chart (read-only), Cost Analysis
- File repository with category management (documents & photos)
- Report generation per project

## Quick Start

### Backend

```bash
cd backend
composer install
cp .env.example .env
php artisan key:generate
# Edit .env with your DB credentials
php artisan migrate
php artisan db:seed
php artisan serve
```

### Frontend

```bash
cd frontend
npm install
npm run dev
```

Frontend runs on `http://localhost:5173`, proxies API to `http://localhost:8000`.

## Default Accounts

Password for all accounts: `password123`

| Email | Role |
|---|---|
| admin@company.com | Administrator Sistem |
| pm@company.com | Project Manager |
| direksi@company.com | Direksi |
| finance@company.com | Finance |
| adminproyek@company.com | Admin Proyek |
