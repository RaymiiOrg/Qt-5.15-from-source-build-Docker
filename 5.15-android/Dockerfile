FROM ubuntu:22.04 as source

ARG NDK_VERSION=22.1.7171670
ARG SDK_PLATFORM=android-31
ARG SDK_BUILD_TOOLS=30.0.2
ARG MIN_NDK_PLATFORM=android-21

ENV DEBIAN_FRONTEND noninteractive
ENV ANDROID_HOME /opt/android-sdk
ENV ANDROID_SDK_ROOT ${ANDROID_HOME}
ENV ANDROID_NDK_ROOT ${ANDROID_HOME}/ndk/${NDK_VERSION}
ENV ANDROID_NDK_HOST linux-x86_64
ENV ANDROID_NDK_PLATFORM ${MIN_NDK_PLATFORM}
ENV ANDROID_API_VERSION ${SDK_PLATFORM}
ENV ANDROID_BUILD_TOOLS_REVISION ${SDK_BUILD_TOOLS}
ENV ANDROID_NDK $ANDROID_NDK_ROOT
ENV STANDALONE_EXTRA="--stl=libc++"

ARG QT_VERSION=5.15.10
ENV QT_PATH /usr/local/Qt-${QT_VERSION}/ 

ENV PATH ${QT_PATH}/bin:${ANDROID_HOME}/cmdline-tools/tools/bin:${ANDROID_HOME}/tools:${ANDROID_HOME}/tools/bin:${ANDROID_HOME}/platform-tools:${PATH}

FROM source AS aptpackages
 
