FROM ghcr.io/sarisia/vscode-dev-containers-multiarch/base:ubuntu-20.04

ARG USERNAME=vscode

# install development tools
RUN apt-get update \
 && apt-get install -y --no-install-recommends \
    # common for EDK2
    git \
    python3-distutils \
    build-essential \
    nasm \
    acpica-tools \
    uuid-dev \
    # clang
    clang \
    llvm-dev \
    lld \
    # for arm64 cross-compile
    crossbuild-essential-amd64 \
    binutils-multiarch \
    # OS dev
    qemu-system-x86 \
    qemu-utils \
    xauth \
    unzip \
    qemu-system-gui \
    dosfstools \
 && apt-get clean -y \
 && rm -rf /var/lib/apt/lists

# # set alternatives
# RUN for item in \
#         llvm-PerfectShuffle \
#         llvm-ar \
#         llvm-as \
#         llvm-bcanalyzer \
#         llvm-cat \
#         llvm-cfi-verify \
#         llvm-config \
#         llvm-cov \
#         llvm-c-test \
#         llvm-cvtres \
#         llvm-cxxdump \
#         llvm-cxxfilt \
#         llvm-diff \
#         llvm-dis \
#         llvm-dlltool \
#         llvm-dwarfdump \
#         llvm-dwp \
#         llvm-exegesis \
#         llvm-extract \
#         llvm-lib \
#         llvm-link \
#         llvm-lto \
#         llvm-lto2 \
#         llvm-mc \
#         llvm-mca \
#         llvm-modextract \
#         llvm-mt \
#         llvm-nm \
#         llvm-objcopy \
#         llvm-objdump \
#         llvm-opt-report \
#         llvm-pdbutil \
#         llvm-profdata \
#         llvm-ranlib \
#         llvm-rc \
#         llvm-readelf \
#         llvm-readobj \
#         llvm-rtdyld \
#         llvm-size \
#         llvm-split \
#         llvm-stress \
#         llvm-strings \
#         llvm-strip \
#         llvm-symbolizer \
#         llvm-tblgen \
#         llvm-undname \
#         llvm-xray \
#         ld.lld \
#         lld-link \
#         clang \
#         clang++ \
#         clang-cpp \
#     ; do \
#         update-alternatives --install "/usr/bin/${item}" "${item}" "/usr/bin/${item}-7" 50 \
#     ; done

# switch to unprivileged
USER ${USERNAME}
WORKDIR /home/${USERNAME}

# build EDK II
RUN git clone --depth=1 https://github.com/tianocore/edk2.git edk2 \
    && cd edk2 \
    && git submodule update --init --depth=1
SHELL ["/bin/bash", "-c"]
RUN cd edk2 \
    && . ./edksetup.sh \
    && make -C BaseTools
#  && rm -rf edk2

# clone mikanos devenv
RUN git clone https://github.com/uchan-nos/mikanos-build.git osbook

# download standard libraries
RUN curl -L https://github.com/uchan-nos/mikanos-build/releases/download/v2.0/x86_64-elf.tar.gz \
  | tar xzvf - -C osbook/devenv

# switch back to root
USER root

# add ~/osbook/devenv to PATH
ENV PATH="/home/${USERNAME}/osbook/devenv:${PATH}"

# set X11 server address
ENV DISPLAY=host.docker.internal:0

# EDK2 clang toolset
ENV CLANG38_BIN=/usr/lib/llvm-10/bin/
