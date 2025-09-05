FROM php:8.2-fpm-alpine AS base

RUN apk add --no-cache \
    libpq-dev libzip-dev zip unzip curl \
    && docker-php-ext-install pdo pdo_pgsql zip

WORKDIR /var/www/html

FROM base AS prod
COPY --chown=www-data:www-data . /var/www/html
RUN php artisan config:cache \
    && php artisan route:cache \
    && php artisan view:cache
USER www-data
