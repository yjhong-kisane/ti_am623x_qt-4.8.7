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
export LD_LIBRATY_PATH=$QTDIR/lib:$QTDIR/lib/fonts:$LD_LIBRARY_PATH

```


## 빌드 절차 (aarch64)
```bash
./configure -prefix /opt/qt4.8.7 -release -embedded aarch64 -xplatform qws/linux-arm-gnueabi-g++ -opensource -no-largefile -little-endian -exceptions -qt-freetype -no-qt3support -qt-mouse-linuxtp -qt-mouse-tslib -qt-gfx-transformed -qt-gfx-vnc -qt-gfx-linuxfb -qt-gfx-multiscreen -no-nis -no-cups -depths 16,24,32 -no-mmx -no-3dnow -no-sse -no-sse2 -no-glib -no-accessibility -plugin-sql-sqlite3 -no-openssl -no-gtkstyle -fast -no-pch
```

```bash
make -j4
sudo make install -j4
```

