FROM ubuntu:22.04 as build-linux-mingw

ARG DEBIAN_FRONTEND=noninteractive
# Locale and time zone
RUN apt-get update && apt-get install -y \
    locales \
    tzdata \
 && rm -rf /var/lib/apt/lists/* \
 && ln -fs /usr/share/zoneinfo/Europe/Berlin /etc/localtime \
 && dpkg-reconfigure tzdata \
 && locale-gen en_US.UTF-8 && update-locale
ENV LANG "en_US.UTF-8"
ENV LANGUAGE "en_US:en"
ENV LC_ALL "en_US.UTF-8"

# Build dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    clang-tidy \
    cmake \
    curl \
    git \
    mingw-w64 \
    nano \
    ninja-build \
    python3-pip \
    unzip \
    wget \
    xsltproc \
 && apt clean \
 && rm -rf /var/lib/apt/lists/* \
 && pip3 install 'gcovr==5.1' \
 && pip3 cache purge \
 # only posix flavour supports c++ multithreading features \
 && update-alternatives --set x86_64-w64-mingw32-gcc /usr/bin/x86_64-w64-mingw32-gcc-posix \
 && update-alternatives --set x86_64-w64-mingw32-g++ /usr/bin/x86_64-w64-mingw32-g++-posix

FROM build-linux-mingw as build-mingw-qt

ARG QT_VERSION_MAJOR=5
ARG QT_VERSION_MINOR=15
ARG QT_VERSION_PATCH=5
ENV QT_VERSION=${QT_VERSION_MAJOR}.${QT_VERSION_MINOR}.${QT_VERSION_PATCH}
ARG QT_DOWNLOAD=qt-everywhere-opensource-src-${QT_VERSION}.tar.xz
ARG QT_SOURCE=qt-everywhere-src-${QT_VERSION}
ARG MYSQL_MINGW=mysql-connector-c-6.1.11-winx64
ARG OPEN_SSL=openssl-1.1.1s

RUN wget -q https://www.openssl.org/source/${OPEN_SSL}.tar.gz \
 && tar xzf ${OPEN_SSL}.tar.gz \
 && rm ${OPEN_SSL}.tar.gz \
 && cd ${OPEN_SSL} \
 && ./Configure --cross-compile-prefix=x86_64-w64-mingw32- mingw64 \
 && make -j${nproc} \
 && cd .. \
 && wget -q https://download.qt.io/official_releases/qt/${QT_VERSION_MAJOR}.${QT_VERSION_MINOR}/${QT_VERSION}/single/${QT_DOWNLOAD} \
 && tar -xJf ${QT_DOWNLOAD} \
 && rm ${QT_DOWNLOAD} \
 && wget -q https://downloads.mysql.com/archives/get/p/19/file/${MYSQL_MINGW}.zip \
 && unzip ${MYSQL_MINGW}.zip \
 && rm ${MYSQL_MINGW}.zip \
 && cd ${QT_SOURCE} \
 && ./configure \
    -c++std c++17 \
    -confirm-license \
    -opensource \
    -xplatform win32-g++ \
    -device-option CROSS_COMPILE=/usr/bin/x86_64-w64-mingw32- \
    -prefix /usr/local/Qt-${QT_VERSION}/mingw_64 \
    -native-win32-bluetooth \
    -opengl desktop \
    -openssl-runtime OPENSSL_INCDIR=/${OPEN_SSL}/include OPENSSL_LIBDIR=/${OPEN_SSL} \
    -sql-mysql -I /${MYSQL_MINGW}/include -L /${MYSQL_MINGW}/lib \
    -nomake examples \
    -nomake tests \
    -skip qt3d \
    -skip qtactiveqt \
    -skip qtandroidextras \
    -skip qtcharts \
    -skip qtdatavis3d \
    -skip qtdeclarative \
    -skip qtdoc \
    -skip qtgamepad \
    -skip qtgraphicaleffects \
    -skip qtlocation \
    -skip qtmacextras \
    -skip qtmultimedia \
    -skip qtnetworkauth \
    -skip qtpurchasing \
    -skip qtquickcontrols \
    -skip qtquickcontrols2 \
    -skip qtremoteobjects \
    -skip qtscript \
    -skip qtscxml \
    -skip qtsensors \
    -skip qtserialbus \
    -skip qtspeech \
    -skip qtsvg \
    -skip qtvirtualkeyboard \
    -skip qtwayland \
    -skip qtwebchannel \
    -skip qtwebengine \
    -skip qtwebglplugin \
    -skip qtwebsockets \
    -skip qtwebview  \
    -skip qtwinextras \
    -skip qtx11extras \
    -skip qtxmlpatterns \
 && make -j$(nproc) \
 && make install \
 && cd .. \
 && rm -rf ${QT_SOURCE}

FROM build-mingw-qt as build-linux-mingw-qt

ARG QT_VERSION_MAJOR=5
ARG QT_VERSION_MINOR=15
ARG QT_VERSION_PATCH=5
ENV QT_VERSION=${QT_VERSION_MAJOR}.${QT_VERSION_MINOR}.${QT_VERSION_PATCH}
ARG QT_DOWNLOAD=qt-everywhere-opensource-src-${QT_VERSION}.tar.xz
ARG QT_SOURCE=qt-everywhere-src-${QT_VERSION}
ARG MYSQL_LINUX=mysql-connector-c-6.1.11-linux-glibc2.12-x86_64

RUN apt update && apt install -y \
    libbluetooth-dev \
    libfontconfig1-dev \
    libfreetype6-dev \
    libgl1-mesa-dev \
    libssl-dev \
    libx11-dev \
    libx11-xcb-dev \
    libxcb1-dev \
    libxcb-glx0-dev \
    libxcb-icccm4-dev \
    libxcb-image0-dev \
    libxcb-keysyms1-dev \
    libxcb-randr0-dev \
    libxcb-render-util0-dev \
    libxcb-shape0-dev \
    libxcb-shm0-dev \
    libxcb-sync0-dev \
    libxcb-xfixes0-dev \
    libxcb-xinerama0-dev \
    libxext-dev \
    libxfixes-dev \
    libxi-dev \
    libxkbcommon-dev \
    libxkbcommon-x11-dev \
    libxrender-dev \
 && apt clean \
 && rm -rf /var/lib/apt/lists/* \
 && wget -q https://download.qt.io/official_releases/qt/${QT_VERSION_MAJOR}.${QT_VERSION_MINOR}/${QT_VERSION}/single/${QT_DOWNLOAD} \
 && tar -xJf ${QT_DOWNLOAD} \
 && rm ${QT_DOWNLOAD} \
 && wget -q https://downloads.mysql.com/archives/get/p/19/file/${MYSQL_LINUX}.tar.gz \
 && tar -xzf ${MYSQL_LINUX}.tar.gz \
 && rm ${MYSQL_LINUX}.tar.gz \
 && cd ${QT_SOURCE} \
 && ./configure \
    -c++std c++17 \
    -confirm-license \
    -opensource \
    -prefix /usr/local/Qt-${QT_VERSION}/linux \
    -openssl-runtime \
    -sql-mysql -I /${MYSQL_LINUX}/include -L /${MYSQL_LINUX}/lib \
    -xcb \
    -nomake examples \
    -nomake tests \
    -skip qt3d \
    -skip qtactiveqt \
    -skip qtandroidextras \
    -skip qtcharts \
    -skip qtdatavis3d \
    -skip qtdeclarative \
    -skip qtdoc \
    -skip qtgamepad \
    -skip qtgraphicaleffects \
    -skip qtlocation \
    -skip qtmacextras \
    -skip qtmultimedia \
    -skip qtnetworkauth \
    -skip qtpurchasing \
    -skip qtquickcontrols \
    -skip qtquickcontrols2 \
    -skip qtremoteobjects \
    -skip qtscript \
    -skip qtscxml \
    -skip qtsensors \
    -skip qtserialbus \
    -skip qtspeech \
    -skip qtsvg \
    -skip qtvirtualkeyboard \
    -skip qtwayland \
    -skip qtwebchannel \
    -skip qtwebengine \
    -skip qtwebglplugin \
    -skip qtwebsockets \
    -skip qtwebview  \
    -skip qtwinextras \
    -skip qtx11extras \
    -skip qtxmlpatterns \
 && make -j$(nproc) \
 && make install \
 && cd .. \
 && rm -rf ${QT_SOURCE}
