FROM buildenv:latest
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Tokyo
ADD mingw.cmake /mingw.cmake
# Fetch sources
RUN mkdir src && cd src && \
    git clone https://github.com/openocd-org/openocd openocd-code --depth 1 -b v0.12.0-rc3 --recursive && \
    git clone https://github.com/signal11/hidapi --depth 1 && \
    git clone https://github.com/libusb/libusb --depth 1 && \
    git clone https://github.com/raspberrypi/picotool --depth 1 && \
    git clone https://github.com/raspberrypi/pico-sdk --depth 1 && \
    cd ..
# Make symbolic link for Windows.h
RUN cd /usr/x86_64-w64-mingw32/include && \
    ln -s windows.h Windows.h && \
    cd -

# Build cross libraries
RUN cd src/hidapi && \
    ./bootstrap && \
    ./configure --host=x86_64-w64-mingw32 --prefix=`pwd`/../../libs --enable-static --disable-shared && \
    make -j `nproc` && \
    make install && \
    cd ../..
RUN cd src/libusb && \
    ./bootstrap.sh && \
    ./configure --host=x86_64-w64-mingw32 --prefix=`pwd`/../../libs --enable-static --disable-shared && \
    make -j `nproc` && \
    make install && \
    cd ../..
# Prepare OpenOCD source
RUN cd src/openocd-code && ./bootstrap && cd ../..
# Build cross OpenOCD
RUN cd src/openocd-code && \
    mkdir `pwd`/../../openocd-win && \
    mkdir build-win && cd build-win && \
    PKG_CONFIG_LIBDIR=`pwd`/../../../libs/lib/pkgconfig ../configure --prefix=`pwd`/../../../openocd-win --host=x86_64-w64-mingw32 --enable-cmsis-dap --enable-picoprobe && \
    make -j `nproc` && \
    make install && \
    cp ../README ../../../openocd-win/ && \
    cd ../..
# Build native OpenOCD
RUN cd src/openocd-code && \
    mkdir `pwd`/../../openocd-linux && \
    mkdir build-native && cd build-native && \
    ../configure --prefix=`pwd`/../../../openocd-linux --enable-cmsis-dap --enable-picoprobe && \
    make -j `nproc` && \
    make install && \
    cp ../README ../../../openocd-linux/ && \
    cd ../..
# Build native picotool
RUN cd src/picotool && \
    mkdir `pwd`/../../picotool-linux && \
    export PICO_SDK_PATH=`pwd`/../pico-sdk && \
    mkdir build-linux && cd build-linux && \
    cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo .. && \
    make -j `nproc` && \
    cp picotool ../../../picotool-linux/ && \
    cp ../README.md ../../../picotool-linux/ && \
    cd ../..
# Build cross picotool
RUN cd src/picotool && \
    mkdir `pwd`/../../picotool-win && \
    export PICO_SDK_PATH=`pwd`/../pico-sdk && \
    mkdir build-win && cd build-win && \
    PKG_CONFIG_LIBDIR=`pwd`/../../../libs/lib/pkgconfig cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCMAKE_EXE_LINKER_FLAGS=-static -DCMAKE_TOOLCHAIN_FILE=`pwd`/../../../mingw.cmake -DLIBUSB_INCLUDE_DIR=`pwd`/../../../libs/include/libusb-1.0 .. && \
    make -j `nproc` && \
    cp picotool.exe ../../../picotool-win/ && \
    cp ../README.md ../../../picotool-win/ && \
    cd ../..
