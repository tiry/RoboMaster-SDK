# RoboMaster-SDK (Updated Fork)

[![Gitter](https://badges.gitter.im/RoboMaster-SDK/community.svg)](https://gitter.im/RoboMaster-SDK/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

<img src="docs/source/images/robomaster.jpg" width="600">

> **This is a fork of the official [DJI RoboMaster SDK](https://github.com/dji-sdk/RoboMaster-SDK) with updates for modern systems.**

## Why This Fork?

The official DJI RoboMaster SDK was last updated in 2021 and has compatibility issues with modern systems:

1. **FFmpeg 5.x+ incompatibility**: The `libmedia_codec` library used deprecated FFmpeg APIs that were removed in FFmpeg 5.0+ (released 2022)
2. **Python 3.10+ incompatibility**: The bundled pybind11 v2.4 doesn't support Python 3.10+ due to internal CPython API changes
3. **CMake version issues**: The old cmake_minimum_required directives cause errors with modern CMake (3.27+)

This fork updates the SDK to work with:
- **FFmpeg 5.x, 6.x, 7.x, 8.x** (and newer)
- **Python 3.8, 3.9, 3.10, 3.11, 3.12** (and potentially newer)
- **Modern CMake** (3.15+)

## Changes Made

### `lib/libmedia_codec/src/media_codec.h`

| Old (FFmpeg 3.x/4.x) | New (FFmpeg 5.x+) | Reason |
|---------------------|-------------------|--------|
| `avcodec_register_all()` | Removed | Auto-registration since FFmpeg 4.0 |
| `avcodec_decode_video2()` | `avcodec_send_packet()` + `avcodec_receive_frame()` | Old API removed in FFmpeg 5.0 |
| `av_init_packet()` | `av_packet_alloc()` | Deprecated in FFmpeg 4.4, removed later |
| `avpicture_fill()` | `av_image_fill_arrays()` | AVPicture removed in FFmpeg 5.0 |
| `avcodec_close()` | `avcodec_free_context()` | avcodec_close deprecated |
| `AVCodec *codec` | `const AVCodec *codec` | Return type changed |
| `CODEC_CAP_TRUNCATED` / `CODEC_FLAG_TRUNCATED` | Removed | Flags removed in FFmpeg 5.x |
| `py::str` in decode() | `py::bytes` | Python 3 passes bytes for video data |

### `lib/libmedia_codec/CMakeLists.txt`

- Updated `cmake_minimum_required` to VERSION 3.15
- Replaced bundled pybind11 submodule with `FetchContent` to get pybind11 v2.11.1
- Updated C++ standard to C++14

## Installation

### Prerequisites

```bash
# Ubuntu/Debian
sudo apt-get install cmake libopus-dev libavcodec-dev libavformat-dev libswscale-dev python3-dev

# Arch Linux
sudo pacman -S cmake opus ffmpeg python

# macOS
brew install cmake opus ffmpeg python
```

### Install libmedia_codec

```bash
git clone https://github.com/tiry/RoboMaster-SDK.git
cd RoboMaster-SDK
pip install lib/libmedia_codec
```

### Install RoboMaster SDK

```bash
pip install git+https://github.com/tiry/RoboMaster-SDK.git
```

Or for the official version (may not work on modern systems):
```bash
pip install git+https://github.com/dji-sdk/RoboMaster-SDK.git
```

## Original README

Learn more about the RoboMaster Education Robot: https://www.dji.com/robomaster-ep

RoboMaster Developer Guide: https://robomaster-dev.rtfd.io/

Gitee link for RoboMaster SDK download: https://gitee.com/xitinglin/RoboMaster-SDK

## License

Same as the original DJI RoboMaster SDK - Apache License 2.0
