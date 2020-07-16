#!/bin/bash

NUM_CORES=4
ARCH=`dpkg --print-architecture`
CONFIGURE_FLAGS=""

if [ ${ARCH} == 'amd64' ]; then
LINUXDEPLOY=linuxdeploy-x86_64.AppImage
elif [ ${ARCH} == 'i386' ]; then
LINUXDEPLOY=linuxdeploy-i386.AppImage
fi

set -e

if [ ! -f linuxdeploy*.AppImage ]; then
	echo "fetch linuxdeploy script"
	wget https://github.com/linuxdeploy/linuxdeploy/releases/download/continuous/"$LINUXDEPLOY" > /dev/null 2>&1

	echo "make linuxdeploy executable"
	chmod +x "$LINUXDEPLOY"
else
	echo "linuxdeploy already fetched; skipping"
fi

if [ -d RetroArch ]; then
	echo "RetroArch repo already fetched; cleaning the source tree"
	cd RetroArch && make clean
else
	git clone https://github.com/libretro/RetroArch.git && cd RetroArch
fi

if [ ! -d AppDir ]; then
	mkdir -p AppDir
else
	echo "Cleaning up old AppDir" && rm -rf AppDir && mkdir -p AppDir
fi

echo "building RetroArch with these flags: $CONFIGURE_FLAGS" && echo "This will take a minute..." && ./configure $CONFIGURE_FLAGS --prefix=/usr > /dev/null 2>&1 && make -j"$NUM_CORES" > /dev/null 2>&1 && echo "RetroArch built successfully"

make install DESTDIR=AppDir prefix=/usr > /dev/null 2>&1 && echo "RetroArch installed to AppDir successfully"

echo "bundling RetroArch AppImage" && ../"$LINUXDEPLOY" --appdir AppDir --output appimage > /dev/null 2>&1 && echo "RetroArch AppImage created successfully" && mv RetroArch*.AppImage ../ && cd ..

echo "All done!" && exit 0
