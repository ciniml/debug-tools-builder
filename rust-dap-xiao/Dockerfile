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
# Install elf2uf2-rs
RUN cargo install elf2uf2-rs
# Build RPi Pico
RUN cd src/rust-dap/boards/rpi_pico && \
    cargo build --release --features=swd,set_clock,ram-exec && \
    cd target/thumbv6m-none-eabi/release && \
    elf2uf2-rs rust-dap-raspberrypi-pico
# # Build XIAO
# RUN cd src/rust-dap/boards/xiao_m0 && \
#     cargo build --release && \
#     cd target/thumbv6m-none-eabi/release && \
#     elf2uf2-rs rust-dap-xiao
# Build XIAO RP2040
RUN cd src/rust-dap/boards/xiao_rp2040 && \
    cargo build --release --features=set_clock,ram-exec && \
    cd target/thumbv6m-none-eabi/release && \
    elf2uf2-rs rust-dap-xiao-rp2040