FROM fuzz_cov_base

LABEL description="Dockerfile for Valkyrie"

RUN apt-get install -y git build-essential wget zlib1g-dev golang-go python-pip python-dev build-essential cmake

ENV RUSTUP_HOME=/usr/local/rustup \
    CARGO_HOME=/usr/local/cargo \
    PIN_ROOT=/pin-3.7-97619-g0d0c92f4f-gcc-linux \
    GOPATH=/go \
    PATH=/clang+llvm/bin:/usr/local/cargo/bin:/valkyrie/bin/:/go/bin:$PATH \
    LD_LIBRARY_PATH=/clang+llvm/lib:$LD_LIBRARY_PATH

RUN git clone https://github.com/ValkyrieFuzzer/valkyrie.git /valkyrie
RUN cd /valkyrie; git checkout valkyrie-solver

WORKDIR /valkyrie

ARG TAG

RUN if [[ -n "$TAG" ]] ; then git checkout $TAG ; fi

RUN ./build/install_rust.sh && \
    PREFIX=/ ./build/install_llvm.sh
RUN mkdir ${HOME}/go && go get github.com/SRI-CSL/gllvm/cmd/...

RUN ./build/build.sh

ENV CC=/valkyrie/bin/angora-clang
ENV CXX=/valkyrie/bin/angora-clang++
