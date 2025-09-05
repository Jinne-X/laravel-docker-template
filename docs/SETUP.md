# Setup Guide

This template provides a Laravel app with Dockerized services for dev, staging, and production.

## First Run Checklist
1. Copy env file: `cp .env.example .env`
2. Build containers: `docker compose -f compose.dev.yml up --build`
3. Run Laravel install commands:
   docker compose -f compose.dev.yml exec dev-tools composer install
   docker compose -f compose.dev.yml exec dev-tools php artisan key:generate
4. Access app at http://localhost

## Certbot
For staging/prod, issue certificates:
docker compose -f compose.staging.yml run --rm certbot certonly --webroot -w /var/www/html -d ${DOMAIN}

Renewal:
- Manual (run above again when needed).
- Automated (add cron/GitHub Action, see docs).

## Healthcheck
Laravel exposes `/health-check` which returns 200 OK.
