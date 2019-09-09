FROM openjdk:8-jre-slim

ENV KAFKA_USER=kafka \
    KAFKA_DATA_DIR=/var/lib/kafka/data \
    KAFKA_HOME=/opt/kafka \
    PATH=$PATH:/opt/kafka/bin

ARG KAFKA_VERSION=2.3.0
ARG KAFKA_DIST=kafka_2.12-2.3.0

# Add a user with an explicit UID/GID and create necessary directories
RUN set -eux; \
    groupadd -r "$KAFKA_USER" --gid=1000; \
    useradd -r -g "$KAFKA_USER" --uid=1000 "$KAFKA_USER";

# Install required packges
RUN set -eux; \
    apt-get update; \
    DEBIAN_FRONTEND=noninteractive \
    apt-get install -y --no-install-recommends ca-certificates dirmngr gosu gnupg netcat wget curl net-tools iputils-ping; \
    rm -rf /var/lib/apt/lists/*; 

# Download Apache Kafka, verify its PGP signature, untar and clean up
RUN set -eux; \
    wget -q "http://www.apache.org/dist/kafka/$KAFKA_VERSION/$KAFKA_DIST.tgz"; \
    wget -q "http://www.apache.org/dist/kafka/$KAFKA_VERSION/$KAFKA_DIST.tgz.asc"; \
    wget -q "http://kafka.apache.org/KEYS"; \
    export GNUPGHOME="$(mktemp -d)"; \
    gpg --import KEYS; \
    gpg --batch --verify "$KAFKA_DIST.tgz.asc" "$KAFKA_DIST.tgz"; \
    tar -xzf "$KAFKA_DIST.tgz" -C /opt; \
    rm -r "$GNUPGHOME" "$KAFKA_DIST.tgz" "$KAFKA_DIST.tgz.asc";

COPY log4j.properties /opt/$KAFKA_DIST/config/

RUN set -eux; \
    mv "/opt/$KAFKA_DIST" "$KAFKA_HOME"; \
    mkdir -p $KAFKA_DATA_DIR; \
    chown -R "$KAFKA_USER:$KAFKA_USER"  "$KAFKA_HOME"; \
    chown -R "$KAFKA_USER:$KAFKA_USER"  "$KAFKA_DATA_DIR"