RUN set -eux && \   
    dpkg --add-architecture i386 && \ 
    apt -y update && \
    apt -y full-upgrade && \
    apt -y install --no-install-recommends \
		freeglut3-dev \
		gdb \
		git \
		iputils-ping \
		libgl1-mesa-dev \
		libjpeg-dev \
		libmysqlclient-dev \
		libnss3-dev \
		libopus-dev \
		libsqlite3-dev \
		libssl-dev \
		libx11-xcb-dev \
		libxcb-xinerama0-dev \
		libxcb1-dev \
		libxcursor-dev \
		libxi-dev \
		libxkbcommon-dev \
		libxkbcommon-x11-dev \
		libxml2-dev \
		coreutils \
		libxrender-dev \
		lzip \
		mesa-common-dev \
		nano \
		perl \
		python3 \
		unzip \
		valgrind \
		wget \
		zlib1g-dev \
	    ant \
	    autoconf \
	    automake \
	    autopoint \
	    autotools-dev \
	    bison \
	    build-essential \
	    ca-certificates \
	    ccache \
	    curl \
	    flex \
	    gettext \
	    git \
	    gperf \
	    libc6:i386 \
	    libdbus-1-3 \
	    libfontconfig1 \
	    libice6 \
	    libncurses5:i386 \
	    libsm6 \
	    libstdc++6:i386 \
	    libtool \
	    libx11-xcb1 \
	    libxext6 \
	    libxkbcommon-x11-0 \
	    libxrender1 \
	    libz1:i386 \
	    locales \
	    make \
	    nasm \
	    ninja-build \
	    openjdk-11-jdk \
	    openjdk-11-jdk-headless \
	    openjdk-11-jre-headless \
	    openssh-client \
	    p7zip \
	    p7zip-full \
	    p7zip-rar \
	    pkg-config \
	    python3 \
	    python3-distutils \
	    python3-future \
	    python3-lxml \
	    python3-paramiko \
	    python3-pip \
	    python3-requests \
	    python3-venv \
	    python3-xdg \
	    python3-yaml \
	    rdfind \
	    shared-mime-info \
	    sudo \
	    zip \
	    zstd \
       	unzip \
        '^libxcb.*-dev' \
        build-essential \
        cmake \
        libglu1-mesa-dev \
        libpng-dev \
        libxcb-xkb-dev \
        libxslt-dev \
		libgl-dev && \
    apt -y autoremove && \
    apt -y autoclean && \
    apt -y clean && \
    rm -rf /var/lib/apt/lists/* && \
    locale-gen en_US.UTF-8 && \
    dpkg-reconfigure locales && \
    update-alternatives --install /usr/bin/python python /usr/bin/python3 10 && \
    exit 0



ENV JAVA_HOME /usr/lib/jvm/java-11-openjdk-amd64

FROM aptpackages AS androidtools

COPY platform-31_r01.zip /tmp/platform-31_r01.zip
RUN mkdir -p /opt/android-sdk/platforms && \
 	unzip -q /tmp/platform-31_r01.zip -d /opt/android-sdk/platforms && \
 	mv /opt/android-sdk/platforms/android-12 /opt/android-sdk/platforms/android-31 && \
 	rm /tmp/platform-31_r01.zip

COPY android-ndk-r22b-linux-x86_64.zip /tmp/android-ndk-r22b-linux-x86_64.zip
RUN mkdir -p /opt/android-sdk/ndk && \
	unzip -q /tmp/android-ndk-r22b-linux-x86_64.zip -d /opt/android-sdk/ndk && \
	mv /opt/android-sdk/ndk/android-ndk-r22b /opt/android-sdk/ndk/22.1.7171670 && \
	rm /tmp/android-ndk-r22b-linux-x86_64.zip 


COPY platform-tools_r34.0.4-linux.zip /tmp/platform-tools_r34.0.4-linux.zip
RUN mkdir -p /opt/android-sdk/ && \
	unzip -q /tmp/platform-tools_r34.0.4-linux.zip -d /opt/android-sdk && \
	rm /tmp/platform-tools_r34.0.4-linux.zip

COPY build-tools_r30.0.2-linux.zip /tmp/build-tools_r30.0.2-linux.zip
RUN mkdir -p /opt/android-sdk/build-tools && \
	unzip -q /tmp/build-tools_r30.0.2-linux.zip -d /opt/android-sdk/build-tools && \
	mv /opt/android-sdk/build-tools/android-11 /opt/android-sdk/build-tools/30.0.2 && \
	rm /tmp/build-tools_r30.0.2-linux.zip

COPY commandlinetools-linux-6609375_latest.zip /tmp/sdk-tools.zip
RUN mkdir -p /opt/android-sdk/cmdline-tools && \
 	unzip -q /tmp/sdk-tools.zip -d /opt/android-sdk/cmdline-tools && \
 	rm /tmp/sdk-tools.zip

FROM androidtools AS cmake

RUN mkdir /opt/nativetooling
COPY cmake-3.23.3-linux-x86_64.sh /tmp/cmake.sh
RUN mkdir -p /opt/cmake && \
    bash /tmp/cmake.sh --skip-license --prefix=/opt/cmake --exclude-subdir
ENV PATH="/opt/cmake/bin:${PATH}"

FROM cmake AS qtgitclone

RUN cd /opt && \
	mkdir build-qt && \
	git clone --depth 1 --branch kde/5.15 \
	https://invent.kde.org/qt/qt/qt5.git && \
	cd qt5 && \
	git submodule update --init --recursive --depth 1

FROM qtgitclone AS sslgitclone

RUN cd /opt && \
	git clone --depth 1 \
	https://github.com/KDAB/android_openssl

FROM sslgitclone AS qtbuild

RUN cd /opt/qt5/ && \	
	mkdir build-qt && \
	cd build-qt && \
    ../configure \
	-xplatform android-clang \
	-verbose \
	-disable-rpath \
	-android-ndk ${ANDROID_NDK_ROOT} \
	-android-sdk ${ANDROID_HOME} \
	-no-warnings-are-errors \
	-opensource \
	-confirm-license \
	-nomake tests \
	-nomake examples \
	-qt-freetype \
	-qt-harfbuzz \
	-qt-libjpeg \
	-qt-libpng \
	-qt-pcre \
	-qt-zlib \
	-skip 3d \
	-skip activeqt \
	-skip canvas3d \
	-skip charts \
	-skip connectivity \
	-skip datavis3d \
	-skip doc \
	-skip gamepad \
	-skip location \
	-skip lottie \
	-skip macextras  \
	-skip multimedia \
	-skip networkauth \
	-skip qtwebengine \
	-skip quick3d \
	-skip quicktimeline \
	-skip remoteobjects \
	-skip script \
	-skip scxml \
	-skip sensors \
	-skip serialbus \
	-skip serialport \
	-skip speech \
	-skip virtualkeyboard \
	-skip wayland \
	-skip webchannel \
	-skip webengine \
	-skip webglplugin \
	-skip websockets \
	-skip webview \
	-skip x11extras \
	-skip xmlpatterns \
	-no-feature-d3d12 \
	-ssl \
	OPENSSL_INCDIR='/opt/android_openssl/ssl_1.1/include/' \
	OPENSSL_LIBS_DEBUG="-llibssl -llibcrypto" \
	OPENSSL_LIBS_RELEASE="-llibssl -llibcrypto" \
	-skip winextras || cat config.log && \
	make -j8 && \
    make install && \
    cd /opt && \
    rm -rf /opt/qt5/ && \
	exit 0


FROM qtbuild AS uservars

RUN chmod a+w /opt/
RUN chmod a+w /usr/local
RUN mkdir /opt/Qt

RUN groupadd -r user && useradd --create-home --gid user user && echo 'user ALL=NOPASSWD: ALL' > /etc/sudoers.d/user

USER user
WORKDIR /home/user
ENV HOME /home/user

ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8
ENV PERSIST=1

ENTRYPOINT ["/bin/bash"]
