FROM buildenv:latest
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Tokyo
ADD mingw.cmake /mingw.cmake
ADD hidapi-libusb.pc /libs/lib/pkgconfig/hidapi-libusb.pc

# Fetch sources
RUN mkdir src && cd src && \
    git clone https://github.com/trabucayre/openFPGALoader openFPGALoader-code --depth 1 -b master --recursive && \
    git clone https://github.com/signal11/hidapi --depth 1 && \
    git clone https://github.com/libconfuse/libconfuse.git -b master --depth 1 && \
    git clone git://developer.intra2net.com/libftdi -b master --depth 1 && \
    git clone https://github.com/libusb/libusb --depth 1 && \
    curl -L https://zlib.net/zlib-1.2.13.tar.gz | tar zx && \
    cd ..
# Make symbolic link for Windows.h
RUN cd /usr/x86_64-w64-mingw32/include && \
    ln -s windows.h Windows.h && \
    cd -

# Build cross libraries
RUN cd src/hidapi && \
    ./bootstrap && \
    ./configure --host=x86_64-w64-mingw32 --prefix=/libs --enable-static --disable-shared && \
    make && \
    make install && \
    ln -s /libs/include/hidapi/hidapi.h /libs/include/hidapi.h && \
    cd ../..
RUN cd src/libusb && \
    ./bootstrap.sh && \
    ./configure --host=x86_64-w64-mingw32 --prefix=/libs --enable-static --disable-shared && \
    make && \
    make install && \
    cd ../..
RUN cd src/libconfuse && \
    ./autogen.sh && \
    ./configure --host=x86_64-w64-mingw32 --prefix=/libs --enable-static --disable-shared --disable-examples && \
    make && \
    make install && \
    cd ../..
RUN cd src/libftdi && \
    mkdir build && \
    cd build && \
    PKG_CONFIG_LIBDIR=/libs/lib/pkgconfig cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCMAKE_INSTALL_PREFIX=/libs -DCMAKE_EXE_LINKER_FLAGS=-static -DCMAKE_TOOLCHAIN_FILE=/mingw.cmake -DLIBUSB_INCLUDE_DIR=/libs/include/libusb-1.0 -DEXAMPLES=OFF .. && \
    make && \
    make install && \
    cd ../../..
RUN cd src/zlib-1.2.12 && \
    mkdir build && \
    cd build && \
    PKG_CONFIG_LIBDIR=/libs/lib/pkgconfig cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCMAKE_INSTALL_PREFIX=/libs -DCMAKE_EXE_LINKER_FLAGS=-static -DCMAKE_TOOLCHAIN_FILE=/mingw.cmake .. && \
    make && \
    make install && \
    ln -s /libs/lib/libzlibstatic.a /libs/lib/libz.a && \
    cd ../../..
# Build cross openFPGALoader
RUN cd /src/openFPGALoader-code && \
    mkdir -p build && cd build && \
    mkdir -p /openFPGALoader && \
    PKG_CONFIG_LIBDIR=/libs/lib/pkgconfig:/libs/share/pkgconfig cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCMAKE_INSTALL_PREFIX=/openFPGALoader -DBUILD_STATIC=ON -DCMAKE_TOOLCHAIN_FILE=/mingw.cmake .. && \
    make && \
    make install && \
    cd ../../..
