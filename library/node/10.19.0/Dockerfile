FROM lcr.loongnix.cn/openeuler/openeuler:24.03-LTS-SP4

RUN groupadd --gid 1000 node \
  && useradd --uid 1000 --gid node --shell /bin/bash --create-home node

ENV NODE_VERSION 10.19.0

RUN ARCH= && MACHINE="$(uname -m)" \
    && case "$MACHINE" in \
      amd64) ARCH='x64';; \
      ppc64el) ARCH='ppc64le';; \
      s390x) ARCH='s390x';; \
      arm64) ARCH='arm64';; \
      armhf) ARCH='armv7l';; \
      i386) ARCH='x86';; \
      loongarch64) ARCH='loong64';; \
      *) echo "unsupported architecture"; exit 1 ;; \
    esac \
    && set -ex \
    # libatomic1 for arm dirmngr
    && dnf install -y ca-certificates curl wget gnupg2 xz libatomic \
    && \
    if [ "${ARCH}" = "loong64" ]; then \
      curl -fsSLO --compressed "https://cloud.loongnix.cn/releases/loongarch64/node/v$NODE_VERSION/node-v$NODE_VERSION-linux-$ARCH.tar.xz"; \
    else \
      curl -fsSLO --compressed "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-$ARCH.tar.xz"; \
      curl -fsSLO --compressed "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc"; \
      gpg --batch --decrypt --output SHASUMS256.txt SHASUMS256.txt.asc; \
      grep " node-v$NODE_VERSION-linux-$ARCH.tar.xz\$" SHASUMS256.txt | sha256sum -c -; \
      rm SHASUMS256.txt.asc SHASUMS256.txt; \
    fi \
    && tar -xJf "node-v$NODE_VERSION-linux-$ARCH.tar.xz" -C /usr/local --strip-components=1 --no-same-owner \
    && rm "node-v$NODE_VERSION-linux-$ARCH.tar.xz" \
    && dnf clean all \
    && ln -s /usr/local/bin/node /usr/local/bin/nodejs \
    && node --version \
    && npm --version


ENV YARN_VERSION 1.22.19


RUN set -ex \
  && dnf install -y --setopt=install_weak_deps=False ca-certificates curl wget \
  && curl -fsSLO --compressed "https://yarnpkg.com/downloads/$YARN_VERSION/yarn-v$YARN_VERSION.tar.gz" \
  && mkdir -p /opt \
  && tar -xzf yarn-v$YARN_VERSION.tar.gz -C /opt/ \
  && ln -s /opt/yarn-v$YARN_VERSION/bin/yarn /usr/local/bin/yarn \
  && ln -s /opt/yarn-v$YARN_VERSION/bin/yarnpkg /usr/local/bin/yarnpkg \
  && rm yarn-v$YARN_VERSION.tar.gz \
  && dnf clean all \
  && yarn --version

COPY docker-entrypoint.sh /usr/local/bin/
ENTRYPOINT ["docker-entrypoint.sh"]

CMD [ "node" ]
