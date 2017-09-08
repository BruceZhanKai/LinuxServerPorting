# Linux Face Recogniton TensorRT

## Ubuntu 16.04 Server


### Boot Override
`Choose [UEFI: USB DISK]`

### Detect keyboard layout?
`<No>`

### Unmount partitions that are in use?
` <No>`

### Partitioning method:
` <Manual>`

### Disk Partition

|容量|位置|格式|
| ------| ------ | ------ |
|500MB|EPS| |
|500MB|/boot|ext4| 
|100GB|/|ext4| 
|130GB|/home|ext4| 
|68GB| |swap| 


### Choose software to install:
` <standard server>`

### xubuntu-desktop install
```
$ sudo apt-get install xfce4
$ sudo apt-get install xubuntu-desktop
```

### static network setting
```
$ sudo gedit /etc/network/interfaces

/****************************************************************************************/
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).
source /etc/network/interfaces.d/*
# The loopback network interface
auto lo
iface lo inet loopback
# The primary network interface
auto ens3f0
#iface ens3f0 inet dhcp
iface ens3f0 inet static
address 172.21.39.40
netmask 255.255.0.0
gateway 172.21.39.251
dns-nameservers 172.21.37.15 172.21.37.35
#sudo ifdown ens3f0 && sudo ifup ens3f0
/****************************************************************************************/

$ sudo gedit /etc/NetworkManager/NetworkManager.conf

/****************************************************************************************/
[main]
plugins=ifupdown,keyfile,ofono
dns=dnsmasq

[ifupdown]
managed=false
/****************************************************************************************/

$ sudo gedit /etc/resolv.conf

/****************************************************************************************/
# Dynamic resolv.conf(5) file for glibc resolver(3) generated by resolvconf(8)
#     DO NOT EDIT THIS FILE BY HAND -- YOUR CHANGES WILL BE OVERWRITTEN
nameserver 172.21.37.15
nameserver 172.21.37.35
/****************************************************************************************/

$ reboot
```
_______________________________________________________________________________________________

## Ubuntu 16.04

### Foce UEFI Installation -> [back]

### 修復 GRUB（PATA=hda、SATA/SCSI/SAS=sda）
```
$ sudo update-grub
$ sudo grub-install /dev/sda
```

### 修復 出現 "grub rescue>" (進不了Linux桌面時)
```
grub rescue > set boot=(hd0,6)
grub rescue > set prefix=(hd0,6)/grub
grub rescue > insmod normal
grub rescue > normal
```
### login usb-ubuntu system
```
$ sudo fdisk -l
$ sudo mount /dev/sda6 /mnt
$ sudo grub-install --root-directory=/mnt /dev/sda
```
reboot and change boot setting first sata

### 進入Linux Install Dependencies
```
$ sudo apt-get update
$ sudo apt-get install -y build-essential
$ sudo apt-get install -y cmake
$ sudo apt install cmake-curses-gui
$ sudo apt-get update
$ sudo apt-get upgrade
$ sudo apt-get dist-upgrade

$ sudo add-apt-repository ppa:notepadqq-team/notepadqq
$ sudo apt-get update
$ sudo apt-get install notepadqq
```

## CUDA

