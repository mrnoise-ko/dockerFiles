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
ARG MYSQL_MINGW_SOURCE=mysql-connector-c-6.1.11-winx64

RUN wget -q https://download.qt.io/official_releases/qt/${QT_VERSION_MAJOR}.${QT_VERSION_MINOR}/${QT_VERSION}/single/${QT_DOWNLOAD} \
 && tar -xJf ${QT_DOWNLOAD} \
 && rm ${QT_DOWNLOAD} \
 && wget -q https://downloads.mysql.com/archives/get/p/19/file/${MYSQL_MINGW_SOURCE}.zip \
 && unzip ${MYSQL_MINGW_SOURCE}.zip \
 && rm ${MYSQL_MINGW_SOURCE}.zip \
 && cd ${QT_SOURCE} \
 && ./configure \
    -c++std c++17 \
    -confirm-license \
    -opensource \
    -xplatform win32-g++ \
    -device-option CROSS_COMPILE=/usr/bin/x86_64-w64-mingw32- \
    -prefix /usr/local/Qt-${QT_VERSION}/mingw_64 \
    -nomake examples \
    -nomake tests \
    -opengl desktop \
    -native-win32-bluetooth \
    -sql-mysql -I /${MYSQL_MINGW_SOURCE}/include -L /${MYSQL_MINGW_SOURCE}/lib \
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
ARG MYSQL_LINUX_SOURCE=mysql-connector-c-6.1.11-linux-glibc2.12-x86_64

RUN apt update && apt install -y \
    libfontconfig1-dev \
    libfreetype6-dev \
    libx11-dev \
    libx11-xcb-dev \
    libxext-dev \
    libxfixes-dev \
    libxi-dev \
    libxrender-dev \
    libxcb1-dev \
    libxcb-glx0-dev \
    libxcb-keysyms1-dev \
    libxcb-image0-dev \
    libxcb-shm0-dev \
    libxcb-icccm4-dev \
    libxcb-sync0-dev \
    libxcb-xfixes0-dev \
    libxcb-shape0-dev \
    libxcb-randr0-dev \
    libxcb-render-util0-dev \
    libxcb-xinerama0-dev \
    libxkbcommon-dev \
    libxkbcommon-x11-dev \
    libbluetooth-dev \
    libgl1-mesa-dev \
 && apt clean \
 && rm -rf /var/lib/apt/lists/* \
 && wget -q https://download.qt.io/official_releases/qt/${QT_VERSION_MAJOR}.${QT_VERSION_MINOR}/${QT_VERSION}/single/${QT_DOWNLOAD} \
 && tar -xJf ${QT_DOWNLOAD} \
 && rm ${QT_DOWNLOAD} \
 && wget -q https://downloads.mysql.com/archives/get/p/19/file/${MYSQL_LINUX_SOURCE}.tar.gz \
 && tar -xzf ${MYSQL_LINUX_SOURCE}.tar.gz \
 && rm ${MYSQL_LINUX_SOURCE}.tar.gz \
 && cd ${QT_SOURCE} \
 && ./configure \
    -c++std c++17 \
    -confirm-license \
    -opensource \
    -prefix /usr/local/Qt-${QT_VERSION}/linux \
    -nomake examples \
    -nomake tests \
    -sql-mysql -I /${MYSQL_LINUX_SOURCE}/include -L /${MYSQL_LINUX_SOURCE}/lib \
    -xcb \
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
