FROM debian:bookworm-20250929-slim AS build

ARG VERSION=

RUN apt-get -y update \
  && apt-get -y install --no-install-recommends \
    ca-certificates \
    git \
    wget \
    xz-utils \
  && apt-get -y clean \
  && rm -rf /var/lib/apt/lists/* \
  && useradd -m -s /bin/bash -u 1000 zig
WORKDIR /opt
RUN wget -q https://ziglang.org/builds/zig-linux-x86_64-${VERSION}.tar.xz \
  && tar -xf zig-linux-x86_64-${VERSION}.tar.xz \
  && mv zig-linux-x86_64-${VERSION}/* /usr/local/bin \
  && git clone https://github.com/zigtools/zls
WORKDIR /opt/zls
RUN zig build -Doptimize=ReleaseSafe \
  && mv zig-out/bin/zls /usr/local/bin \
  && rm -rf /opt/zig-linux-x86_64-${VERSION}.tar.xz \
  && rm -rf /opt/zls \
  && chown -R zig:zig /usr/local/bin
USER zig
