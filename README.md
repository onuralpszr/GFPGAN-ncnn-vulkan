# GFPGAN-ncnn-vulkan 🚀

[![Linux-CI](https://github.com/onuralpszr/GFPGAN-ncnn-vulkan/actions/workflows/build_devCI.yml/badge.svg?branch=main)](https://github.com/onuralpszr/GFPGAN-ncnn-vulkan/actions/workflows/build_devCI.yml)
![GitHub](https://img.shields.io/github/license/onuralpszr/GFPGAN-ncnn-vulkan?color=red)
[![Open issue](https://img.shields.io/github/issues/onuralpszr/GFPGAN-ncnn-vulkan)](https://github.com/onuralpszr/GFPGAN-ncnn-vulkan/issues)
[![Closed issue](https://img.shields.io/github/issues-closed/onuralpszr/GFPGAN-ncnn-vulkan)](https://github.com/onuralpszr/GFPGAN-ncnn-vulkan/issues)
![GitHub pull requests](https://img.shields.io/github/issues-pr-raw/onuralpszr/GFPGAN-ncnn-vulkan)
![cpp](https://img.shields.io/badge/C++20-Project-blue.svg?style=flat&logo=c%2B%2B)
[![Github All Releases](https://img.shields.io/github/downloads/onuralpszr/GFPGAN-ncnn-vulkan/total.svg)]()

Ncnn with Vulkan implementation of **GFPGAN aims at developing Practical Algorithms for Real-world Face Restoration**

This repository contains the code and pre-trained models for a real-world face restoration algorithm based on the [GFPGAN](https://github.com/TencentARC/GFPGAN) method and optimized for mobile devices using the [NCNN](https://github.com/Tencent/ncnn) framework with a Vulkan backend.

The goal of this project is to develop practical algorithms that can restore the appearance of damaged or low-quality face images, such as those obtained from security cameras, old photographs, or social media profiles. The proposed approach combines the power of deep learning with the speed and efficiency of hardware acceleration, making it suitable for real-time applications on smartphones, drones, or robots.


## Get Submodules
Make sure submodules are initialized and updated
```sh
git submodule update --init --recursive
```

## Clone project with Submodules

```sh
git clone --recursive https://github.com/onuralpszr/GFPGAN-ncnn-vulkan.git
```

## Project Prerequisites ⚙️

- CMake version 3.20 or later
- C++17 or above with filesystem support
- Clang-Tidy for code analysis (optional)
- Threads library
- Vulkan SDK
- glslangValidator executable
- OpenCV library
- OpenMP library
- ncnn library
- libwebp library

## Building 🛠️

Configure and build

```sh
mkdir -p build && cd build
cmake ..
cmake --build . --parallel $(($(nproc) - 1))
```

## :construction: Model support :construction:

1. GFPGANCleanv1-NoCE-C2

### TODO: :bookmark_tabs:
- [x] Support ncnn-vulkan
- [ ] Convert pth->onnx->ncnn
- [ ] Model with colorization

### References
1. https://github.com/xinntao/Real-ESRGAN
2. https://github.com/TencentARC/GFPGAN
3. https://github.com/xinntao/Real-ESRGAN-ncnn-vulkan
3. https://github.com/Tencent/ncnn
4. https://github.com/Tencent/ncnn/tree/master/tools/pnnx
5. https://github.com/pnnx/pnnx
6. https://github.com/deepcam-cn/yolov5-face
7. https://github.com/derronqi/yolov7-face
8. https://github.com/derronqi/yolov8-face
9. https://github.com/FeiGeChuanShu/GFPGAN-ncnn
10. https://github.com/ultralytics/ultralytics


## Download Model files (GFPGAN-ncnn model files)

### Models-v0.0.1

https://github.com/onuralpszr/GFPGAN-ncnn-vulkan/releases/download/v0.0.1-models/GFPGAN-ncnn-models.zip
