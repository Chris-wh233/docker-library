FROM lcr.loongnix.cn/library/alpine:3.24
ARG GIT_VER=
ENV VER=2.2.8
LABEL version=2.2.8${GIT_VER}
LABEL maintainer="Liangliang Zhai <zhailiangliang@loongson.cn>"

RUN wget -O /tmp/keepalived-2.2.8.tar.gz https://github.com/acassen/keepalived/archive/refs/tags/v2.2.8.tar.gz

RUN addgroup -S keepalived_script && adduser -D -S -G keepalived_script keepalived_script

RUN apk --no-cache add \
        binutils \
        gcc \
        libnl3 \
        libnl3-dev \
        make \
        musl-dev \
        openssl \
        openssl-dev \
        autoconf \
        automake \
    && tar -xzf /tmp/keepalived-2.2.8.tar.gz -C /tmp \
    && cd /tmp/keepalived-2.2.8 \
    && ./autogen.sh \
    && ./configure \
        --disable-dynamic-linking \
        --prefix=/usr \
        --exec-prefix=/usr \
        --bindir=/usr/bin \
        --sbindir=/usr/sbin \
        --sysconfdir=/etc \
        --datadir=/usr/share \
        --localstatedir=/var \
        --mandir=/usr/share/man \
        --enable-bfd \
        --disable-iptables \
    && make && make install \
    && strip /usr/sbin/keepalived \
    && cd / \
    && cp /tmp/keepalived-2.2.8/keepalived/etc/keepalived/keepalived.conf.sample /etc/keepalived/keepalived.conf \
    && rm -rf /tmp/keepalived-2.2.8.tar.gz /tmp/keepalived-2.2.8 \
    && apk --no-cache del \
        binutils \
        gcc \
        libnl3-dev \
        make \
        musl-dev \
        openssl-dev \
        autoconf \
        automake

ENTRYPOINT ["/usr/sbin/keepalived","--dont-fork","--log-console", "-f","/etc/keepalived/keepalived.conf"]
