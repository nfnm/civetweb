Building Civetweb
=========

This guide covers the build instructions for stand-alone web server.
See [Embedding.md](https://github.com/bel2125/civetweb/blob/master/docs/Embedding.md) for information on extending an application.

#### Where to get the source code?
https://github.com/bel2125/civetweb


Building for Windows
---------

#### Using Visual Studio
Open the *VS2012/civetweb.sln* in Visual Studio.
To include SSL support, you may have to use yaSSL.  However, it is GPL licensed.
See [yaSSL.md](https://github.com/bel2125/civetweb/blob/master/docs/yaSSL.md) for more information.

#### Using MinGW-w64 or TDM-GCC
In the start menu locate and run the "Run terminal" batch file. For TDM-GCC this is named "MinGW Command Prompt".
Navigate to the civetweb sources directory and run:
```
mingw32-make CC=gcc
```

Building for Linux, BSD, and OSX
---------

## Using Make

```
make help
```
Get help

```
make build
```
compile the code

```
make install
```
Install on the system, Linux only.

```
make lib WITH_CPP=1 WITH_IPV6=1
make clean slib WITH_CPP=1 WITH_LUA=1 WITH_WEBSOCKET=1
```
Build the static and shared libraries.
The *WITH_CPP* make option is to include the CivetServer class.
The additional make options configure the library just as it would the application.

The *slib* option should be done on a separate clean build as position
independent code (PIC) is required for it.  Trying to run it after
building the static library or the server will result in a link error.

```
make clean
```
Clean up the mess

## Setting build options

Make options can be set on the command line with the make command like so.
```
make build WITH_LUA=1
```


| Make Options              | Description                              |
| ------------------------- | ---------------------------------------- |
| WITH_LUA=1                | build with Lua support                   |
| WITH_DEBUG=1              | build with GDB debug support             |
| WITH_IPV6=1               | with IPV6 support                        |
| WITH_WEBSOCKET=1          | build with web socket support            |
| WITH_CPP=1                | build libraries with c++ classes         |
| CONFIG_FILE=file          | use 'file' as the config file            |
| CONFIG_FILE2=file         | use 'file' as the backup config file     |
| HTMLDIR=/path             | place to install initial web pages       |
| DOCUMENT_ROOT=/path       | HTMLDIR override, config option, install |
|                           | nothing is installed here.               |
| PORTS=8080                | listening ports override when installing |
| SSL_LIB=libssl.so.0       | use versioned SSL library                |
| CRYPTO_LIB=libcrypto.so.0 | system versioned CRYPTO library          |
| PREFIX=/usr/local         | sets the install directory               |
| COPT='-DNO_SSL'           | method to insert compile flags           |

Note that the WITH_* options used for *make* are not identical to the
preprocessor defines in the source code - usually USE_* is used there.

## Setting compile flags

Compile flags can be set using the *COPT* make option like so.
```
make build COPT="-DNDEBUG -DNO_CGI"
```

| Compile Flags             | Description                          |
| ------------------------- | ------------------------------------ |
| NDEBUG                    | strip off all debug code             |
| DEBUG                     | build debug version (very noisy)     |
| NO_CGI                    | disable CGI support                  |
| NO_SSL                    | disable SSL functionality            |
| NO_SSL_DL                 | link against system libssl library   |
| NO_FILES                  | do not serve files from a directory  |
| SQLITE_DISABLE_LFS        | disables large files (Lua only)      |
| SSL_ALREADY_INITIALIZED   | do not initialize libcrypto          |

## Cross Compiling

Take total control with *CC*, *COPT* and *TARGET_OS* as make options.
TARGET_OS is used to determine some compile details as will as code function.
TARGET_OS values should be be one found in *resources/Makefile.in-os*.

```
make CC=arm-none-linux-gnueabi-gcc COPT="-march=armv7-a  -mfpu=vfp -mfloat-abi=softfp" TARGET_OS=FROG
```

## Cocoa DMG Packaging (OSX Only)

Use the alternate *Makefile.osx* to do the build.  The entire build has
to be done using *Makefile.osx* because additional compile and link options
are required.  This Makefile has all the same options as the other one plus
one additional *package* rule.

```
make -f Makefile.osx package
```

Building with Buildroot
---------

[Buildroot](http://buildroot.uclibc.org/) is a tool for creating cross compiled file systems.  Including Civetweb in buildroot is fairly easy.  There is even support for various build options.

1. First, check if it already there.
  - In buildroot, make menuconfig
     - Package Selection for the target --->
     - Networking applications  --->
     - civetweb
2. If not there, just add it
  - copy *Config.in* and *civetweb.mk* from Civetweb's *contrib/buildroot/* to Buildroot's *package/civetweb/* directory.
  - In Buildroot's *package/Config.in, insert the following line in were you will know how to find it in the menu.
    > ``` source "package/civetweb/Config.in" ```


Building on Android
---------

This is a small guide to help you run civetweb on Android. Currently it is
tested on the HTC Wildfire. If you have managed to run it on other devices
as well, please comment or drop an email in the mailing list.
Note : You dont need root access to run civetweb on Android.

- Download the source from the Downloads page.
- Download the Android NDK from [http://developer.android.com/tools/sdk/ndk/index.html](http://developer.android.com/tools/sdk/ndk/index.html)
- Run `/path-to-ndk/ndk-build -C /path-to-civetweb/resources`
  That should generate civetweb/lib/armeabi/civetweb
- Using the adb tool (you need to have Android SDK installed for that),
  push the generated civetweb binary to `/data/local` folder on device.
- From adb shell, navigate to `/data/local` and execute `./civetweb`.
- To test if the server is running fine, visit your web-browser and
  navigate to `http://127.0.0.1:8080` You should see the `Index of /` page.

![screenshot](https://a248.e.akamai.net/camo.github.com/b88428bf009a2b6141000937ab684e04cc8586af/687474703a2f2f692e696d6775722e636f6d2f62676f6b702e706e67)


Notes:

- `jni` stands for Java Native Interface. Read up on Android NDK if you want
  to know how to interact with the native C functions of civetweb in Android
  Java applications.
- TODO: A Java application that interacts with the native binary or a
  shared library.


