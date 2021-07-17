FROM buildenv:latest
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Tokyo
# Install rustc
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs -o rustup.sh
RUN sh ./rustup.sh -y
ENV PATH /root/.cargo/bin:$PATH
RUN rustup target add thumbv6m-none-eabi
# Fetch sources
RUN mkdir src && cd src && \
    git clone https://github.com/ciniml/rust-dap.git --depth 1 && \
    cd ..
# Build
RUN cd src/rust-dap/rust-dap-xiao && \
    cargo build --release && \
    cd target/thumbv6m-none-eabi/release && \
    USER=root python3 /script/uf2conv.py -b 0x2000 rust-dap-xiao -o rust-dap-xiao.uf2
