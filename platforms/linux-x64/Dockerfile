FROM amazonlinux:2
LABEL maintainer="Lovell Fuller <npm@lovell.info>"

# Create Amazon Linux 2 (glibc 2.26) container suitable for building Linux x64 binaries

# Path settings
ENV \
  RUSTUP_HOME="/usr/local/rustup" \
  CARGO_HOME="/usr/local/cargo" \
  PATH="/usr/local/cargo/bin:$PATH"

# Build dependencies
RUN \
  yum update -y && \
  amazon-linux-extras install epel -y && \
  yum install -y --setopt=tsflags=nodocs \
    --exclude=gcc \
    autoconf \
    automake \
    bzip2 \
    cmake3 \
    curl \
    gcc10 \
    gcc10-binutils \
    gcc10-c++ \
    gettext \
    git \
    gperf \
    jq \
    make \
    nasm \
    openssl-devel \
    patch \
    pkgconfig \
    python3 \
    tar \
    xz \
    && \
  # TODO(calebmer): The `./autogen.sh` script from `libde265` needs the script
  # `libtoolize` (from `libtool`). `libtool` requires the `gcc` package (GCC 7)
  # but we want to install GCC 10. Ideally we'd find a way to install `libtool`
  # without installing GCC 7, but for now override GCC 7 binaries with GCC 10
  # symlinks.
  yum install -y --setopt=tsflags=nodocs libtool && \
  rm /usr/bin/cc && ln -s /usr/bin/gcc10-gcc /usr/bin/cc && \
  ln -s /usr/bin/gcc10-g++ /usr/bin/c++ && \
  rm /usr/bin/as && ln -s /usr/bin/gcc10-as /usr/bin/as && \
  rm /usr/bin/ar && ln -s /usr/bin/gcc10-ar /usr/bin/ar && \
  rm /usr/bin/nm && ln -s /usr/bin/gcc10-nm /usr/bin/nm && \
  rm /usr/bin/ld && ln -s /usr/bin/gcc10-ld /usr/bin/ld && \
  rm /usr/bin/strip && ln -s /usr/bin/gcc10-strip /usr/bin/strip && \
  rm /usr/bin/ranlib && ln -s /usr/bin/gcc10-ranlib /usr/bin/ranlib && \
  rm /usr/bin/readelf && ln -s /usr/bin/gcc10-readelf /usr/bin/readelf && \
  curl https://sh.rustup.rs -sSf | sh -s -- -y \
    --no-modify-path \
    --profile minimal \
    && \
  cargo install cargo-c && \
  ln -s /usr/bin/cmake3 /usr/bin/cmake && \
  pip3 install meson ninja packaging tomli

# Compiler settings
ENV \
  PKG_CONFIG="pkg-config --static" \
  PLATFORM="linux-x64" \
  FLAGS="-march=nehalem" \
  MESON="--cross-file=/root/meson.ini"

COPY Toolchain.cmake /root/
COPY meson.ini /root/
