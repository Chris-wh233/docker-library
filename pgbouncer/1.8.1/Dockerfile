FROM lcr.loongnix.cn/openeuler/openeuler:24.03-LTS-SP4 AS build_stage

LABEL maintainer "doupengda@loongson.cn"

# libc-dev libressl-dev libressl file
# hadolint ignore=DL3018
RUN dnf install -y \
        autoconf \
        automake \
        c-ares \
        c-ares-devel \
        curl \
        gcc \
        libevent \
        libevent-devel \
        libtool \
        openssl-devel \
        make \
        pkgconf

ARG PGBOUNCER_VERSION="1.8.1"

RUN curl -Lso  "/tmp/pgbouncer.tar.gz" "https://pgbouncer.github.io/downloads/files/${PGBOUNCER_VERSION}/pgbouncer-${PGBOUNCER_VERSION}.tar.gz" && \
        file "/tmp/pgbouncer.tar.gz"

WORKDIR /tmp

RUN mkdir /tmp/pgbouncer && \
        tar -zxvf pgbouncer.tar.gz -C /tmp/pgbouncer --strip-components 1

WORKDIR /tmp/pgbouncer

RUN  ./autogen.sh && \
        ./configure --prefix=/usr && \
        make

FROM lcr.loongnix.cn/openeuler/openeuler:24.03-LTS-SP4

# hadolint ignore=DL3018
RUN dnf install -y \
        libevent \
        ca-certificates \
        coreutils \
        shadow \
        c-ares

RUN groupadd -r postgres && useradd -r -g postgres postgres

WORKDIR /etc/pgbouncer
WORKDIR /var/log/pgbouncer

RUN chown -R postgres:root \
        /etc/pgbouncer \
        /var/log/pgbouncer

USER postgres

COPY --from=build_stage --chown=postgres ["/tmp/pgbouncer", "/opt/pgbouncer"]
COPY --chown=postgres ["entrypoint.sh", "/opt/pgbouncer"]

WORKDIR /opt/pgbouncer
ENTRYPOINT ["/opt/pgbouncer/entrypoint.sh"]
