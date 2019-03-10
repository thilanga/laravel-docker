FROM alpine:edge as base
LABEL builder=true

# Create user
RUN adduser -D -u 1000 -g 1000 -s /bin/sh www-data && \
    mkdir -p /var/www/html && \
    chown -R www-data:www-data /var/www/html

# setup .composer folder and set permissions
RUN  mkdir -p /var/www/.composer && \
  chown -R www-data:www-data /var/www/.composer

# Install a golang port of supervisord
COPY --from=ochinchina/supervisord:latest /usr/local/bin/supervisord /usr/bin/supervisord

# latest composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Install nginx & gettext (envsubst)
# Create cachedir and fix permissions
RUN apk add --no-cache --update \
# Install tini - 'cause zombies - see: https://github.com/ochinchina/supervisord/issues/60
# (also pkill hack)
    tini \
    gettext \
    git \
    dcron \
    nginx && \
    mkdir -p /var/cache/nginx && \
    chown -R www-data:www-data /var/cache/nginx && \
    chown -R www-data:www-data /var/lib/nginx && \
    chown -R www-data:www-data /var/tmp/nginx && \
# Install PHP/FPM + Modules
apk add \
    php7 \
    php7-apcu \
    php7-bcmath \
    php7-bz2 \
    php7-cgi \
    php7-ctype \
    php7-curl \
    php7-dom \
    php7-fileinfo \
    php7-fpm \
    php7-gd \
    php7-iconv \
    php7-json \
    php7-mbstring \
    php7-mongodb \
    php7-oauth \
    php7-opcache \
    php7-openssl \
    php7-pcntl \
    php7-pdo \
    php7-phar \
    php7-redis \
    php7-session \
    php7-soap \
    php7-simplexml \
    php7-tokenizer \
    php7-xml \
    php7-xmlwriter \
    php7-zip \
    php7-zlib

# Set up cron
COPY crontab /var/spool/cron/crontabs/www-data
RUN /usr/bin/crontab /var/spool/cron/crontabs/www-data

# Runtime env vars are envstub'd into config during entrypoint
ENV SERVER_NAME="localhost"
ENV SERVER_ALIAS=""
ENV SERVER_ROOT=/var/www/html

# Alias defaults to empty, example usage:
# SERVER_ALIAS='www.example.com'

COPY supervisord.conf /supervisord.conf
COPY php-fpm-www.conf /etc/php7/php-fpm.d/www.conf
COPY nginx.conf.template /nginx.conf.template
COPY ./docker-entrypoint.sh /docker-entrypoint.sh

# Nginx on :80
EXPOSE 80
WORKDIR /var/www/html
ENTRYPOINT ["tini", "--", "/docker-entrypoint.sh"]

# ---- Dev ----
FROM base AS dev
LABEL builder=true

# install xdebug
RUN apk add php7-xdebug --repository http://dl-3.alpinelinux.org/alpine/edge/testing/