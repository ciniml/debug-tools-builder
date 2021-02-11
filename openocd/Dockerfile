FROM buildenv:latest
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Tokyo
# Fetch sources
RUN mkdir src && cd src && \
    git clone https://git.code.sf.net/p/openocd/code openocd-code --depth 1 --recursive && \
    git clone https://github.com/signal11/hidapi --depth 1 && \
    git clone https://github.com/libusb/libusb --depth 1 && \
    cd ..
# Build cross libraries
RUN cd src/hidapi && \
    ./bootstrap && \
    ./configure --host=x86_64-w64-mingw32 --prefix=`pwd`/../../libs --enable-static --disable-shared && \
    make && \
    make install && \
    cd ../..
RUN cd src/libusb && \
    ./bootstrap.sh && \
    ./configure --host=x86_64-w64-mingw32 --prefix=`pwd`/../../libs --enable-static --disable-shared && \
    make && \
    make install && \
    cd ../..
# Prepare OpenOCD source
RUN cd src/openocd-code && ./bootstrap && cd ../..
# Build cross OpenOCD
RUN cd src/openocd-code && \
    mkdir `pwd`/../../openocd-win && \
    mkdir build-win && cd build-win && \
    PKG_CONFIG_LIBDIR=`pwd`/../../../libs/lib/pkgconfig ../configure --prefix=`pwd`/../../../openocd-win --host=x86_64-w64-mingw32 --enable-cmsis-dap && \
    make && \
    make install && \
    cp ../README ../../../openocd-win/ && \
    cd ../..
# Build native OpenOCD
RUN cd src/openocd-code && \
    mkdir `pwd`/../../openocd-native && \
    mkdir build-native && cd build-native && \
    ../configure --prefix=`pwd`/../../../openocd-native --enable-cmsis-dap && \
    make && \
    make install && \
    cp ../README ../../../openocd-native/ && \
    cd ../..
