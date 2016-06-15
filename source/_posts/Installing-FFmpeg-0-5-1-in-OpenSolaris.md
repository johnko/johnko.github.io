---
title: Installing FFmpeg 0.5.1 in OpenSolaris
date: 2010-04-12 08:17:02
tags: [ ffmpeg, media, opensolaris, video ]
alias: installing-ffmpeg-051-on-opensolaris/index.html
---

## Download the source

Download the file ffmpeg-0.5.1.tar.gz from http://ffmpeg.org/download.html

Save this in /src

You may also want to verify the file signature or checksums and read the README or INSTALL files.

## Extract, configure and make

```sh
cd /src
tar xzf ffmpeg-0.5.1.tar.gz && cd ffmpeg-0.5.1
CFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
  CPPFLAGS=$( find -L /my -type d -name include -exec echo "-I{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
  LDFLAGS=$( find -L /my -type d -name lib -exec echo "-L{} -R{} " \; | grep -v "[.][0-9]" | tr -d '\n' ) \
  ./configure --enable-gpl --enable-nonfree \
  --enable-pthreads --enable-x11grab --enable-libmp3lame \
  --enable-libtheora --enable-libvorbis --enable-libx264 \
  --enable-libxvid --enable-mlib \
  --extra-ldflags="-L/my/pth/lib -L/my/lame/lib \
  -L/my/x264/lib -L/my/xvidcore/lib" \
  --prefix=/my/ffmpeg-0.5.1 >>mylog.txt \
  && gmake all >>mylog.txt && su
```

`gmake install` kinda doesn't copy, so install manually:

```sh
mkdir -p /my/ffmpeg-0.5.1/include/libavdevice
mkdir -p /my/ffmpeg-0.5.1/include/libavformat
mkdir -p /my/ffmpeg-0.5.1/include/libavcodec
mkdir -p /my/ffmpeg-0.5.1/include/libavutil
mkdir -p /my/ffmpeg-0.5.1/lib/pkgconfig
mkdir -p /my/ffmpeg-0.5.1/lib/vhook
mkdir -p /my/ffmpeg-0.5.1/bin
mkdir -p /my/ffmpeg-0.5.1/share/ffmpeg
cp */*.a /my/ffmpeg-0.5.1/lib/
cp libavdevice/*.h /my/ffmpeg-0.5.1/include/libavdevice/
cp */*.pc /my/ffmpeg-0.5.1/lib/pkgconfig/
cp libavformat/*.h /my/ffmpeg-0.5.1/include/libavformat/
cp libavcodec/*.h /my/ffmpeg-0.5.1/include/libavcodec/
cp libavutil/*.h /my/ffmpeg-0.5.1/include/libavutil/
cp vhook/*.so /my/ffmpeg-0.5.1/lib/vhook/
cp ffmpeg /my/ffmpeg-0.5.1/bin/
cp ffplay /my/ffmpeg-0.5.1/bin/
cp ffserver /my/ffmpeg-0.5.1/bin/
cp ffpresets/* /my/ffmpeg-0.5.1/share/ffmpeg
```

Edit Makefile, replace "install -c" with "install":

```sh
vi Makefile
```

`gmake install` should now run with no errors:

```
gmake install >>mylog.txt
ln -s /my/ffmpeg-0.5.1 /my/ffmpeg
exit
echo "export PATH=/my/ffmpeg/bin:\$PATH" >> ~/.profile
```

Congrats! FFmpeg should be built successfully!
