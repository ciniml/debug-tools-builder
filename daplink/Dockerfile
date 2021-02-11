FROM buildenv:latest
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Tokyo
# Fetch sources
RUN mkdir src && cd src && \
    git clone https://github.com/Seeed-Studio/Seeed_Arduino_DAPLink.git --depth 1 && \
    cd ..
# Add DAPLink as Arduino library
RUN ln -s /src/Seeed_Arduino_DAPLink ~/Arduino/libraries/Seeed_Arduino_DAPLink
# Build
RUN cd src/Seeed_Arduino_DAPLink/examples/custom_pins && \
    arduino-cli compile --fqbn Seeeduino:samd:seeed_XIAO_m0 --build-property build.flags.usbstack=-DUSE_TINYUSB --libraries Seeed_Arduino_DAPLink --output-dir . && \
    USER=root python3 /script/uf2conv.py -b 0x2000 custom_pins.ino.bin -o Seeeduino_XIAO_DAPLink.uf2
