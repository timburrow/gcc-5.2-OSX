# GCC 5.2 for OS X #

This is a tar file of gcc-5.2 (gnu compiler collection) with C, C++ and gfortran compiled for OS X and which can be extracted into /usr/local/. The latest tarball can be found on the [Releases Page](https://github.com/timburrow/gcc-5.2-OSX/releases).

Tested on OS X 10.11 "El Capitan", OS X 10.10 "Yosemite", and OS X 10.9 "Mavericks".

## How to install ##

Extract into /usr/local. Note that this will overwrite any gcc you have already installed in /usr/local. Then add the path to /usr/local/bin, if not already set

```
sudo tar xf gcc5.2-osx-usrlocal.tar.bz2 -C /
export PATH=/usr/local/bin:${PATH}
which gcc
# should be /usr/local/bin/gcc
gcc -v
# should return the following:
Using built-in specs.
COLLECT_GCC=/usr/local/bin/gcc
COLLECT_LTO_WRAPPER=/usr/local/libexec/gcc/x86_64-apple-darwin15.0.0/5.2.0/lto-wrapper
Target: x86_64-apple-darwin15.0.0
Configured with: ../gcc-5.2.0/configure --prefix=/usr/local --enable-languages=c,c++,fortran --with-system-zlib --with-libiconv-prefix=/usr/local --with-native-system-header-dir=/usr/include -with-sysroot=/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.11.sdk
Thread model: posix
gcc version 5.2.0 (GCC) 
```

## License ##
 
<a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-sa/4.0/88x31.png" /></a><br />This README is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-sa/4.0/">Creative Commons Attribution-ShareAlike 4.0 International License</a>.

## Testing ##

Use the repository scons-test https://github.com/timburrow/scons-test to test that C and FORTRAN will compile and link. 

### Install scons ###

Install SConstruct (tar.gz archive) from http://scons.org/download.php (as of writing, the latest version is scons-2.4.0.tar.gz).

Install:

```
tar xf scons-2.4.0.tar.gz
cd scons-2.4.0
sudo python setup.py install
```

### Run the test ###

Run the test and check that the .c and .f files compile and link.

```
cd scons-test
scons
./build/test
```

The outout should look like

```
The cosine of 60.000000 degrees is 0.500000 
 The sum of the numbers you entered are:    4.00000000    
 And the square of the sum is:   16.0000000    
Compiled using (gcc) 5.2.0 on Sat Oct 10 11:17:01 2015
```

## Troubleshooting ##

If <stdio.h> or <stdlib.h> cannot be found, then the system headers cannot be found. This gcc assumes headers are in /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.11.sdk

Install Xcode, if you haven't already. If you are running a older version of OS X or Xcode, then the SDK may be different. Find out the SDKs installed and set the path to the SDK, like this:

```
export CPPFLAGS='-isysroot /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.XX.sdk'
gcc -c -o test.o test.c $CPPFLAGS
```
where XX is replaced with the correct SDK.

To check the search path for includes, use the following command

```
`gcc -print-prog-name=cc1plus` -v
```

If the FORTRAN object file does not link, at the link stage use:

```
gcc -o test test.o output.o -L/usr/local/lib -lgfortran $CPPFLAGS
```

If libSystem.dylib is not found with an error: "ld: library not found for -lSystem" then at the link stage add:

```
-L/usr/lib
```

# How this archive was made #

This archive was built on Mac OS X 10.11 "El Capitan" using Xcode 7.0. Follow the instructions below to make the archive. On my system, /usr/local was empty prior to starting so I could make the archive cleanly.

## Xcode 7.0 ##

Xcode 7.0 is free from Apple, through the Mac App Store, go to https://itunes.apple.com/app/xcode/id497799835

You need to build chain from Xcode to bootstrap the GCC compiler.

## libiconv (1.14) ##

Download configure libiconv with:

```
curl -sO http://gnu.mirror.iweb.com/libiconv/libiconv-1.14.tar.gz
tar xf libiconv-1.14.tar.gz
mkdir build-libiconv && cd build-libiconv
../libiconv-1.14/configure --without-libintl-prefix --prefix=/usr/local --enable-static --disable-shared
make
sudo make install
```

## Get the gnu compiler collection source ##

Get the gcc source and expand. We'll move the prerequisites into the source tree later.

```
curl -Os http://gnu.mirror.iweb.com/gcc/gcc-5.2.0/gcc-5.2.0.tar.bz2
tar xf gcc-5.2.0.tar.bz2
```

## More prerequisites ##

Get the prerequisite libraries from the GNU mirrors, then expand and move them into the gcc source tree.

```
curl -sO http://gnu.mirror.iweb.com/gmp/gmp-6.0.0a.tar.bz2
curl -sO http://gnu.mirror.iweb.com/mpfr/mpfr-3.1.3.tar.bz2
curl -sO http://gnu.mirror.iweb.com/mpc/mpc-1.0.3.tar.gz
curl -sO ftp://gcc.gnu.org/pub/gcc/infrastructure/isl-0.14.tar.bz2
curl -sO http://www.bastoul.net/cloog/pages/download/cloog-0.18.1.tar.gz
tar xf gmp-6.0.0a.tar.bz2 && mv gmp-6.0.0a gcc-5.2.0/gmp
tar xf mpfr-3.1.3.tar.bz2 && mv mpfr-3.1.3 gcc-5.2.0/mpfr
tar xf mpc-1.0.3.tar.gz && mv mpc-1.0.3 gcc-5.2.0/mpc
tar xf isl-0.14.tar.bz2 && mv isl-0.14 gcc-5.2.0/isl
tar xf cloog-0.18.1.tar.gz && mv cloog-0.18.1 gcc-5.2.0/cloog
```

## Configure, make and install GCC ##

In its own build directory, we'll configure gcc to build C, C++ and FORTRAN. Add more languages if you need.

```
mkdir gcc-build && cd gcc-build
../gcc-5.2.0/configure --prefix=/usr/local --enable-languages=c,c++,fortran --with-system-zlib --with-libiconv-prefix=/usr/local --with-native-system-header-dir=/usr/include -with-sysroot=/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.11.sdk
make -j8
sudo make install
```

## Create tar file ##

Since the /usr/local directory was empty whenI started, only the gcc files are present. The leading / is dropped by tar, so to expand either by changing directory to / or use -C / when expanding.

```
tar jcf gcc5.2-osx-usrlocal.tar.bz2 /usr/local
```

