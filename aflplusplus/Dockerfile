# Copied from https://github.com/AFLplusplus/AFLplusplus/blob/stable/Dockerfile
# and modified by Peter.

#
# This Dockerfile for AFLplusplus uses Ubuntu 20.04 xenial and
# installs LLVM 11 from llvm.org for afl-clang-lto support :-)
# It also installs gcc/g++ 10 from the Ubuntu development platform
# since xenial has gcc-10 but not g++-10 ...
#

FROM fuzz_cov_base
LABEL "maintainer"="afl++ team <afl@aflplus.plus>"
LABEL "about"="AFLplusplus docker image"

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
    apt-get -y install --no-install-suggests --no-install-recommends \
    automake \
    bison flex \
    build-essential \
    git \
    python3 python3-dev python3-setuptools \
    libtool libtool-bin \
    libglib2.0-dev \
    wget vim jupp nano bash-completion \
    apt-utils apt-transport-https ca-certificates gnupg dialog \
    libpixman-1-dev \
    gnuplot-nox \
    zip \
    unzip \
    cmake \
    && rm -rf /var/lib/apt/lists/*

RUN echo "deb http://apt.llvm.org/xenial/ llvm-toolchain-xenial-11 main" >> /etc/apt/sources.list && \
    wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add -

RUN echo "deb http://ppa.launchpad.net/ubuntu-toolchain-r/test/ubuntu xenial main" >> /etc/apt/sources.list && \
    apt-key adv --recv-keys --keyserver keyserver.ubuntu.com 1E9377A2BA9EF27F

RUN apt-get update && apt-get full-upgrade -y 
RUN apt-get -y install --no-install-suggests --no-install-recommends \
    # gcc-10 g++-10 gcc-10-plugin-dev gcc-10-multilib gdb lcov \
    clang-11 clang-tools-11 libc++1-11 libc++-11-dev \
    libc++abi1-11 libc++abi-11-dev libclang1-11 libclang-11-dev \
    libclang-common-11-dev libclang-cpp11 libclang-cpp11-dev liblld-11 \
    liblld-11-dev liblldb-11 liblldb-11-dev libllvm11 libomp-11-dev \
    libomp5-11 lld-11 lldb-11 llvm-11 llvm-11-dev llvm-11-runtime llvm-11-tools \
    && rm -rf /var/lib/apt/lists/*

# ENV LINUX_VER=${LINUX_VER:-ubuntu-16.04}
# ENV LLVM_VER=${LLVM_VER:-11.1.0}
# ENV TAR_NAME=clang+llvm-${LLVM_VER}-x86_64-linux-gnu-${LINUX_VER}
# ENV LLVM_DEP_URL=https://github.com/llvm/llvm-project/releases/download/llvmorg-${LLVM_VER}

# RUN wget ${LLVM_DEP_URL}/${TAR_NAME}.tar.xz -O /${TAR_NAME}.tar.xz
# RUN tar -xf /${TAR_NAME}.tar.xz && rm /${TAR_NAME}.tar.xz && mv /${TAR_NAME} /clang+llvm

# ENV PATH=/clang+llvm/bin:$PATH
# ENV LD_LIBRARY_PATH=/clang+llvm/lib:$LD_LIBRARY_PATH

# RUN update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-10 0
# RUN update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-10 0

ENV LLVM_CONFIG=llvm-config-11
ENV AFL_SKIP_CPUFREQ=1

RUN git clone --depth=1 https://github.com/AFLplusplus/AFLplusplus.git /aflplusplus && \
    cd /aflplusplus && \
    ## We are using bigger table.
    sed -i 's/MAP_SIZE_POW2\ 16/MAP_SIZE_POW2\ 20/' config.h && \
    #export REAL_CXX=g++-10 && export CC=gcc-10 && export CXX=g++-10 && \
    make clean && \
    make source-only CFLAGS="-O3 -funroll-loops -D_FORTIFY_SOURCE=2" && make install

ENV IS_DOCKER="1"

LABEL description="Dockerfile for AFLplusplus"

WORKDIR /

ENV CC=afl-clang-lto \
    CXX=afl-clang-lto++
