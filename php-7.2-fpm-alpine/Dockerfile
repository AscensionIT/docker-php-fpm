FROM php:7.2-fpm-alpine

MAINTAINER Janaka Wickramasinghe <janaka@ascesnionit.com.au>

RUN apk update && apk upgrade

RUN set -eux \
    && apk add --no-cache --virtual .build-deps libmcrypt-dev libjpeg-turbo-dev \ 
      freetype-dev libpng-dev openldap-dev mariadb-dev libxml2-dev \
      libzip-dev gettext-dev libxml2-dev libxslt-dev\
    \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ \
          --with-jpeg-dir=/usr/include/ \
    \
    && pecl install redis-4.2.0 \
    \
    && pecl install mcrypt-1.0.1 \
    \
    && pecl install apcu \
    \
    && pecl install igbinary \
    \
    && docker-php-ext-install -j$(getconf _NPROCESSORS_ONLN) gd ldap \
          opcache mysqli pdo_mysql zip soap bcmath sockets \
          calendar exif gettext pcntl shmop sysvmsg sysvsem \
          sysvshm wddx xsl \
    \
    && docker-php-ext-enable redis mcrypt apcu igbinary calendar exif \
          gettext pcntl shmop sysvmsg sysvsem sysvshm wddx xsl \
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

RUN rm -rf /var/cache/apk/*
