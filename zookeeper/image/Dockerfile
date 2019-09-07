FROM openjdk:8-jre-slim

ENV ZOO_USER=zookeeper \
    ZOO_CONF_DIR=/opt/zookeeper/conf \
    ZOO_DATA_DIR=/var/lib/zookeeper/data \
    ZOO_DATA_LOG_DIR=/var/lib/zookeeper/log \
    ZOO_LOG_DIR=/var/log/zookeeper \
    ZOO_TICK_TIME=2000 \
    ZOO_INIT_LIMIT=5 \
    ZOO_SYNC_LIMIT=2 \
    ZOO_AUTOPURGE_PURGEINTERVAL=0 \
    ZOO_AUTOPURGE_SNAPRETAINCOUNT=3 \
    ZOO_MAX_CLIENT_CNXNS=60 \
    ZOO_STANDALONE_ENABLED=true \
    ZOO_ADMINSERVER_ENABLED=true \
    ZOO_LOG_LEVEL=INFO \
    ZOO_CLIENT_PORT=2181 \
    ZOO_SERVER_PORT=2888 \
    ZOO_ELECTION_PORT=3888 \
    ZOO_HEAP_SIZE=2G \
    ZOO_MAX_CLIENT_CNXNS=60 \
    ZOO_SNAP_RETAIN_COUNT=3 \
    ZOO_PURGE_INTERVAL=0

ARG GPG_KEY=3F7A1D16FA4217B1DC75E1C9FFE35B7F15DFA1BA
ARG SHORT_DISTRO_NAME=zookeeper-3.5.5
ARG DISTRO_NAME=apache-zookeeper-3.5.5-bin

# Add a user with an explicit UID/GID and create necessary directories
RUN set -eux; \
    groupadd -r "$ZOO_USER" --gid=1000; \
    useradd -r -g "$ZOO_USER" --uid=1000 "$ZOO_USER"; \
    mkdir -p "$ZOO_DATA_LOG_DIR" "$ZOO_DATA_DIR" "$ZOO_LOG_DIR"; \
    chown "$ZOO_USER":"$ZOO_USER" "$ZOO_DATA_LOG_DIR" "$ZOO_DATA_DIR" "$ZOO_LOG_DIR"

# Install required packges
RUN set -eux; \
    apt-get update; \
    DEBIAN_FRONTEND=noninteractive \
    apt-get install -y --no-install-recommends ca-certificates dirmngr gosu gnupg netcat wget curl net-tools iputils-ping; \
    rm -rf /var/lib/apt/lists/*; \
# Verify that gosu binary works
    gosu nobody true

# Download Apache Zookeeper, verify its PGP signature, untar and clean up
RUN set -eux; \
    wget -q "https://www.apache.org/dist/zookeeper/$SHORT_DISTRO_NAME/$DISTRO_NAME.tar.gz"; \
    wget -q "https://www.apache.org/dist/zookeeper/$SHORT_DISTRO_NAME/$DISTRO_NAME.tar.gz.asc"; \
    export GNUPGHOME="$(mktemp -d)"; \
    gpg --keyserver ha.pool.sks-keyservers.net --recv-key "$GPG_KEY" || \
    gpg --keyserver pgp.mit.edu --recv-keys "$GPG_KEY" || \
    gpg --keyserver keyserver.pgp.com --recv-keys "$GPG_KEY"; \
    gpg --batch --verify "$DISTRO_NAME.tar.gz.asc" "$DISTRO_NAME.tar.gz"; \
    tar -zxf "$DISTRO_NAME.tar.gz"; \
    mv "$DISTRO_NAME" "/opt/zookeeper"; \
    rm -rf "$GNUPGHOME" "$DISTRO_NAME.tar.gz" "$DISTRO_NAME.tar.gz.asc"; \
    rm -f /opt/zookeeper/LICENSE.txt; \
    rm -f /opt/zookeeper/NOTICE.txt; \
    rm -f /opt/zookeeper/README.md; \
    rm -f /opt/zookeeper/README_packaging.txt; \
    rm -rf /opt/zookeeper/docs;

ADD docker-entrypoint.sh zkOk.sh zkMetrics.sh /opt/zookeeper/bin/
RUN set -eux; \
    mkdir -p $ZOO_DATA_DIR $ZOO_DATA_LOG_DIR $ZOO_LOG_DIR /usr/share/zookeeper /tmp/zookeeper; \
    chown -R "$ZOO_USER":"$ZOO_USER" $ZOO_DATA_DIR $ZOO_DATA_LOG_DIR $ZOO_LOG_DIR /opt/zookeeper /tmp/zookeeper; \
    chmod +x /opt/zookeeper/bin/*; \
    ln -s /opt/zookeeper/bin/* /usr/bin;\
    ln -s /opt/zookeeper/lib/* /usr/share/zookeeper 

WORKDIR /opt/zookeeper/bin
EXPOSE 2181 2888 3888 8080

ENV PATH=$PATH:/opt/zookeeper/bin ZOOCFGDIR=$ZOO_CONF_DIR

ENTRYPOINT ["/opt/zookeeper/bin/docker-entrypoint.sh"]
CMD ["/opt/zookeeper/bin/zkServer.sh", "start-foreground"]
