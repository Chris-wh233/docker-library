FROM lcr.loongnix.cn/openeuler/openeuler:24.03-LTS-SP4 AS builder

Run dnf install curl && \
    curl -o /tmp/elasticsearch-7.8.1-SNAPSHOT-no-jdk-linux-loongarch64.tar.gz \ 
    https://cloud.loongnix.cn/releases/loongarch64/elastic/elasticsearch/7.8.1/elasticsearch-7.8.1-SNAPSHOT-no-jdk-linux-loongarch64.tar.gz

RUN mkdir -p /opt/elasticsearch && \
    tar -xzf /tmp/elasticsearch-7.8.1-SNAPSHOT-no-jdk-linux-loongarch64.tar.gz -C /opt/elasticsearch --strip-components=1 && \
    rm -f /tmp/*.tar.gz

FROM lcr.loongnix.cn/openeuler/openeuler:24.03-LTS-SP4

RUN dnf install -y java-11-openjdk-headless --setopt=install_weak_deps=False && \
    dnf clean all

RUN groupadd -g 1000 elasticsearch && \
    useradd -u 1000 -g 1000 -m elasticsearch

COPY --from=builder --chown=elasticsearch:elasticsearch /opt/elasticsearch /usr/share/elasticsearch

RUN JDK_DIR=$(ls -d /usr/lib/jvm/java-11-openjdk-* 2>/dev/null | head -1) && \
    if [ -z "$JDK_DIR" ]; then \
        echo "JDK not found!" && exit 1; \
    else \
        echo "Found JDK at $JDK_DIR" && \
        ln -s "$JDK_DIR" /usr/share/elasticsearch/jdk; \
    fi

RUN chmod +x /usr/share/elasticsearch/modules/x-pack-ml/platform/linux-loongarch64/bin/* && \
    mkdir -p /var/lib/elasticsearch /var/log/elasticsearch && \
    chown -R elasticsearch:elasticsearch /var/lib/elasticsearch /var/log/elasticsearch && \
    echo "path.data: /var/lib/elasticsearch" >> /usr/share/elasticsearch/config/elasticsearch.yml && \
    echo "path.logs: /var/log/elasticsearch" >> /usr/share/elasticsearch/config/elasticsearch.yml

ENV ES_HOME=/usr/share/elasticsearch
ENV PATH=$ES_HOME/bin:$PATH

EXPOSE 9200 9300

COPY docker-entrypoint.sh /usr/local/bin/
RUN chmod +x /usr/local/bin/docker-entrypoint.sh
ENTRYPOINT ["docker-entrypoint.sh"]

USER elasticsearch
WORKDIR /usr/share/elasticsearch

CMD ["elasticsearch"]
