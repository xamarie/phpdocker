FROM php:7.4-fpm-alpine3.11

RUN apk add --no-cache --virtual .phpize-deps $PHPIZE_DEPS nano zip unzip wget \
    && apk add --no-cache libzip-dev pcre-dev postgresql-dev imagemagick-dev libmemcached-dev oniguruma-dev libxml2-dev freetype-dev libjpeg-turbo-dev libpng-dev icu-dev \
    && docker-php-ext-install exif pdo pdo_mysql mysqli pdo_pgsql pgsql bcmath ctype fileinfo json mbstring tokenizer opcache \
    && docker-php-ext-install zip intl xml dom xmlrpc xmlwriter sockets \
    && docker-php-ext-install -j$(nproc) iconv \
    && apk add --no-cache supervisor git openssh curl-dev \
    && docker-php-ext-install curl \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd \
    && rm -rf /tmp/* \
    && docker-php-source extract \
    && mkdir -p /usr/src/php/ext/igbinary \
    && curl -fsSL https://github.com/igbinary/igbinary/archive/3.2.0.tar.gz | tar xvz -C /usr/src/php/ext/igbinary --strip 1 \
    && docker-php-ext-install igbinary \
    && mkdir -p /usr/src/php/ext/redis \
    && curl -fsSL https://github.com/phpredis/phpredis/archive/5.3.0.tar.gz | tar xvz -C /usr/src/php/ext/redis --strip 1 \
    && docker-php-ext-configure redis --enable-redis-igbinary \
    && docker-php-ext-install redis \
    && mkdir -p /usr/src/php/ext/apcu \
    && curl -fsSL https://github.com/krakjoe/apcu/archive/v5.1.17.tar.gz | tar xvz -C /usr/src/php/ext/apcu --strip 1 \
    && docker-php-ext-install apcu \
    && mkdir -p /usr/src/php/ext/imagick \
    && curl -fsSL https://github.com/imagick/imagick/archive/3.5.0.tar.gz | tar xvz -C /usr/src/php/ext/imagick --strip 1 \
    && docker-php-ext-install imagick \
    && docker-php-source delete \
    && echo 'opcache.interned_strings_buffer=16' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini \
    && echo 'opcache.load_comments=Off' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini \
    && echo 'opcache.max_accelerated_files=16000' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini \
    && echo 'opcache.save_comments=Off' >> /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini

ENV COMPOSER_HOME /composer
ENV PATH ./vendor/bin:/composer/vendor/bin:$PATH
ENV COMPOSER_ALLOW_SUPERUSER 1
RUN curl -s https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin/ --filename=composer

COPY ./supervisord.conf /etc
RUN mkdir /var/log/supervisor /etc/supervisor /etc/supervisor/conf.d
EXPOSE 9001

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisord.conf"]
