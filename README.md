# GCC 5.2 for OS X #

Build started with gcc 5.2 (installed originally in /usr/local/gcc5.2)

## libiconv (1.14) ##

configure with 
```
../libiconv-1.14/configure --without-libintl-prefix --prefix=/usr/local --enable-static --disable-shared
```
confgire gcc with
```
./gcc-5.2.0/configure --prefix=/usr/local --enable-languages=c,c++,fortran --with-system-zlib --with-libiconv-prefix=/usr/local --with-native-system-header-dir=/usr/include -with-sysroot=/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.10.sdk
```
