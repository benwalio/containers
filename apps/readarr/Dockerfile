ARG VERSION
FROM docker.io/library/alpine:3.21

ARG VERSION
ARG CHANNEL
ARG TARGETPLATFORM
ARG TARGETARCH
ARG TARGETOS

LABEL dev.benwalio.image.target_platform=$TARGETPLATFORM
LABEL dev.benwalio.image.target_architecture=$TARGETARCH
LABEL dev.benwalio.image.target_os=$TARGETOS
LABEL org.opencontainers.image.source="https://github.com/Readarr/Readarr"

ENV \
    COMPlus_EnableDiagnostics="0" \
    READARR__UPDATE__BRANCH="${CHANNEL}"

ENV UMASK="0002" \
    TZ="Etc/UTC"

USER root
WORKDIR /app

#hadolint ignore=DL3018,DL3059
RUN \
    apk add --no-cache \
        bash \
        ca-certificates \
        catatonit \
        coreutils \
        curl \
        icu-libs \
        jo \
        jq \
        libintl \
        nano \
        sqlite-libs \
        trurl \
        tzdata \
        xmlstarlet \
    && \
    case "${TARGETPLATFORM}" in \
        'linux/amd64') export ARCH='x64' ;; \
        'linux/arm64') export ARCH='arm64' ;; \
    esac \
    && mkdir -p /app/bin \
    && curl -fsSL "https://readarr.servarr.com/v1/update/${READARR__UPDATE__BRANCH}/updatefile?version=${VERSION}&os=linuxmusl&runtime=netcore&arch=${ARCH}" \
        | tar xzf - -C /app/bin --strip-components=1 \
    && printf "UpdateMethod=docker\nBranch=%s\nPackageVersion=%s\nPackageAuthor=[benwalio](https://github.com/benwalio)\n" "${READARR__UPDATE__BRANCH}" "${VERSION}" > /app/package_info \
    && chown -R root:root /app && chmod -R 755 /app \
    && rm -rf /tmp/* /app/bin/Readarr.Update

COPY ./apps/readarr/entrypoint.sh /entrypoint.sh

USER nobody:nogroup
WORKDIR /config
VOLUME ["/config"]

ENTRYPOINT ["/usr/bin/catatonit", "--", "/entrypoint.sh"]
