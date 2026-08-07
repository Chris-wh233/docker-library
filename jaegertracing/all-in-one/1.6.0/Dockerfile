FROM lcr.loongnix.cn/library/alpine:latest

RUN apk --no-cache add ca-certificates tzdata wget \
    && adduser -D -s /bin/sh jaeger \
    && mkdir -p /etc/jaeger \
    && wget -O /usr/bin/jaeger-all-in-one \
        https://cloud.loongnix.cn/releases/loongarch64/jaegertracing/jaeger-all-in-one/1.6.0/standalone-linux \
    && wget -O /etc/jaeger/sampling_strategies.json \
        https://cloud.loongnix.cn/releases/loongarch64/jaegertracing/jaeger-all-in-one/1.6.0/sampling_strategies.json \
    && chmod +x /usr/bin/jaeger-all-in-one

ENV SAMPLING_STRATEGIES_FILE=/etc/jaeger/sampling_strategies.json
EXPOSE 5775/udp 6831/udp 6832/udp 5778 16686 14268 9411
USER jaeger
CMD ["/usr/bin/jaeger-all-in-one"]
