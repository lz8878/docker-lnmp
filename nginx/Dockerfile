FROM nginx:1.23-alpine

COPY nginx.conf /etc/nginx/

RUN set -eux; \
    sed -i 's/dl-cdn.alpinelinux.org/mirrors.ustc.edu.cn/g' /etc/apk/repositories; \
    apk update; \
    apk add --no-cache \
        shadow \
    ;

# 创建 www-data 用户
ARG NGINX_GID=1000
ARG NGINX_UID=1000
RUN set -eux; \
    adduser -u ${NGINX_UID} -g ${NGINX_GID} -D -S -G www-data www-data

RUN set -eux; \
	[ ! -d /wwwdata/www ]; \
	mkdir -p /wwwdata/www; \
	chown www-data:www-data /wwwdata/www

RUN rm /etc/nginx/conf.d/default.conf
