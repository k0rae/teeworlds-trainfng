Trainfng is a Teeworlds mod created from [ddnet](https://github.com/ddnet/ddnet).
The aim of this mod is to be able to train easily to the [fng](https://github.com/fstd/teeworlds) mod.

Cloning
-------

To clone this repository with full history and external libraries (~350 MB):

    git clone --recursive https://github.com/35niavlys/teeworlds-trainfng

To clone this repository with full history when you have the necessary libraries on your system already (~220 MB):

    git clone https://github.com/35niavlys/teeworlds-trainfng

To clone this repository with history since we moved the libraries to https://github.com/ddnet/ddnet-libs (~40 MB):

    git clone --shallow-exclude=included-libs https://github.com/35niavlys/teeworlds-trainfng

To clone the libraries if you have previously cloned TrainFNG without them:

    git submodule update --init --recursive

Dependencies on Linux
---------------------

You can install the required libraries on your system, `touch CMakeLists.txt` and CMake will use the system-wide libraries by default. You can install all required dependencies and CMake on Debian or Ubuntu like this:

    sudo apt install build-essential cmake git libcurl4-openssl-dev libssl-dev libfreetype6-dev libglew-dev libnotify-dev libogg-dev libopus-dev libopusfile-dev libpnglite-dev libsdl2-dev libsqlite3-dev libwavpack-dev python

Or on Arch Linux like this:

    sudo pacman -S --needed base-devel cmake curl freetype2 git glew libnotify opusfile python sdl2 sqlite wavpack

There is an [AUR package for pnglite](https://aur.archlinux.org/packages/pnglite/). For instructions on installing it, see [AUR packages installation instructions on ArchWiki](https://wiki.archlinux.org/index.php/Arch_User_Repository#Installing_packages).

If you don't want to use the system libraries, you can pass the `-DPREFER_BUNDLED_LIBS=ON` parameter to cmake.

Building on Linux and macOS
---------------------------

To compile TrainFNG yourself, execute the following commands in the source root:

    mkdir build
    cd build
    cmake ..
    make -j$(nproc)

Pass the number of threads for compilation to `make -j`. `$(nproc)` in this case returns the number of processing units. TrainFNG requires additional libraries, that are bundled for the most common platforms (Windows, Mac, Linux, all x86 and x86\_64). The bundled libraries are now in the ddnet-libs submodule.

The following is a non-exhaustive list of build arguments that can be passed to the `cmake` command-line tool in order to enable or disable options in build time:

* **-DCMAKE_BUILD_TYPE=[Release|Debug|RelWithDebInfo|MinSizeRel]** <br>
An optional CMake variable for setting the build type. If not set, defaults to "Release" if `-DDEV=ON` is **not** used, and "Debug" if `-DDEV=ON` is used. See `CMAKE_BUILD_TYPE` in CMake Documentation for more information.

* **-DPREFER_BUNDLED_LIBS=[ON|OFF]** <br>
Whether to prefer bundled libraries over system libraries. Setting to ON will make TrainFNG use third party libraries available in the `ddnet-libs` folder, which is the git-submodule target of the [ddnet-libs](https://github.com/ddnet/ddnet-libs) repository mentioned above -- Useful if you do not have those libraries installed and want to avoid building them. If set to OFF, will only use bundled libraries when system libraries are not found. Default value is OFF.

* **-DWEBSOCKETS=[ON|OFF]** <br>
Whether to enable WebSocket support for server. Setting to ON requires the `libwebsockets-dev` library installed. Default value is OFF.

* **-DMYSQL=[ON|OFF]** <br>
Whether to enable MySQL/MariaDB support for server. Requires at least MySQL 8.0 or MariaDB 10.2. Setting to ON requires the `libmariadbclient-dev`, `libmysqlcppconn-dev` and `libboost-dev` libraries installed, which are also provided as bundled libraries for the common platforms. Default value is OFF.

   Note that the bundled MySQL libraries might not work properly on your system. If you run into connection problems with the MySQL server, for example that it connects as root while you chose another user, make sure to install your system libraries for the MySQL client and C++ connector. Make sure that the CMake configuration summary says that it found MySQL libs that were not bundled (no "using bundled libs").

* **-DAUTOUPDATE=[ON|OFF]** <br>
Whether to enable the autoupdater. Packagers may want to disable this for their packages. Default value is ON for Windows and Linux.

* **-DCLIENT=[ON|OFF]** <br>
Whether to enable client compilation. If set to OFF, TrainFNG will not depend on Curl, Freetype, Ogg, Opus, Opusfile, and SDL2. Default value is ON.

* **-DVIDEORECORDER=[ON|OFF]** <br>
Whether to add video recording support using FFmpeg to the client. You can use command `start_video` and `stop_video` to start and stop conversion from demo to mp4. This feature is currently experimental and not enabled by default.

Dependencies needed on debian: `libx264-dev libavfilter-dev libavdevice-dev libavformat-dev libavcodec-extra libavutil-dev`

* **-DDOWNLOAD_GTEST=[ON|OFF]** <br>
Whether to download and compile GTest. Useful if GTest is not installed and, for Linux users, there is no suitable package providing it. Default value is OFF.

* **-DDEV=[ON|OFF]** <br>
Whether to optimize for development, speeding up the compilation process a little. If enabled, don't generate stuff necessary for packaging. Setting to ON will set CMAKE\_BUILD\_TYPE to Debug by default. Default value is OFF.

* **-DUPNP=[ON|OFF]** <br>
Whether to enable UPnP support for the server.
You need to install `libminiupnpc-dev` on Debian, `miniupnpc` on Arch Linux.

* **-GNinja** <br>
Use the Ninja build system instead of Make. This automatically parallizes the build and is generally faster. Compile with `ninja` instead of `make`. Install Ninja with `sudo apt install ninja-build` on Debian, `sudo pacman -S --needed ninja` on Arch Linux.

Running tests (Debian/Ubuntu)
-----------------------------

In order to run the tests, you need to install the following library `libgtest-dev`.

This library isn't compiled, so you have to do it:
```bash
sudo apt install libgtest-dev
cd /usr/src/gtest
sudo cmake CMakeLists.txt
sudo make -j8
 
# copy or symlink libgtest.a and libgtest_main.a to your /usr/lib folder
sudo cp *.a /usr/lib
```

To run the tests you must target `run_tests` with make:
`make run_tests`

Building on Windows with Visual Studio
--------------------------------------

Download and install some version of [Microsoft Visual Studio](https://www.visualstudio.com/) (as of writing, MSVS Community 2019) with **C++ support**, install [Python 3](https://www.python.org/downloads/) **for all users** and install [CMake](https://cmake.org/download/#latest).

You should be able to compile TrainFNG by clicking the green, triangular "Run" button.

Cross-compiling on Linux to Windows x86/x86\_64
-----------------------------------------------

Install MinGW cross-compilers of the form `i686-w64-mingw32-gcc` (32 bit) or
`x86_64-w64-mingw32-gcc` (64 bit). This is probably the hard part. ;)

Then add `-DCMAKE_TOOLCHAIN_FILE=../cmake/toolchains/mingw64.toolchain` to the
**initial** CMake command line.

Cross-compiling on Linux to macOS
---------------------------------

Install [osxcross](https://github.com/tpoechtrager/osxcross), then add
`-DCMAKE_TOOLCHAIN_FILE=../cmake/toolchains/darwin.toolchain` and
`-DCMAKE_OSX_SYSROOT=/path/to/osxcross/target/SDK/MacOSX10.11.sdk/` to the
**initial** CMake command line.

Install `dmg` and `hfsplus` from
[libdmg-hfsplus](https://github.com/mozilla/libdmg-hfsplus) and `newfs_hfs`
from
[diskdev\_cmds](http://pkgs.fedoraproject.org/repo/pkgs/hfsplus-tools/diskdev_cmds-540.1.linux3.tar.gz/0435afc389b919027b69616ad1b05709/diskdev_cmds-540.1.linux3.tar.gz)
to unlock the `package_dmg` target that outputs a macOS disk image.
