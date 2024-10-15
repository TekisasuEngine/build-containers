# Tekisasu Engine build containers

This repository contains the Dockerfiles for the official Tekisasu Engine builds.
These containers should help you build Tekisasu for all platforms supported on
any machine that can run Docker containers.

The in-container build scripts are in a separate repository:
https://github.com/TekisasuEngine/tekisasu-build-scripts


## Introduction

These scripts build a number of containers which are then used to build final
Tekisasu tools, templates and server packages for several platforms.

Once these containers are built, they can be used to compile different Tekisasu
versions without the need of recreating them.

The `upload.sh` file is used upstream and is only present as reference.


## Requirements

These containers have been tested under Fedora 36 (other distros/releases may work too).

The tool used to build and manage the containers is `podman` (install it with `dnf -y podman`).

We currently use `podman` as root to build and use these containers. Documenting a workflow to
configure the host OS to be able to do all this without root would be welcome (but back when we
tried we ran into performance issues).


## Usage

The `build.sh` script included is used to build the containers themselves.

The two arguments can take any value and are meant to convey what Tekisasu branch
you are building for (e.g. `4.3`) and what Linux distribution the `Dockerfile.base`
is based on (e.g. `f40` for Fedora 40).

Run the command using:

    ./build.sh 4.3 f40

The above will generate images using the tag '4.3-f40'.
You can then specify it in the `build.sh` of
[tekisasu-build-scripts](https://github.com/TekisasuEngine/tekisasu-build-scripts).


### Selecting which images to build

If you don't need to build all versions or you want to try with a single target OS first,
you can comment out the corresponding lines from the script:

    podman_build linux
    podman_build windows
    podman_build web
    podman_build android
    ...


## Image sizes

These are the expected container image sizes, so you can plan your disk usage in advance:

    REPOSITORY                         TAG                SIZE
    localhost/tekisasu-fedora             4.3-f40            1.01 GB
    localhost/tekisasu-linux              4.3-f40            2.80 GB
    localhost/tekisasu-windows            4.3-f40            2.46 GB
    localhost/tekisasu-web                4.3-f40            2.41 GB
    localhost/tekisasu-android            4.3-f40            4.29 GB
    localhost/tekisasu-osx                4.3-f40            4.78 GB
    localhost/tekisasu-ios                4.3-f40            5.49 GB

In addition to this, generating containers will also require some host disk space
(up to 10 GB) for the dependencies (Xcode).


## Toolchains

These are the toolchains currently in use for Tekisasu 4.3 and later:

- Base image: Fedora 40
- SCons: 4.8.0
- Linux: GCC 13.2.0 built against glibc 2.28, binutils 2.40, from our own [Linux SDK](https://github.com/TekisasuEngine/buildroot)
- Windows:
  * x86_64/x86_32: MinGW 11.0.1, GCC 14.1.1, binutils 2.41
  * arm64: llvm-mingw 20240619, LLVM 18.1.8
- Web: Emscripten 3.1.64
- Android: Android NDK 23.2.8568313, build-tools 34.0.0, platform android-34, CMake 3.22.1, JDK 17
- macOS: Xcode 15.4 with Apple Clang (LLVM 16.0.0), MacOSX SDK 14.5
- iOS: Xcode 15.4 with Apple Clang (LLVM 16.0.0), iPhoneOS SDK 17.5
