FROM fuzz_cov_base

LABEL description="Dockerfile for AFL"

RUN apt update && apt install wget -y && \
    wget http://releases.llvm.org/7.0.0/clang+llvm-7.0.0-x86_64-linux-gnu-ubuntu-16.04.tar.xz -O clang+llvm.tar.xz && \
    xz -d clang+llvm.tar.xz && \
    tar xf clang+llvm.tar && \
    rm clang+llvm.tar && \
    mv clang+llvm-7.0.0-x86_64-linux-gnu-ubuntu-16.04 clang+llvm

ENV LLVM_HOME=/clang+llvm \
    PATH=/go/bin:/clang+llvm/bin:$PATH

RUN git clone https://github.com/google/AFL.git afl && \
    cd afl && \
    make && make install

RUN cd afl/llvm_mode &&\
    make

# It seems afl-clang can't compile binutils' bytecode for some assembly reason.
ENV CC=/afl/afl-clang-fast \
    CXX=/afl/afl-clang++

RUN apt update && apt install -y libc++-dev libc++abi-dev
