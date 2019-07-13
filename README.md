# libssh2lv: A LabVIEW-Friendly C library for libssh2

The libssh2lv project is a [LabVIEW&trade;](http://www.ni.com/labview)-friendly C "wrapper" library for the [libssh2](http://libssh2.org) library. The library is intended to be used with the [Call Library Function](http://zone.ni.com/reference/en-XX/help/371361P-01/glang/call_library_function/) node. This provides SSH client functionality to LabVIEW as a <abbr title="Dynamic Link Library">DLL</abbr> (Windows), <abbr title="Dynamic Library">Dylib</abbr> (macOS), and/or <abbr title="Shared Object">SO</abbr> (Linux).

## Table of Contents

- [Installation](#installation) 
- [Build](#build)
- [Tests](#tests) 
- [License](#license)

## Installation

A single ZIP archive containing the pre-compiled/built shared libraries for all of the desktop platforms listed in the [Build](#build) section is provided with each [release](https://github.com/fieldrndservices/libssh2lv/releases). These pre-compiled/built shared libraries include software developed by the OpenSSL Project for use in the OpenSSL Toolkit (http://www.openssl.org).

1. Download the ZIP archive for the latest release. Note, this is _not_ the source code ZIP file. The ZIP archive containing the pre-compiled/built shared libraries will be labeled: `libssh2lv_#.#.#.zip`, where `#.#.#` is the version number for the release.
2. Extract, or unzip, the ZIP archive.
3. Copy and paste all or the platform-specific shared libraries to one of the following locations on disk:

| Platform    | Destination           |
|-------------|-----------------------|
| Windows     | `C:\Windows\System32` |
| macOS       | `/usr/local/lib`      |
| Linux       | `/usr/local/lib`      |

## Build

Ensure all of the following dependencies are installed and up-to-date before proceeding:

- [CMake 3.4.x](https://cmake.org/), or newer
- [Microsoft Visual C++ Build Tools 2017](https://www.visualstudio.com/downloads/#build-tools-for-visual-studio-2017), Windows Only
- [XCode Command Line Tools](https://developer.apple.com/xcode/features/), macOS Only
- [Git](https://git-scm.com/)
- [Doxygen](http://www.doxygen.org), Documentation only
- [ActivePerl](https://www.perl.org/), Windows Only 

The [OpenSSL](https://www.openssl.org/) and [libssh2](https://www.libssh2.org/) dependencies will automatically be downloaded and built as part of the build process. This is to ensure the correct version is used and that these dependencies are statically linked to the eventual libssh2lv shared library. Statically linking the dependencies avoids having to distribute separate shared libraries for OpenSSL and libssh2 on each of the supported desktop platforms (Windows, macOS, and Linux). 

For NI Linux RT, the libssh2 shared library must be installed on the platform. The `libssh2lv` shared library is _not_ statically compiled with the libssh2 dependencies for the NI Linux RT operating system. Instead, a [libssh2 package is available from a sibling project](https://github.com/fieldrndservices/libssh2-nilrt-ipk) that can be installed using the [opkg](http://www.ni.com/en-us/innovations/white-papers/13/under-the-hood-of-ni-linux-real-time.html#section--222824847) package manager that is shipped with the NI Linux RT operating system.

The first build of the project on a system will take longer because the OpenSSL and libssh2 libraries will be downloaded and built. These dependencies can be found in the `.deps` folder.

### Windows

The [Microsoft Visual C++ Build Tools 2019](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16) should have installed a `x64 Native Build Tools` command prompt. Start the `x64 Native Build Tools` command prompt. This ensures the appropriate C compiler is available to CMake to build the library. Run the following commands to obtain a copy of the source code and build both the 32-bit and 64-bit DLLs with a `Release` configuration:

    C:\> git clone https://github.com/fieldrndservices/libssh2lv.git
    C:\> cd libssh2lv
    C:\> build.bat

The DLLs (libssh2lv.dll and libssh2lv-x64.dll) will be available in the `build32\bin` and `build64\bin` folders. 

### macOS

Ensure the command-line tools for [XCode](https://developer.apple.com/xcode/) have been installed along with [git](https://git-scm.com/) before proceeding.

    $ git clone https://github.com/fieldrndservices/libssh2lv.git
    $ cd libssh2lv
    $ mkdir build && cd build
    $ cmake ..
    $ cmake --build .

The dynamic library (libssh2lv.dylib) will be available in the `bin` folder within the `build` folder.

### Linux

If running on [Ubuntu](https://www.ubuntu.com/) or similar [Debian](https://www.debian.org/)-based distribution, ensure the [build-essential](https://packages.ubuntu.com/trusty/build-essential), [cmake](https://packages.ubuntu.com/trusty/cmake), and [git](https://packages.ubuntu.com/trusty/git) packages are installed before proceeding. These can be installed with the following command from a terminal:

    $ sudo apt-get install build-essential cmake git

Start a terminal, and run the following commands to obtain a copy of the source code from the repository and build the shared object (so):

    $ git clone https://github.com/fieldrndservices/libssh2lv.git
    $ cd libssh2lv
    $ mkdir build && cd build
    $ cmake ..
    $ cmake --build .

The shared object (libssh2lv.so) will be available in the `bin` folder.

### NI Linux RT

The recommended procedure for building the shared object for NI Linux RT is to use and follow the [build instructions](https://github.com/fieldrndservices/libssh2lv-nilrt-ipk#build) for the [libssh2lv-nilrt-ipk](https://github.com/fieldrndservices/libssh2lv-nilrt-ipk) project.

Additionally, while [NI](http://www.ni.com) provides a [cross-compiler](http://www.ni.com/download/labview-real-time-module-2017/6731/en/) for their Real-Time (RT) Linux distribution, it is relatively difficult, if not impossible, to configure and use the cross-compiler to build the shared object. The shared object requires the libssh2 and libcrypto libraries to be statically linked to the libssh2lv library, but the build procedure for these two dependencies are not easily ported to the Eclipse build system. An alternative is to dynamically link the dependencies to the libssh2lv library, but this requires the libssh2.so and libcrypto.so libraries to be present and available in the cross-compiler distribution, which they are not. Thus, it is easier to configure a [CompactRIO (cRIO)](http://www.ni.com/en-us/shop/compactrio.html) running the NI Linux RT distribution as a [suitable build environment](https://gist.github.com/volks73/ff5bdf361c1dccd6005bfaa31ab80441) and build the shared object on the cRIO with the following steps:

1. Power on the cRIO.
2. Connect the cRIO to a host computer.
3. Download an archive file of the libssh2lv source code.
4. Transfer the libssh2lv source code archive to the cRIO. This can be done using FTP if installed or SCP.
5. Log into the cRIO via a SSH client
6. Navigate to the libssh2lv source code archive on the cRIO.
7. Execute the following commands to build the shared library, note the version numbers maybe different:

   ```bash
   $ unzip libssh2lv_0.1.0.zip
   $ cd libssh2lv_0.1.0
   $ mkdir build && cd build
   $ cmake -DBUILD_DEPS=OFF ..
   $ cmake --build .
   ```
   
The shared object (libssh2lv.so) will be available in the `bin` folder.

### [Documentation](https://fieldrndservices.github.io/libssh2lv/)

[Doxygen](http://www.doxygen.org) is used to build the Application Programming Interface (API) documentation. Ensure the latest version is installed then enter the following command from the root directory of the project to build the API docs:

    $ mkdir -p build/doc
    $ doxygen doc/Doxyfile

The output will be in the `build/doc/html` folder of the root directory of the project. If on Windows, the following commands can be used:

    C:\> mkdir build\doc
    C:\> doxygen doc\Doxyfile

The output can be viewed by opening the `build/doc/html/index.html` file in a web browser.

## Tests

All of the tests are located in the `tests` folder. The tests are organized in "modules", where an executable is created that tests each source "module". By default, the test are _not_ built. To build the tests, the `-DBUILD_TESTS=ON` flag must be added to the `cmake ..` build step. Each test executable is located in the `bin\tests` folder of the build directory and they can be run independently.

If the `ctest`, or `make test` on non-Windows systems, commands are used _after_ building the tests to run the tests, then the [ctest](https://cmake.org/Wiki/CMake/Testing_With_CTest) test runner framework is used to run the tests. This provides a very high level summary of the results of running all tests. Since the tests are organized into "modules" and suites, the [ctest](https://cmake.org/cmake/help/v3.9/manual/ctest.1.html) command only indicates that a test within a module and suite has failed. It does _not_ indicate which test has failed. To investigate the failed test, the executable in the `bin\tests` folder for test module should be run.

### Windows

Start a terminal command prompt and navigate to the root folder of the project. Note, if following from the [Build](#build) instructions, a command prompt should already be available at the root folder of the project. Enter the following commands to run the tests:

```text
> ctest -C "Debug"
```

### macOS

Start the Terminal.app. Note, if following from the [Build](#build) instructions, the Terminal.app has already been started and the present working directory (pwd) should already be the root folder of the project. Enter the following commands to run the tests:

    $ ctest

Or,

    $ make test

### Linux

Start a terminal. Note, if following from the [Build](#build) instructions, the terminal has already been started and the present working directory (pwd) should already be the root folder of the project. Enter the following commands to run the tests:

    $ ctest

Or,

    $ make test

## License

The libssh2lv project is licensed under the [BSD-3-Clause](https://opensource.org/licenses/BSD-3-Clause) license. The libssh2lv project statically compiles code from the [libssh2](https://www.libssh2.org) and [OpenSSL](https://www.openssl.org) projects, which are licensed under the [MIT](https://github.com/libssh2/libssh2/blob/master/COPYING) license and [Apache-2.0](https://github.com/openssl/openssl/blob/master/LICENSE) license, respectively. See the [LICENSE](https://github.com/fieldrndservices/lv-libssh2/blob/master/LICENSE) file for more information about licensing and copyright.
