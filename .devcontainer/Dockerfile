FROM debian:bookworm-20231009-slim

RUN apt-get -y update \
  && apt-get -y install --no-install-recommends wget ca-certificates xz-utils \
  && apt-get -y clean \
  && rm -rf /var/lib/apt/lists/*

ARG VERSION=
RUN wget https://ziglang.org/builds/zig-linux-x86_64-${VERSION}.tar.xz \
  && tar -xf zig-linux-x86_64-${VERSION}.tar.xz \
  && mv zig-linux-x86_64-${VERSION}/* /usr/local/bin