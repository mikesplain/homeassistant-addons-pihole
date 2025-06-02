FROM pihole/pihole:2025.05.1 AS pihole

ARG TARGETPLATFORM
ARG TARGETARCH
ARG TARGETVARIANT
ARG YQ_VERSION=v4.45.1
ARG SV_VERSION=0.3
ARG BUILD_ARCH
ARG BUILD_VERSION
ARG VERSION=${BUILD_VERSION}

SHELL ["/bin/bash", "-e", "-o", "pipefail", "-c"]

# Ensure that build args are given
RUN [ -n "$BUILD_ARCH" ] || (echo "Error: Build argument 'BUILD_ARCH' is missing" && exit 1)
RUN [ -n "$BUILD_VERSION" ] || (echo "Error: Build argument 'BUILD_VERSION' is missing" && exit 1)

# Update base image packages and install additional software
RUN <<EOF
    apk upgrade --no-cache
    apk add     --no-cache dumb-init nginx nginx-mod-http-lua patch
EOF

# Install yq
RUN <<EOF
    curl -sSLf -o /usr/bin/yq "https://github.com/mikefarah/yq/releases/download/$YQ_VERSION/yq_linux_$TARGETARCH"
    chmod +x /usr/bin/yq

    # Test binary
    yq --version
EOF

# Install supervisor.sh
RUN <<EOF
    curl -sSLf -o /usr/bin/supervisor.sh               "https://raw.githubusercontent.com/casperklein/supervisor.sh/refs/tags/$SV_VERSION/supervisor.sh"
    curl -sSLf -o /etc/bash_completion.d/supervisor.sh "https://raw.githubusercontent.com/casperklein/supervisor.sh/refs/tags/$SV_VERSION/supervisor-completion.bash"
    chmod +x /usr/bin/supervisor.sh
EOF

# Copy root filesystem
COPY rootfs /

# Remove docker-pi-hole update instructions
RUN sed -i 's|To install updates, <a href="https://github.com/pi-hole/docker-pi-hole#upgrading-persistence-and-customizations" rel="noopener" target="_blank">replace this old container with a fresh upgraded image</a>.||' /var/www/html/admin/scripts/js/footer.js

# Apply patches
RUN patch -i /etc/pihole-patches/start.sh.patch /usr/bin/start.sh

# TODO Temporary fix until https://github.com/pi-hole/docker-pi-hole/pull/1803 is merged and released
RUN sed -i 's| 7 PATH| 0 PATH|' /crontab.txt

# Remove obsolete files
RUN rm -f /etc/nginx/http.d/default.conf /usr/bin/sedfile

# Build dnscrypt-proxy from source #################################################################
FROM golang:1.24-bullseye AS dnscrypt

ARG TARGETPLATFORM
ARG TARGETARCH
ARG TARGETVARIANT
ARG DNSCRYPT_VERSION="2.1.8"
ENV URL="https://github.com/dnscrypt/dnscrypt-proxy/archive/$DNSCRYPT_VERSION.tar.gz"
ENV GOARCH=$TARGETARCH

# Set GOARM based on architecture
RUN if [ "$TARGETARCH" = "arm" ]; then \
        if [ -z "$TARGETVARIANT" ] || [ "$TARGETVARIANT" = "v7" ]; then \
            echo "Setting GOARM=7 for armv7" && \
            export GOARM=7; \
        else \
            echo "Setting GOARM=${TARGETVARIANT#v}" && \
            export GOARM=${TARGETVARIANT#v}; \
        fi; \
    fi

ENV DEBIAN_FRONTEND="noninteractive"
RUN apt-get update && apt-get -y upgrade

WORKDIR /build
RUN curl -sSLf "$URL" | tar --strip-component 1 -xz
WORKDIR /build/dnscrypt-proxy

# Compile with static linking (including all library code in the binary); needed for alpine image
ENV CGO_ENABLED=0
ENV GO111MODULE=on

# Builds a Go binary without symbol tables and debugging information, reducing the file size.
RUN go build -ldflags="-s -w" -mod vendor

# Test binary
RUN ./dnscrypt-proxy -version

# Build final image ################################################################################
FROM scratch

ARG BUILD_ARCH
ARG VERSION

LABEL io.hass.name="Pi-hole"
LABEL io.hass.description="Network-wide Ad Blocking"
LABEL io.hass.arch="${BUILD_ARCH}"
LABEL io.hass.type="addon"
LABEL io.hass.version="${VERSION}"

# Disable Pi-hole authentication --> Single Sign On via HA Ingress
ENV FTLCONF_webserver_api_password=""

# Let Pi-hole listen on port 8080
ENV FTLCONF_webserver_port="127.0.0.1:8080,443s"

# Disable DHCP
ENV FTLCONF_dhcp_active="false"

COPY --from=dnscrypt /build/dnscrypt-proxy/dnscrypt-proxy /usr/bin/
COPY --from=pihole / /

ENTRYPOINT ["/usr/bin/dumb-init", "--"]
CMD ["/usr/bin/supervisor.sh", "--config", "/etc/supervisor.yaml"]

# From base image
ENV DNSMASQ_USER="pihole"
ENV FTL_CMD="no-daemon"
EXPOSE 123/udp
EXPOSE 443/tcp
EXPOSE 53/tcp
EXPOSE 53/udp
EXPOSE 80/tcp
WORKDIR /
