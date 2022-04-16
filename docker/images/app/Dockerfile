FROM alpine:latest as base
LABEL builder=true

# Create a group and user
RUN adduser -S www-data -G www-data && \
    mkdir -p /var/www/html && \
    chown -R www-data:www-data /var/www/html 

RUN mkdir -p /var/www/html 

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
    mysql-client \
    nginx && \
    mkdir -p /var/cache/nginx && \
    chown -R www-data:www-data /var/cache/nginx && \
    chown -R www-data:www-data /var/lib/nginx
    #chown -R www-data:www-data /var/tmp/nginx && \
# Install PHP/FPM + Modules
RUN apk add \
    php8 \
    php8-apcu \
    php8-bcmath \
    php8-bz2 \
    php8-cgi \
    php8-ctype \
    php8-curl \
    php8-dom \
    php8-fileinfo \
    php8-fpm \
    php8-gd \
    php8-pdo_mysql \
    php8-iconv \
    php8-json \
    php8-mbstring \
    php8-mongodb \
    php8-opcache \
    php8-openssl \
    php8-pcntl \
    php8-pdo \
    php8-phar \
    php8-redis \
    php8-session \
    php8-soap \
    php8-simplexml \
    php8-tokenizer \
    php8-xml \
    php8-xmlwriter \
    php8-zip \
    php8-zlib

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
COPY php-fpm-www.conf /etc/php8/php-fpm.d/www.conf
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
RUN apk add php8-xdebug --repository http://dl-3.alpinelinux.org/alpine/edge/testing/