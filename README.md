# Laravel Docker Template 🚀

A production-ready Dockerized template for **Laravel + JavaScript frameworks** (Vue, React, Konva.js, etc).  
This repo makes it easy to spin up new projects without repeating the boilerplate every time.

---

## ✨ Features

- **Dev / Staging / Prod / Test** Docker Compose stacks  
- **Postgres** as default DB (swappable if needed)  
- **Redis** for cache & sessions  
- **Unprivileged Nginx** container with Gzip + HTTPS support  
- **Certbot** container for TLS certificates (manual or automated renewal)  
- **Dev Tools Sidecar** → run `artisan`, `composer`, `npm`, `vite` inside a container  
- **Frontend**: ships with JS by default, optional Vue & React demos included  
- **Storage volumes** mounted securely (not root owned)  
- **Health checks** for PHP, DB, Redis, Nginx  
- **Docs** and clean `.gitignore` / `.dockerignore`  

---

## 📂 Stack Overview

| Stack     | File                 | Notes |
|-----------|----------------------|-------|
| Dev       | `compose.dev.yml`    | Hot reload, dev-tools, npm/vite inside container |
| Staging   | `compose.staging.yml`| HTTP + HTTPS, good for testing certs |
| Prod      | `compose.prod.yml`   | HTTPS only, slim builds, certbot available |
| Test      | `compose.test.yml`   | Spins up DB + PHP test runner for CI/local |

---

## ⚡ Getting Started

### 1. Clone & Init
```bash
git clone https://github.com/Jinne-X/laravel-docker-template.git
cd laravel-docker-template
cp .env.example .env
```

### 2. Start Dev Stack
```bash
docker compose -f compose.dev.yml up --build -d
```

Visit: [http://localhost](http://localhost)

### 3. Install Laravel
```bash
docker compose -f compose.dev.yml exec dev-tools composer install
docker compose -f compose.dev.yml exec dev-tools php artisan key:generate
```

---

## 🛠️ Development Workflow

- Write code on your **host editor** (e.g. VSCode)  
- Containers auto-reload changes via bind mounts  
- Run commands inside `dev-tools` container:

```bash
# Laravel
docker compose -f compose.dev.yml exec dev-tools php artisan migrate
docker compose -f compose.dev.yml exec dev-tools php artisan tinker

# Node / JS
docker compose -f compose.dev.yml exec dev-tools npm install
docker compose -f compose.dev.yml exec dev-tools npm run dev
```

- Logs:
```bash
docker compose -f compose.dev.yml logs -f
```

---

## 🌐 Staging & Production

### Staging
- Defined in `compose.staging.yml`  
- Runs both HTTP & HTTPS  
- Good for testing certs before production  

```bash
docker compose -f compose.staging.yml up --build -d
```

### Production
- Defined in `compose.prod.yml`  
- HTTPS **only**  
- Slim images (no dev dependencies, npm, artisan, vite, etc)  

```bash
docker compose -f compose.prod.yml up --build -d
```

---

## 🔒 HTTPS with Certbot

Certificates are handled by a **separate Certbot container**.  

### One-Time Issue
```bash
docker compose -f compose.prod.yml run --rm certbot certonly --webroot -w /var/www/certbot -d example.com -d www.example.com
```

### Renewal (manual trigger)
```bash
docker compose -f compose.prod.yml run --rm certbot renew
```

You can also schedule renewal with:
- **Host cron** (call `docker compose run certbot renew`)  
- Or a **certbot sidecar container** with `cron` inside  

---

## 📦 Storage

- Laravel storage is mapped to a named volume (`laravel_storage`)  
- Volume is mounted inside PHP & Nginx containers  
- Ownership set to **non-root user** for security  

---

## ❤️ Health Checks

Health checks are defined in each Compose stack for:
- PHP-FPM  
- Postgres  
- Redis  
- Nginx  

Docker will auto-restart containers if unhealthy.  

---

## 🧪 Testing

Use the `compose.test.yml` stack for local testing or CI.  

```bash
docker compose -f compose.test.yml up --build -d
docker compose -f compose.test.yml exec php php artisan migrate --env=testing
docker compose -f compose.test.yml exec php php artisan test
```

---

## 🎨 Frontend

This template ships with **vanilla JS by default**.  
Optional demos included for:

- Vue.js  
- React.js  
- Konva.js (canvas/shape framework)  

Enable them as needed in your project.  

---

## 📖 Documentation

See `docs/` for:  
- Certbot playbook  
- Deployment notes  
- Example environment variables  

---

## ✅ Notes

- No CI/CD pipeline included in this template (add per-project)  
- Queue worker not included by default (add if needed per-project)  
- Template is designed to be **copied** for new projects  
