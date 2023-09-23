Title: GoPro Max Reframing on Linux
Date: 2022-03-18
Author: Jacob Morgan
Category: Code
Tags: GoPro, FFmpeg, Davinci Resolve
Slug: gopro-ffmpeg-conversion
Status: draft


I purchased a GoPro Max for some snowboarding trips this year. I had done some research comparing One360 vs GoPro Max but largely it was availability in Sweden that led me to GoPro Max. I just assumed one way or another a way to use/work/edit the footage be possible on Linux. Wrong. 


The GoPro Max creates .360 files when recording in the 360 degree mode. Within this .360 file there is two seperate 4096x1344 video streams, their audio tracks, and other metadata tracks(gps, etc).

    :::bash
    ffmpeg version n5.0 Copyright (c) 2000-2022 the FFmpeg developers
      built with gcc 11.2.0 (GCC)
      configuration: --prefix=/usr --disable-debug --disable-static --disable-stripping --enable-amf --enable-avisynth --enable-cuda-llvm --enable-lto --enable-fontconfig --enable-gmp --enable-gnutls --enable-gpl --enable-ladspa --enable-libaom --enable-libass --enable-libbluray --enable-libdav1d --enable-libdrm --enable-libfreetype --enable-libfribidi --enable-libgsm --enable-libiec61883 --enable-libjack --enable-libmfx --enable-libmodplug --enable-libmp3lame --enable-libopencore_amrnb --enable-libopencore_amrwb --enable-libopenjpeg --enable-libopus --enable-libpulse --enable-librav1e --enable-librsvg --enable-libsoxr --enable-libspeex --enable-libsrt --enable-libssh --enable-libsvtav1 --enable-libtheora --enable-libv4l2 --enable-libvidstab --enable-libvmaf --enable-libvorbis --enable-libvpx --enable-libwebp --enable-libx264 --enable-libx265 --enable-libxcb --enable-libxml2 --enable-libxvid --enable-libzimg --enable-nvdec --enable-nvenc --enable-shared --enable-version3
      libavutil      57. 17.100 / 57. 17.100
      libavcodec     59. 18.100 / 59. 18.100
      libavformat    59. 16.100 / 59. 16.100
      libavdevice    59.  4.100 / 59.  4.100
      libavfilter     8. 24.100 /  8. 24.100
      libswscale      6.  4.100 /  6.  4.100
      libswresample   4.  3.100 /  4.  3.100
      libpostproc    56.  3.100 / 56.  3.100
    Guessed Channel Layout for Input Stream #0.6 : 4.0
    Input #0, mov,mp4,m4a,3gp,3g2,mj2, from 'GS010026.360':
      Metadata:
        major_brand     : mp41
        minor_version   : 538120216
        compatible_brands: mp41
        creation_time   : 2022-02-10T11:53:24.000000Z
        firmware        : H19.03.02.00.00
      Duration: 00:00:36.50, start: 0.000000, bitrate: 66171 kb/s
      Stream #0:0[0x1](eng): Video: hevc (Main) (hvc1 / 0x31637668), yuvj420p(pc, bt709), 4096x1344 [SAR 1:1 DAR 64:21], 29838 kb/s, 29.97 fps, 29.97 tbr, 90k tbn (default)
        Metadata:
          creation_time   : 2022-02-10T11:53:24.000000Z
          handler_name    : GoPro H.265
          vendor_id       : [0][0][0][0]
          encoder         : GoPro H.265 encoder
          timecode        : 11:52:41:25
      Stream #0:1[0x2](eng): Audio: aac (LC) (mp4a / 0x6134706D), 48000 Hz, stereo, fltp, 189 kb/s (default)
        Metadata:
          creation_time   : 2022-02-10T11:53:24.000000Z
          handler_name    : GoPro AAC
          vendor_id       : [0][0][0][0]
          timecode        : 11:52:41:25
      Stream #0:2[0x3](eng): Data: none (tmcd / 0x64636D74) (default)
        Metadata:
          creation_time   : 2022-02-10T11:53:24.000000Z
          handler_name    : GoPro TCD
          timecode        : 11:52:41:25
      Stream #0:3[0x4](eng): Data: bin_data (gpmd / 0x646D7067), 90 kb/s (default)
        Metadata:
          creation_time   : 2022-02-10T11:53:24.000000Z
          handler_name    : GoPro MET
      Stream #0:4[0x5](eng): Data: none (fdsc / 0x63736466), 20 kb/s (default)
        Metadata:
          creation_time   : 2022-02-10T11:53:24.000000Z
          handler_name    : GoPro SOS
      Stream #0:5[0x6](eng): Video: hevc (Main) (hvc1 / 0x31637668), yuvj420p(pc, bt709), 4096x1344 [SAR 1:1 DAR 64:21], 29809 kb/s, 29.97 fps, 29.97 tbr, 90k tbn (default)
        Metadata:
          creation_time   : 2022-02-10T11:53:24.000000Z
          handler_name    : GoPro H.265
          vendor_id       : [0][0][0][0]
          encoder         : GoPro H.265 encoder
          timecode        : 11:52:41:25
        Side data:
          displaymatrix: rotation of nan degrees
      Stream #0:6[0x7](eng): Audio: pcm_s32le (in32 / 0x32336E69), 48000 Hz, 4.0, s32, 6144 kb/s (default)
        Metadata:
          creation_time   : 2022-02-10T11:53:24.000000Z
          handler_name    : GoPro AMB
          vendor_id       : [0][0][0][0]
    At least one output file must be specified
    [dopey@dopey-desktop raw_360_footage]$


Tracks 0:0 and 0:5 are video, for a timelapse it is tracks 0:0 and 0:4






