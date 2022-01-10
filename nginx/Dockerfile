ARG NGINX_VERSION=1.21.4
FROM nginx:${NGINX_VERSION}-alpine AS builder
# For latest build deps, see https://github.com/nginxinc/docker-nginx/blob/master/mainline/alpine/Dockerfile
RUN apk add --no-cache --virtual .build-deps \
    gcc \
    libc-dev \
    make \
    openssl-dev \
    pcre-dev \
    zlib-dev \
    linux-headers \
    libxslt-dev \
    gd-dev \
    geoip-dev \
    perl-dev \
    libedit-dev \
    bash \
    alpine-sdk \
    findutils \
    git

# Download sources
RUN wget "https://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz" -O nginx.tar.gz && \
git clone --recursive "https://github.com/google/ngx_brotli.git"

RUN mkdir /usr/src && \
    tar -zxC /usr/src -f nginx.tar.gz && \
    cd /usr/src/nginx-${NGINX_VERSION} && \
    ./configure --with-compat --add-dynamic-module=/ngx_brotli && \
    make modules && \
    mv ./objs/*.so /

FROM nginx:${NGINX_VERSION}-alpine

COPY --from=builder [ "/*.so","/usr/lib/nginx/modules/"]

# Updates and installs
RUN apk update \
    && apk upgrade \
    && apk --update add logrotate \
    && apk add --no-cache bash curl \
    && set -x ; \
       addgroup -g 82 -S www-data ; \
       adduser -u 82 -D -S -G www-data www-data && exit 0 ; exit 1

# Create 'messages' file used from 'logrotate'
RUN touch /var/log/messages

# Copy 'logrotate' config file
COPY logrotate/nginx /etc/logrotate.d/

# Remove some files in image
RUN rm /etc/nginx/conf.d/default.conf \
       /docker-entrypoint.d/30-tune-worker-processes.sh \
       /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh

# Copy configs and scripts
COPY [\
"./conf/nginx.conf"\
,"./conf/mime.types"\
,"/etc/nginx/"\
]

COPY ./scripts/startup.sh /opt/startup.sh

# Run scripts
RUN sed -i 's/\r//g' /opt/startup.sh
CMD ["/bin/bash", "/opt/startup.sh"]

# Recomendet ports
EXPOSE 80 443
