FROM alpine:3.17.1 AS grab

ARG COREDNS_VERSION=1.10.0

WORKDIR /tmp

RUN set -xe \
    && apk add --no-cache \
        bind-tools \
        ca-certificates \
        openssl \
        curl \
        git \
        dumb-init \
    && update-ca-certificates

# RUN curl -fsSL \
#     "https://github.com/coredns/coredns/archive/refs/tags/v${COREDNS_VERSION}.tar.gz" \    
#     | tar xzf - -C . --strip-components=1 \
#     && echo "unbound:unbound" >> plugin.cfg

RUN git clone https://github.com/coredns/coredns.git \
  && echo "unbound:github.com/coredns/unbound" >> /tmp/coredns/plugin.cfg

FROM golang:1.19.13 AS build

COPY --from=grab /tmp/coredns /project

WORKDIR /project

ENV CGO_ENABLED=1

RUN apt-get -qq update \
    && apt-get -yyqq upgrade \
    && apt-get -yyqq install libunbound-dev

RUN make

RUN mkdir /app \
    && ls \
    && mv coredns /app

RUN /app/coredns -version

RUN rm -r /project

EXPOSE 53 53/udp

VOLUME ["/etc/coredns/"]

# ENTRYPOINT ["/coredns"]

CMD ["/app/coredns", "-conf", "/etc/coredns/Corefile"]

ENTRYPOINT ["/app/coredns"]