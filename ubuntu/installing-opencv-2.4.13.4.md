# 安装OpenCV-2.4.13.4
## 环境
- 操作系统：Ubuntu
- 系统版本：16.04.3 LTS
## 准备
1. 安装编译环境：`sudo apt install build-essential`；
2. 安装依赖库：`sudo apt install libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev`；
## 安装
1. 下载OpenCV-2.4.13.4源码并解压：
  ```console
  wget https://github.com/opencv/opencv/archive/2.4.13.4.tar.gz -O opencv-2.4.13.4.tar.gz
  tar -zxvf ./opencv-2.4.13.4.tar.gz -C ./
  ```
2. 安装CMake：`sudo apt install cmake`；
3. 准备编译：
  ```console
  cd ./opencv-2.4.13.4
  mkdir build
  cd build
  cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local ..
  ```
4. 编译：`make -j10`；
5. 安装：`sudo make install`；
---
安装步骤到此结束，以下为卸载和删除操作：

6. 卸载：`sudo make uninstall`；
7. 删除：`sudo find /usr/local -name "*opencv*" | xargs sudo rm -rf`；
