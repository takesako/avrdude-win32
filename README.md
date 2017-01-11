# avrdude-win32

avrdude for Win32 binary package

https://github.com/takesako/avrdude-win32

# How to build for avrdude-win32
## # Install MinGW
```
https://sourceforge.net/projects/mingw/files/Installer/mingw-get-setup.exe
```
## # mingw-get install
```
mingw-get install msys-base
mingw-get install msys-zip msys-unzip
mingw-get install msys-wget
mingw-get install mingw-developer-toolkit
mingw-get install mingw32-base
```
## # wget wget
```
mkdir -p /mingw/src
cd /mingw/src
wget -nc --no-check-certificate https://eternallybored.org/misc/wget/current/wget.exe
```
## # --disable-shared=libpthread
```
mv /mingw/lib/libpthread.dll.a /mingw/src
```
## # build libelf-0.8.13
```
cd /mingw/src
wget -nc http://www.mr511.de/software/libelf-0.8.13.tar.gz
tar xvf libelf-0.8.13.tar.gz
cd libelf-0.8.13
./configure --prefix=/mingw --disable-shared --disable-nls
make
make install
cd ..
```
## # build confuse-3.0
```
cd /mingw/src
wget -nc -c --no-check-certificate https://github.com/martinh/libconfuse/releases/download/v3.0/confuse-3.0.tar.gz
tar xvf confuse-3.0.tar.gz
cd confuse-3.0
./configure --prefix=/mingw --disable-shared --disable-nls --disable-examples
make CFLAGS="-DLC_MESSAGES=LC_ALL"
make install
cd ..
```
## # download libftdi_0.20_devkit_mingw32
```
cd /mingw/src
wget -nc http://downloads.sourceforge.net/project/picusb/libftdi_0.20_devkit_mingw32_08April2012.zip
unzip libftdi_0.20_devkit_mingw32_08April2012.zip
mv libftdi_0.20_devkit_mingw32_08April2012/lib/libftdi.dll.a /mingw/src
cp -p libftdi_0.20_devkit_mingw32_08April2012/bin/libusb0.dll /mingw/src
```
## # build avrdude-6.3 with patch
```
cd /mingw/src
wget -nc -c --no-check-certificate https://github.com/takesako/alpine-iot/raw/master/avrdude/avrdude-6.3-ftdi232.patch
wget -nc -c --no-check-certificate https://github.com/takesako/alpine-iot/raw/master/avrdude/avrdude-6.3-libusb_exit.patch
wget -nc http://download.savannah.gnu.org/releases/avrdude/avrdude-6.3.tar.gz
tar xvf avrdude-6.3.tar.gz
cd avrdude-6.3
patch -p1 < ../avrdude-6.3-ftdi232.patch
patch -p1 < ../avrdude-6.3-libusb_exit.patch
env CFLAGS="-I/mingw/src/libftdi_0.20_devkit_mingw32_08April2012/include" \
LDFLAGS="-static -L/mingw/src/libftdi_0.20_devkit_mingw32_08April2012/lib" \
./configure --prefix=/avrdude --sysconfdir=/avrdude/bin \
--disable-doc --enable-versioned-doc=no
make
make install
cd ..
```
### # make avrdude-win32-6.3-r2.zip
```
cd /mingw/src
wget -N -c --no-check-certificate https://github.com/takesako/avrdude-win32/raw/master/README.md
cp -p README.md /avrdude/bin
cd /avrdude/bin
strip avrdude.exe
cp -p /mingw/src/libftdi_0.20_devkit_mingw32_08April2012/bin/libusb0.dll .
unix2dos avrdude.conf
objdump -p avrdude.exe | grep "DLL Name"
zip -9 /mingw/src/avrdude-win32-6.3-r2.zip avrdude.exe avrdude.conf libusb0.dll README.md
```
