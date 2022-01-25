# Build from source

The curl project creates source code that can be built to produce the two
products curl and libcurl. The conversion from source code to binaries
is often referred to as "building". You build curl and libcurl from source.

The curl project does not provide any built binaries at all—it only ships the
source code. The binaries which can be found on the download page of the curl
web and installed from other places on the Internet are all built and provided
to the world by other friendly people and organizations.

The source code consists of a large number of files containing C
code. Generally speaking, the same set of files are used to build binaries for
all platforms and computer architectures that curl supports. curl can be built
and run on a vast number of platforms. If you use a rare operating system
yourself, chances are that building curl from source is the easiest or perhaps
the only way to get curl.

Making it easy to build curl is a priority to the curl project, although we do
not always necessarily succeed!

## git vs tarballs

When release tarballs are created, a few files are generated and included in
the final release bundle. Those generated files are not present in the git
repository, because they are generated and there is no need to
store them in git.

So, if you want to build curl from git you need to generate some of those
files yourself before you can build. On Linux and Unix-like systems, do this by
running `./buildconf` and on Windows, run `buildconf.bat`.

## On Linux and Unix-like systems

There are two distinctly different ways to build curl on Linux and other
Unix-like systems; there is the one using the configure script and there is the
CMake approach.

There are two different build environments to cater to people's different
opinions and tastes. The configure-based build is arguably the more mature and
more encompassing build system and should probably be considered the default
one.

## Autotools

The "Autotools" are a collection of different tools that used together generate
the `configure` script. The configure script is run by the user who wants to
build curl and it does a whole bunch of things:

 - It checks for features and functions present in your system.

 - It offers command-line options so that you as a builder can decide what to
   enable and disable in the build. Features and protocols, etc., can be
   toggled on/off, even compiler warning levels and more.

 - It offers command-line options to let the builder point to specific
   installation paths for various third-party dependencies that curl can be
   built to use.

 - It specifies on which file path the generated installation should be placed
   when ultimately the build is made and "make install" is invoked.

In the most basic usage, just running `./configure` in the source directory is
enough. When the script completes, it outputs a summary of what options it has
detected/enabled and what features that are still disabled, some of which
possibly because it failed to detect the presence of necessary third-party
dependencies that are needed for those functions to work. If the summary is
not what you expected it to be, invoke configure again with new options or
with the previously used options adjusted.

After configure has completed, you invoke `make` to build the entire thing and
then finally `make install` to install curl, libcurl and associated
things. `make install` requires that you have the correct rights in your
system to create and write files in the installation directory or you will get
some errors.

### Cross-compiling

Cross-compiling means that you build the source on one architecture but the
output is created to be run on a different one. For example, you could build
the source on a Linux machine but have the output work on a Windows machine.

For cross-compiling to work, you need a dedicated compiler and build system
setup for the particular target system for which you want to build. How to get
and install that system is not covered in this book.

Once you have a cross compiler, you can instruct configure to use that
compiler instead of the "native" compiler when it builds curl so that the end
result then can be moved over and used on the other machine.

### Static linking

By default, configure will setup the build files so that the following 'make'
command will create both shared and static versions of libcurl. You can change
that with the `--disable-static` or `--disable-shared` options to configure.

If you instead want to build with static versions of third party libraries
instead of shared libraries, you need to prepare yourself for an uphill
battle. curl's configure script is focused on setting up and building with
shared libraries.

One of the differences between linking with a static library compared to
linking with a shared one is in how shared libraries handle their own
dependencies while static ones do not. In order to link with library xyz as a
shared library, it is as basically a matter of adding `-lxyz` to the linker
command line no matter which other libraries xyz itself was built to use. But,
if that xyz is instead a static library we also need to specify each of xyz's
dependencies on the linker command line. curl's configure typically cannot
keep up with or know all possible dependencies for all the libraries it can be
made to build with, so users wanting to build with static libs mostly need to
provide that list of libraries to link with.

### Select TLS back-end

The configure-based build offers the user to select from a wide variety of
different TLS libraries when building. You select them by using the correct
command line options. Before curl 7.77.0, the configure script would
automatically check for OpenSSL, but in modern versions it does not.

 - BearSSL: `--with-bearssl`
 - BoringSSL: `--with-openssl`
 - GnuTLS: `--with-gnutls`
 - libressl: `--with-openssl`
 - mbedTLS: `--with-mbedtls`
 - MesaLink: `--with-mesalink`
 - NSS: `--with-nss`
 - OpenSSL: `--with-openssl`
 - Rustls: `--with-rustls` (point to the rustls-ffi install path)
 - schannel: `--with-schannel`
 - secure transport: `--with-secure-transport`
 - Wolfssl: `--with-wolfssl`

If you do not specify which TLS library to use, the configure script will
fail. If you want to build *without* TLS support, you must explicitly ask for
that with `--without-ssl`.

These `--with-*` options also allow you to provide the install prefix so that
configure will search for the specific library where you tell it to. Like
this:

    ./configure --with-gnutls=/home/user/custom-gnutls

You can opt to build with support for **multiple** TLS libraries by specifying
multiple `--with-*` options on the configure command line. Pick which one to
make the default TLS back-end with `--with-default-ssl-backend=[NAME]`. For
example, build with support for both GnuTLS and OpenSSL and default to
OpenSSL:

    ./configure --with-openssl --with-gnutls --with-default-ssl-backend=openssl

### Select SSH back-end

The configure-based build offers the user to select from a variety of
different SSH libraries when building. You select them by using the
correct command-line options.

 - libssh2: `--with-libssh2`
 - libssh: `--with-libssh`
 - wolfSSH: `--with-wolfssh`

These `--with-*` options also allow you to provide the install prefix so that
configure will search for the specific library where you tell it to. Like
this:

    ./configure --with-libssh2=/home/user/custom-libssh2

### Select HTTP/3 back-end

The configure-based build offers the user to select different HTTP/3 libraries
when building. You select them by using the correct command-line options.

 - quiche: `--with-quiche`
 - ngtcp2: `--with-ngtcp2 --with-nghttp3`

## CMake

CMake is an alternative build method that works on most modern platforms,
including Windows. Using this method you first need to have cmake installed on
your build machine, invoke cmake to generate the build files and then
build. With cmake's `-G` flag, you select which build system to generate files
for. See `cmake --help` for the list of "generators" your cmake installation
supports.

On the cmake command line, the first argument specifies where to find the cmake
source files, which is `.` (a single dot) if in the same directory.

To build on Linux using plain make with CMakeLists.txt in the same directory,
you can do:

    cmake -G "Unix Makefiles" .
    make

Or rely on the fact that unix makefiles are the default there:

    cmake .
    make

To create a subdirectory for the build and run make in there:

    mkdir build
    cd build
    cmake ..
    make

## On Windows

You can build curl on Windows in several different ways. We recommend using
the MSVC compiler from Microsoft or the free and open source mingw compiler. The
build process is, however, not limited to these.

### nmake

Build with MSVC using the nmake utility like this:

    cd winbuild

Decide what options to enable/disable in your build. The `README.md`
file details them all, but an example command line could look like this (split
into several lines for readability):

    nmake WITH_SSL=dll WITH_NGHTTP2=dll ENABLE_IPV6=yes \
    WITH_ZLIB=dll MACHINE=x64 
