FROM alpine

ARG SS_VER=3.2.1
ARG SS_OBFS_VER=0.0.5

RUN set -ex \
    && apk add --no-cache --virtual .build-deps \
        autoconf \
        automake \
        build-base \
        c-ares-dev \
        git \
        libev-dev \
        libtool \
        libsodium-dev \
        linux-headers \
        mbedtls-dev \
        pcre-dev \
    # Build & install
    && cd /tmp/ \
    && git clone https://github.com/shadowsocks/shadowsocks-libev.git \
    && cd shadowsocks-libev \
    && git checkout v$SS_VER \
    && git submodule update --init --recursive \
    && ./autogen.sh \
    && ./configure --prefix=/usr --disable-documentation \
    && make install \
    && cd /tmp/ \
    && git clone https://github.com/shadowsocks/simple-obfs.git shadowsocks-obfs \
    && cd shadowsocks-obfs \
    && git checkout v$SS_OBFS_VER \
    && git submodule update --init --recursive \
    && ./autogen.sh \
    && ./configure --prefix=/usr --disable-documentation \
    && make install \
    && cd .. \
    && runDeps="$( \
        scanelf --needed --nobanner /usr/bin/ss-* \
          | awk '{ gsub(/,/, "\nso:", $2); print "so:" $2 }' \
          | xargs -r apk info --installed \
          | sort -u \
        )" \
    && apk add --no-cache --virtual .run-deps rng-tools $runDeps \
    && apk del .build-deps \
    && rm -rf /tmp/* /var/cache/apk/* /var/lib/apt/lists/*

CMD ["ss-server", "-c", "/etc/shadowsocks-libev/config.json"]
