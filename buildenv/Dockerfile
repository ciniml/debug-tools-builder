FROM ubuntu:20.04
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Tokyo
# Install required packages
RUN apt-get update && \
    apt-get install -y tzdata && \
    apt-get install -y make autoconf autopoint flex pkg-config git gcc g++ mingw-w64 libtool libhidapi-dev libusb-1.0-0-dev libudev-dev curl python3 zip cmake && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
# Install arduino-cli
RUN curl -fsSL https://raw.githubusercontent.com/arduino/arduino-cli/master/install.sh | BINDIR=/usr/local/bin sh
# Install Arduino core
RUN arduino-cli core update-index --additional-urls https://files.seeedstudio.com/arduino/package_seeeduino_boards_index.json && \
    arduino-cli core install --additional-urls https://files.seeedstudio.com/arduino/package_seeeduino_boards_index.json Seeeduino:samd@1.8.1
# Install dependencies
RUN arduino-cli lib install --no-deps "Adafruit TinyUSB Library"@0.10.5
RUN mkdir script && cd script && curl -s -o uf2conv.py https://raw.githubusercontent.com/microsoft/uf2/master/utils/uf2conv.py && curl -s -o uf2families.json https://raw.githubusercontent.com/microsoft/uf2/master/utils/uf2families.json && cd ..
