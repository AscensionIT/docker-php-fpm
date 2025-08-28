FROM php:8.3-fpm-alpine

MAINTAINER Janaka Wickramasinghe <janaka@ascesnionit.com.au>

RUN set -eux \
    && apk add --no-cache --virtual .build-deps libjpeg-turbo-dev \ 
      freetype-dev libpng-dev openldap-dev mariadb-dev libxml2-dev \
      libzip-dev linux-headers \
    \
    && docker-php-ext-configure gd --with-freetype \
          --with-jpeg \
    \
    && pecl install redis-6.2.0 \
    \
    && docker-php-ext-install -j$(getconf _NPROCESSORS_ONLN) gd ldap \
          mysqli pdo_mysql zip soap bcmath sockets\
    \
    && docker-php-ext-enable redis\
    \
    && runDeps="$( \
      scanelf --needed --nobanner --recursive /usr/local \
        | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
        | sort -u \
        | xargs -r apk info --installed \
        | sort -u \
    )" \
    \
    && apk add --virtual .php-rundeps $runDeps \
    \
    && apk del .build-deps
