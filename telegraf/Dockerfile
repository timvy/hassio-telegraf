ARG BUILD_ARCH=amd64
ARG BUILD_FROM=ghcr.io/hassio-addons/debian-base/${BUILD_ARCH}:9.5.1
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Setup base system
ARG BUILD_ARCH
ARG TELEGRAF_VERSION=1.40.3

# Setup base
RUN \
    apt-get update \
    && apt-get install -y --no-install-recommends \
        iputils-ping snmp procps lm-sensors smartmontools ipmitool \
    && ARCH="${BUILD_ARCH}" \
    && if [ "${BUILD_ARCH}" = "aarch64" ]; then ARCH="arm64"; fi \
    && if [ "${BUILD_ARCH}" = "armv7" ]; then ARCH="armhf"; fi \
    && curl -J -L -o /tmp/telegraf.deb \
        "https://dl.influxdata.com/telegraf/releases/telegraf_${TELEGRAF_VERSION}-1_${ARCH}.deb" \
    && dpkg --install /tmp/telegraf.deb \
    && rm -fr \
        /tmp/* \
        /var/{cache,log}/* \
        /var/lib/apt/lists/* 

# Copy root filesystem
COPY rootfs/etc/ /etc/
COPY rootfs/bin/ /bin/

RUN chown root /etc/cont-init.d/telegraf.sh
RUN chown root /etc/telegraf/telegraf.conf
RUN chown root /etc/services.d/telegraf/run
RUN chown root /etc/services.d/telegraf/finish
RUN chmod 775 /etc/cont-init.d/telegraf.sh
RUN chmod 775 /etc/telegraf/telegraf.conf
RUN chmod 775 /etc/services.d/telegraf/run
RUN chmod 775 /etc/services.d/telegraf/finish
        
EXPOSE 8092/udp 8094 8125/udp 9273/tcp
