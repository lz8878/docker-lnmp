FROM php:8.2-cli-alpine

# 更换 apk 源
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories

# 安装依赖
RUN set -eux; \
    apk update; \
    apk add --no-cache \
        git \
        shadow \
        supervisor \
    ;

# 修改 www-data 用户的 GID 和 UID
ARG WWW_GID=1000
ARG WWW_UID=1000
RUN set -eux; \
    groupmod -g ${WWW_GID} www-data; \
    usermod -u ${WWW_UID} www-data

RUN set -eux; \
	[ ! -d /wwwdata/html ]; \
	mkdir -p /wwwdata/html; \
	chown www-data:www-data /wwwdata/html

# 安装 PHP 扩展
RUN set -eux; \
    apk update; \
    apk add --no-cache \
        freetype \
        libpng \
        libjpeg-turbo \
        libwebp \
        libzip \
        postgresql-libs \
    ; \
    apk add --no-cache --virtual .build-deps \
        $PHPIZE_DEPS \
        libpng-dev \
        libjpeg-turbo-dev \
        libwebp-dev \
        freetype-dev \
        libzip-dev \
        postgresql-dev \
    ; \
    docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp; \
    docker-php-ext-install -j$(nproc) \
        bcmath \
        gd \
        pdo_mysql \
        pdo_pgsql \
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

RUN set -eux; \
    mkdir -p /etc/supervisor/conf.d; \
    mkdir -p /var/log/supervisor

COPY supervisord.conf /etc/supervisor/
COPY --from=composer:2.5 /usr/bin/composer /usr/bin/composer

WORKDIR /etc/supervisor/conf.d

CMD [ "/usr/bin/supervisord", "-n", "-c", "/etc/supervisor/supervisord.conf" ]
