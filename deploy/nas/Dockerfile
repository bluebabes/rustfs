# Dedicated linux/amd64 runtime image for pre-AVX NAS CPUs (e.g. Celeron J4125).
# The binary must be compiled from this repository with x86-64-v2 RUSTFLAGS.
FROM ubuntu:24.04

RUN apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
       ca-certificates curl tzdata coreutils libssl3t64 \
    && rm -rf /var/lib/apt/lists/*

RUN groupadd -g 10001 rustfs \
    && useradd -u 10001 -g rustfs -M -s /usr/sbin/nologin rustfs \
    && mkdir -p /data/.rustfs/events /data/.rustfs/audit /logs /opt/rustfs \
    && ln -s /data/.rustfs/events /opt/rustfs/events \
    && ln -s /data/.rustfs/audit /opt/rustfs/audit \
    && chown -R rustfs:rustfs /data /logs \
    && chmod 0750 /data /logs

COPY dist/rustfs /usr/bin/rustfs
COPY entrypoint.sh /entrypoint.sh
RUN chmod 0755 /usr/bin/rustfs /entrypoint.sh

ENV RUSTFS_VOLUMES=/data \
    RUSTFS_OBS_LOG_DIRECTORY=/logs \
    RUSTFS_OBS_LOGGER_LEVEL=warn \
    RUSTFS_CONSOLE_CORS_ALLOWED_ORIGINS="*" \
    RUSTFS_USERNAME=rustfs \
    RUSTFS_GROUPNAME=rustfs \
    RUSTFS_UID=10001 \
    RUSTFS_GID=10001

EXPOSE 9000 9001
VOLUME ["/data"]
USER rustfs
ENTRYPOINT ["/entrypoint.sh"]
CMD ["rustfs"]
