# ti_am623x_qt-4.8.7


## 64비트 아키택처 지원 패치 적용 (aarch64)

(참고) https://bugreports.qt.io/browse/QTBUG-35442

```bash
$ patch -p1 < qt4-aarch64.patch
```


## ~/mkspecs/qws/linux-arm-gnueabi-g++/qmake.conf 수정
```bash
#
# qmake configuration for building with arm-none-linux-gnueabi-g++
#

include(../../common/linux.conf)
include(../../common/gcc-base-unix.conf)
include(../../common/g++-unix.conf)
include(../../common/qws.conf)

# modifications to g++.conf
QMAKE_CC                = /home/yjhong/kisan/TI_AM6231/toolchain/gcc-arm-9.2-2019.12-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-gcc
QMAKE_CXX               = /home/yjhong/kisan/TI_AM6231/toolchain/gcc-arm-9.2-2019.12-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-g++
QMAKE_LINK              = /home/yjhong/kisan/TI_AM6231/toolchain/gcc-arm-9.2-2019.12-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-g++
QMAKE_LINK_SHLIB        = /home/yjhong/kisan/TI_AM6231/toolchain/gcc-arm-9.2-2019.12-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-g++

# modifications to linux.conf
QMAKE_AR                = /home/yjhong/kisan/TI_AM6231/toolchain/gcc-arm-9.2-2019.12-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-ar cqs
QMAKE_OBJCOPY           = /home/yjhong/kisan/TI_AM6231/toolchain/gcc-arm-9.2-2019.12-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-objcopy
QMAKE_STRIP             = /home/yjhong/kisan/TI_AM6231/toolchain/gcc-arm-9.2-2019.12-x86_64-aarch64-none-linux-gnu/bin/aarch64-none-linux-gnu-strip


QMAKE_INCDIR = /opt/tslib/include
QMAKE_LIBDIR = /opt/tslib/lib

QMAKE_LFLAGS += -Wl,-rpath-link=/opt/tslib/lib
QMAKE_CXXFLAGS += $$QMAKE_CFLAGS -std=gnu++98


load(qt_config)
```

## 루트파일시스템 환경변수 추가 (/etc/profile)
```bash
export TSLIBDIR=/opt/tslib
export TSLIB_CONFFILE=/etc/ts.conf
export TSLIB_PLUGINDIR=/opt/tslib/lib/ts
export TSLIB_TSDEVICE=/dev/input/event1
export TSLIB_FBDEVICE=/dev/fb0
export TSLIB_CONSOLEDEVICE=none
export TSLIB_CALIBFILE=/etc/pointercal
export LD_LIBRARY_PATH=$TSLIBDIR:$TSLIBDIR/lib:$TSLIBDIR/lib/ts:$LD_LIBRARY_PATH

export QTDIR=/opt/qtlib
export QTLIB=$QTDIR/lib
export QTINC=$QTDIR/include
export PATH=$QTDIR/bin:$PATH
export QMAKESPEC=$QTDIR/mkspecs/linux-arm-gnueabi-g++
export QT_QWS_FONTDIR=$QTDIR/lib/fonts
export QT_PLUGIN_PATH=$QTDIR/plugins
export QWS_MOUSE_PROTO="Tslib:${TSLIB_TSDEVICE}"
#export QWS_MOUSE_PROTO="Tslib:${TSLIB_TSDEVICE} Auto"
#export QT_QPA_GENERIC_PLUGINS=$QWS_MOUSE_PROTO
export QT_QPA_PLATFORM=LinuxFB
#export POINTERCAL_FILE="/etc/pointercal"
export LD_LIBRARY_PATH=$QTDIR/lib:$QTDIR/lib/fonts:$LD_LIBRARY_PATH

```


## 빌드 절차 (aarch64)
```bash
./configure -prefix /opt/qt4.8.7 -release -embedded aarch64 -xplatform qws/linux-arm-gnueabi-g++ -opensource -no-largefile -little-endian -exceptions -qt-freetype -no-qt3support -qt-mouse-linuxtp -qt-mouse-tslib -qt-gfx-transformed -qt-gfx-vnc -qt-gfx-linuxfb -qt-gfx-multiscreen -no-nis -no-cups -depths 16,24,32 -no-mmx -no-3dnow -no-sse -no-sse2 -no-glib -no-accessibility -plugin-sql-sqlite3 -no-openssl -no-gtkstyle -fast -no-pch
```

(참고) make 에 약 20분 소요

```bash
make -j4
sudo make install
```


## 추가 폰트 복사 (Qt_Fonts.tar.gz)
```bash
$ tar -xvzf Qt_Fonts.tar.gz -C /opt/qt-4.8.7/lib/fonts
```


## configure 결과
```bash
Building on:   qws/linux-x86_64-g++
Building for:  qws/linux-arm-gnueabi-g++
Architecture:  aarch64
Host architecture: x86_64

Build .................. libs examples demos docs translations
Configuration ..........  cross_compile release embedded stl  minimal-config small-config medium-config large-config full-config no-pkg-config shared embedded reduce_exports ipv6 clock-gettime clock-monotonic mremap getaddrinfo ipv6ifname getifaddrs inotify png freetype zlib concurrent multimedia audio-backend svg script scripttools declarative release
Debug .................. no
Qt 3 compatibility ..... no
QtDBus module .......... no
QtConcurrent code ...... yes
QtGui module ........... yes
QtScript module ........ yes
QtScriptTools module ... yes
QtXmlPatterns module ... no
Phonon module .......... no
Multimedia module ...... auto
SVG module ............. yes
WebKit module .......... no
JavaScriptCore JIT ..... To be decided by JavaScriptCore
Declarative module ..... yes
Declarative debugging ...yes
Support for S60 ........ no
Symbian DEF files ...... no
STL support ............ yes
PCH support ............ no
MMX/3DNOW/SSE/SSE2/SSE3. no/no/no/no/no
SSSE3/SSE4.1/SSE4.2..... no/no/no
AVX..................... no
IPv6 support ........... yes
IPv6 ifname support .... yes
getaddrinfo support .... yes
getifaddrs support ..... yes
Accessibility .......... no
NIS support ............ no
CUPS support ........... no
Iconv support .......... no
Glib support ........... no
GStreamer support ...... no
PulseAudio support ..... no
Large File support ..... no
GIF support ............ plugin
TIFF support ........... plugin (qt)
JPEG support ........... plugin (qt)
PNG support ............ yes (qt)
MNG support ............ plugin (qt)
zlib support ........... yes
Session management ..... no
Embedded support ....... aarch64
Freetype2 support ...... yes
Graphics (qt) .......... linuxfb multiscreen transformed vnc linuxfb multiscreen
Graphics (plugin) ...... 
Decorations (qt) ....... styled windows default
Decorations (plugin) ... 
Keyboard driver (qt) ... tty
Keyboard driver (plugin) .. 
Mouse driver (qt) ...... pc linuxtp linuxtp tslib
Mouse driver (plugin) .. 
OpenGL support ......... no
OpenVG support ......... no
SQLite support ......... plugin (qt)
OpenSSL support ........ no 
Alsa support ........... no
ICD support ............ no
libICU support ......... no
Use system proxies ..... no
```

