FROM php:8.1-fpm-alpine

COPY php.ini "$PHP_INI_DIR/"
COPY laravel.ini "$PHP_INI_DIR/conf.d/"
COPY xlaravel.pool.conf /usr/local/etc/php-fpm.d/

# 更换 apk 源
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories

# 安装依赖
RUN set -eux; \
    apk update; \
    apk add --no-cache \
        git \
        shadow \
    ;

# 修改 www-data 用户的 GID 和 UID
ARG PHP_FPM_GID=1000
ARG PHP_FPM_UID=1000
RUN set -eux; \
    groupmod -g ${PHP_FPM_GID} www-data; \
    usermod -u ${PHP_FPM_UID} www-data

RUN set -eux; \
	[ ! -d /wwwdata/www ]; \
	mkdir -p /wwwdata/www; \
	chown www-data:www-data /wwwdata/www

# 安装 PHP 扩展
RUN set -eux; \
    apk update; \
    apk add --no-cache \
        freetype \
        libpng \
        libjpeg-turbo \
        libwebp \
        libzip \
    ; \
    apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
        libpng-dev \
        libjpeg-turbo-dev \
        libwebp-dev \
        freetype-dev \
        libzip-dev \
    ; \
    docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp; \
    docker-php-ext-install -j$(nproc) \
        bcmath \
        gd \
        pdo_mysql \
        zip \
    ; \
    pecl install \
        redis-5.3.4 \
    ; \
    docker-php-ext-enable \
        redis \
    ; \
    apk del --no-network .build-deps; \
    rm -rf /usr/src/*

COPY --from=composer:2.3 /usr/bin/composer /usr/bin/composer

WORKDIR /wwwdata/www
