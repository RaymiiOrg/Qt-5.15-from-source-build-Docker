# Qt-5.15-from-source-build-Docker

Qt 5.15 LTS with the KDE patch collection applied.


https://github.com/RaymiiOrg/Qt-5.15-from-source-build-Docker

More info on the KDE Qt 5.15 patches: https://community.kde.org/Qt5PatchCollection

Currently a docker image for Android.

Make sure to sync up the `NDK_VERSION` and `BUILD_TOOLS` in the Dockerfile with the `ndkVersion` in the `build.gradle` file:

Dockerfile:

    SDK_BUILD_TOOLS=30.0.2
    NDK_VERSION=22.1.7171670

build.gradle:

    buildToolsVersion '30.0.2'
    ndkVersion '22.1.7171670'


Github blocks files larger than 100 MB, so you'll need to download the assets
yourself, including their md5sum here:

    b99a69907ca29e8181852645328b6004  ./android-ndk-r22b-linux-x86_64.zip
    a858219c60cf559a1e206fd9be7f5c70  ./build-tools_r30.0.2-linux.zip
    ff016ef6fe902a22839895dbb4b8afb6  ./cmake-3.23.3-linux-x86_64.sh
    ca155336e152acb9f40697ef93772503  ./commandlinetools-linux-6609375_latest.zip
    e687a5b5cc7bb3af39d0e9528ac4a84c  ./platform-31_r01.zip
    d811f1344b16c7113733cc7b801efdab  ./platform-tools_r34.0.4-linux.zip

They are in the docker image that is on Docker hub however, but if you want to build the image
yourself you need these files.
