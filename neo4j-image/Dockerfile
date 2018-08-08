FROM openjdk:8-jre-alpine

RUN addgroup -S neo4j && adduser -S -H -h /var/lib/neo4j -G neo4j neo4j

ENV NEO4J_SHA256=af53823776645e11d04436a513368e7e417b515572d6228da6b2977c8490ffbb \
    NEO4J_TARBALL=neo4j-community-3.4.5-unix.tar.gz \
    NEO4J_EDITION=community
ARG NEO4J_URI=http://dist.neo4j.org/neo4j-community-3.4.5-unix.tar.gz

COPY transitive.db.tgz /tmp/

RUN apk add --no-cache --quiet \
    bash \
    curl \
    tini \
    su-exec \
    && curl --fail --silent --show-error --location --remote-name ${NEO4J_URI} \
    && echo "${NEO4J_SHA256}  ${NEO4J_TARBALL}" | sha256sum -csw - \
    && tar --extract --file ${NEO4J_TARBALL} --directory /var/lib \
    && mv /var/lib/neo4j-* /var/lib/neo4j \
    && rm ${NEO4J_TARBALL} \
    && mv /var/lib/neo4j/data /data \
    && chown -R neo4j:neo4j /data \
    && chmod -R 777 /data \
    && chown -R neo4j:neo4j /var/lib/neo4j \
    && chmod -R 777 /var/lib/neo4j \
    && ln -s /data /var/lib/neo4j/data \
    && apk del curl \
    && tar --extract --file /tmp/transitive.db.tgz --directory /data/databases/ \
    && rm /tmp/transitive.db.tgz \
    && echo "dbms.transaction.timeout=5s" >> /var/lib/neo4j/conf/neo4j.conf \
    && echo "dbms.active_database=transitive.db" >> /var/lib/neo4j/conf/neo4j.conf

ENV PATH /var/lib/neo4j/bin:$PATH

WORKDIR /var/lib/neo4j

COPY docker-entrypoint.sh /docker-entrypoint.sh

EXPOSE 7474 7473 7687

ENTRYPOINT ["/sbin/tini", "-g", "--", "/docker-entrypoint.sh"]
CMD ["neo4j"]
