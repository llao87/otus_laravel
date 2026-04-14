FROM php:8.1-fpm-alpine AS base

ENV NODE_VERSION=10.15.3

ARG WWWUSER
ARG WWWGROUP

RUN apk add --no-cache su-exec \
    bash \
    zip \
    unzip \
    git \
    curl \
    supervisor \
    tzdata \
    libpng-dev \
    libjpeg-turbo-dev \
    freetype-dev \
    postgresql-dev \
    rabbitmq-c-dev \
    linux-headers \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd pdo pdo_mysql pdo_pgsql opcache bcmath sockets pcntl

RUN apk add --no-cache nodejs \
    npm

RUN node -v && npm -v

RUN apk add --no-cache autoconf \
    g++ \
    make \
    libtool

RUN pecl install redis amqp msgpack \
    && docker-php-ext-enable redis amqp msgpack

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

# COPY .docker/php/php.ini /usr/local/etc/php/conf.d/custom.ini

# RUN mkdir -p /var/log/supervisor \
#     && touch /var/log/supervisord.log /var/log/supervisord.err \
#     && chown -R www-data:www-data /var/log/supervisor /var/log/supervisord.* \
#     && chmod -R 777 /var/log/supervisor /var/log/supervisord.*

# COPY .docker/php/entrypoint.sh /entrypoint.sh

RUN apk add --no-cache shadow \
    && groupmod -g ${WWWGROUP} www-data \
    && usermod -u ${WWWUSER} www-data \
    && usermod -aG www-data www-data

FROM base AS prod

WORKDIR /var/www/otus/

EXPOSE 80