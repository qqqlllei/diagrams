FROM busybox:latest
ENV LANG=C.UTF-8
RUN set -eux && mkdir -p /opt/skywalking/agent/
ADD apache-skywalking-apm-bin/agent/ /opt/skywalking/agent/
WORKDIR /