FROM ubuntu:22.04

ENV DEBIAN_FRONTEND noninteractive
RUN \
    apt-get -y update && \
    apt-get -y dist-upgrade && \
    apt-get -y install build-essential yasm nasm \
        xxd pkgconf curl wget unzip zip git subversion mercurial rsync jq \
        autoconf automake libtool libtool-bin autopoint gettext cmake clang meson ninja-build \
        texinfo texi2html help2man flex bison groff \
        gperf itstool ragel libc6-dev zlib1g-dev libssl-dev \
        gtk-doc-tools gobject-introspection gawk \
        ocaml ocamlbuild libnum-ocaml-dev indent p7zip-full \
        python3-distutils python3-jinja2 python3-jsonschema python3-apt python-is-python3 && \
    apt-get -y clean && \
    git config --global user.email "builder@localhost" && \
    git config --global user.name "Builder" && \
    git config --global advice.detachedHead false


RUN DEBIAN_FRONTEND=noninteractive apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y libopenjp2-7-dev \
    ninja-build cmake git python3 python3-pip nasm xxd pkg-config curl unzip

RUN cd /tmp && wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-ubuntu2204.pin && \
    mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600 && \
    wget -nv https://developer.download.nvidia.com/compute/cuda/12.4.0/local_installers/cuda-repo-ubuntu2204-12-4-local_12.4.0-550.54.14-1_amd64.deb && \
    dpkg -i cuda-repo-ubuntu2204-12-4-local_12.4.0-550.54.14-1_amd64.deb
RUN cp /var/cuda-repo-ubuntu2204-12-4-local/cuda-*-keyring.gpg /usr/share/keyrings/
RUN apt-get update && \
    apt-get -y install cuda-toolkit-12-4 && \
    apt-get install -y cuda-drivers && \
    apt-get install -y nvidia-driver-550-open && apt-get install -y cuda-drivers-550 && rm -rf /tmp/cuda-repo-ubuntu2204-12-4-local_12.4.0-550.54.14-1_amd64.deb

RUN cd /tmp && git clone https://github.com/FFmpeg/nv-codec-headers.git && cd nv-codec-headers && make && make install


ENV CARGO_HOME="/opt/cargo" RUSTUP_HOME="/opt/rustup" PATH="/opt/cargo/bin:${PATH}"
RUN curl https://sh.rustup.rs -sSf | bash -s -- -y --no-modify-path && \
    cargo install cargo-c && rm -rf "${CARGO_HOME}"/registry "${CARGO_HOME}"/git

RUN --mount=src=.,dst=/input \
    for s in /input/*.sh; do cp $s /usr/bin/$(echo $s | sed -e 's|.*/||' -e 's/\.sh$//'); done

ENV HOST_CC="gcc" \
    HOST_CXX="g++" \
    HOST_CFLAGS="-O2 -pipe" \
    HOST_CXXFLAGS="-O2 -pipe"