Download [cuda8.0](https://developer.nvidia.com/cuda-downloads)
```
$ sudo dpkg -i cuda-repo-ubuntu1604-8-0-local-ga2_8.0.61-1_amd64.deb
$ sudo apt-get update
$ sudo apt-get install cuda
$ sudo vi ~/.bashrc
# export PATH="$PATH:/usr/local/cuda-8.0/bin" 
# export LD_LIBRARY_PATH="/usr/local/cuda-8.0/lib64"
$ source ~/.bashrc
```

## OpenCV

issue

```
OpenCV static library was compiled with CUDA 7.5 support.  Please, use the
  same version or rebuild OpenCV with CUDA 8.0
Call Stack (most recent call first):
  CMakeLists.txt:22 (find_package)

Could NOT find CUDA: Found unsuitable version "7.5", but required is exact
  version "8.0" (found /usr)

```

opencv-2.4.13-linux

```
$ wget http://downloads.sourceforge.net/project/opencvlibrary/opencv-unix/2.4.13/opencv-2.4.13.zip
$ sudo apt-get install build-essential
$ sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
$ sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
$ unzip opencv-2.4.13.zip
$ cd opencv-2.4.13
$ mkdir release
$ cd release
$ cmake cmake -G "Unix Makefiles" -DCMAKE_CXX_COMPILER=/usr/bin/g++ CMAKE_C_COMPILER=/usr/bin/gcc -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_INSTALL_PREFIX=/usr/local -DWITH_TBB=ON -DBUILD_NEW_PYTHON_SUPPORT=ON -DWITH_V4L=ON -DINSTALL_C_EXAMPLES=ON -DINSTALL_PYTHON_EXAMPLES=ON -DBUILD_EXAMPLES=ON -DWITH_OPENGL=ON -DBUILD_FAT_JAVA_LIB=ON -DINSTALL_TO_MANGLED_PATHS=ON -DINSTALL_CREATE_DISTRIB=ON -DINSTALL_TESTS=ON -DENABLE_FAST_MATH=ON -DWITH_IMAGEIO=ON -DBUILD_SHARED_LIBS=OFF -DWITH_GSTREAMER=ON ..
$ cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -DWITH_TBB=ON -DINSTALL_C_EXAMPLES=ON -DINSTALL_TESTS=ON -DBUILD_TBB=ON ..
$ make all -j4
$ sudo make install
```

## Caffe

```
$ sudo apt-get install libprotobuf-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler
$ sudo apt-get install --no-install-recommends libboost-all-dev 
$ sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev
$ sudo apt-get install libatlas-base-dev
$ git clone git://github.com/BVLC/caffe.git
$ cd caffe/
$ cp Makefile.config.example Makefile.config
$ mkdir build
$ cd build
$ cmake ..
$ make all -j4
$ make install
```

error Could NOT find LevelDB (missing: LevelDB_INCLUDE LevelDB_LIBRARY)
```
$ sudo apt-get install libleveldb-dev
```

erorr opencv in cmake/Dependencies.cmake
```
#add
set( OpenCV_DIR /home/user/opencv-2.4.13.2/release )
set(OpenCV_FOUND 1)
```

erorr /usr/bin/ld: cannot find -lopencv_dep_cudart
```
$ sudo ln -s /usr/local/cuda-8.0/lib64/libcudart.so /usr/local/lib/
$ sudo ln -s /usr/local/cuda-8.0/lib64/libcudart.a /usr/local/lib/
#add in /caffe/CmakeLists.txt
set(CUDA_USE_STATIC_CUDA_RUNTIME OFF)
```


build code issue 
```
/home/bruce/caffe/include/caffe/layer_factory.hpp:48:34: fatal error: caffe/proto/caffe.pb.h: No such file or directory
cmakelists add
include_directories(/home/bruce/caffe/build/install/include/)
or
include_directories(/home/bruce/caffe/build/include/)

```

## TensorRT

Download [TensorRT](https://developer.nvidia.com/nvidia-tensorrt-download)
```
$ sudo dpkg -i nv-gie-repo-ubuntu1604-cuda8.0-trt2.1-20170614_1-1_amd64.deb
$ sudo apt-get update
$ sudo apt-get install tensorrt-2.1.2

$ dpkg -l | grep TensorRT
### you should see:
### libnvinfer-dev 3.0.2-1+cuda8.0 amd64 TensorRT development libraries and headers
### libnvinfer3 3.0.2-1+cuda8.0 amd64 TensorRT runtime libraries   tensorrt-2.1.2 3.0.2-1+cuda8.0 amd64 Meta package of         TensorRT

### run & test
$ cd /usr/src/tensorrt/samples
$ sudo make
$ cd ../bin/
$ giexec --deploy=mnist.prototxt --model=mnist.caffemodel --output=prob

```

issue
```
tensorrt-2.1.2 : Depends: libnvinfer3 (>= 3.0.2) but it is not going to be installed
Depends: libnvinfer-dev (>= 3.0.2) but it is not going to be installed
E: Unable to correct problems, you have held broken packages.

solve
update cuda, it solves.
```

## Eigen

```
$ sudo apt-get install libeigen3-dev
$ cd /usr/include/eigen3
$ sudo cp Eigen/ .. -R
$ sudo cp unsupported/ .. -R
```

## MySQL

Download [MySQL Connector](https://dev.mysql.com/downloads/connector/cpp/)

Choose mysql-connector-c++-1.1.9-linux-ubuntu16.04-x86-64bit

```
$ sudo apt-get install libboost-dev libboost-dbg libboost-doc
```

## MySQL WorkBench

remove mysql all
```
$ sudo apt-get purge --auto-remove mysql-common mysql-server mariadb-server
$ sudo apt-get autoremove
$ sudo apt-get autoclean
$ sudo rm -rf /etc/mysql
$ sudo rm -rf /var/lib/mysql
$ killall -9 mysql (or killall -9 mysqld)
$ userdel mysql
```

install mysql-server & mysql-workbench
```
$ sudo apt-get install mysql-common mysql-server

remember to SET password for root

$ sudo apt-get install mysql-workbench
```







