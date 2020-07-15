# Kinect生成点云

[toc]

## 安装系统

###	Raspbian

- 树莓派上的`Raspbian` [官方系统.img](..\..\下载\树莓派系统\官方系统.img) 为官方系统，可以在[官方网站](https://www.raspberrypi.org/downloads/raspberry-pi-os/)上进行下载 [2020-05-27-raspios-buster-full-armhf.img](..\..\下载\树莓派系统\2020-05-27-raspios-buster-full-armhf.img) 。另外还有64位的官方系统。 [2020-05-27-raspios-buster-arm64.img](..\..\下载\树莓派系统\2020-05-27-raspios-buster-arm64.img) 
  - 优点：树莓派官方系统，兼容性好，支持`VNC`远程桌面连接（64位目前不支持`VNC`）
  - 缺点：只支持`OpenGL2`，和`VTK`不兼容，虽然不影响运行，但是在运行`PCL`时会一直有错误提示，导致输出的结果难以查看

### Ubuntu

- 树莓派上的[Ubuntu Server系统](https://ubuntu.com/download/raspberry-pi)有18.04和20.04LTS版本，各自拥有32位和64位系统。 [ubuntu-18.04.4-preinstalled-server-armhf+raspi3.img](..\..\下载\树莓派系统\ubuntu-18.04.4-preinstalled-server-armhf+raspi3.img)  [ubuntu-20.04-preinstalled-server-arm64+raspi.img](..\..\下载\树莓派系统\ubuntu-20.04-preinstalled-server-arm64+raspi.img) 
  - 优点：在网上可以找到许多资源，支持`OpenGL3`
  - 缺点：不支持`VNC`远程桌面

## 软件安装

### OpenKinect安装

[libfreenect源码](https://github.com/OpenKinect/libfreenect)

**安装步骤**

- 安装依赖软件

```Shell
sudo apt-get install git cmake build-essential libusb-1.0-0-dev
sudo apt-get install freeglut3-dev libxmu-dev libxi-dev
```

- Fetch & Build

```Shell
git clone https://github.com/OpenKinect/libfreenect
cd libfreenect
mkdir build
cd build
cmake -L ..
make
```

**API接口**

如果需要获取Kinect的深度图或者彩色图的话，需要更改`Wrappers`里的程序，然后再次进入`build`里执行`make`即可。

- 更改过的`cpp`显示点云数据[程序](https://pan.baidu.com/s/1EnXq4TiNr_0_oxD7W7_DGQ#list/path=%2F),提取码为p7us

  参考[此处](https://www.cnblogs.com/gaoxiang12/p/4652478.html)的文章将深度信息和彩色信息转化为点云。

  `cpp_pc_view.cpp`目前实现功能为运行后显示点云数据，按a后提取10帧点云数据并保存到`txt`

  `dian.cpp`将`txt`转为点云数据

  - 缺陷

  1. 目前还没有把`RGB`信息提取出来。

  2. 生成的`txt`文件最后一行会多一个回车，导致点云数据多出一行来。

**运行**

进入`build/bin`内选择相应的程序`sudo ./...`

### 安装PCL

**安装依赖库**

```shell
sudo apt-get update  
sudo apt-get install git build-essential linux-libc-dev  
sudo apt-get install cmake cmake-gui   
sudo apt-get install libusb-1.0-0-dev libusb-dev libudev-dev  
sudo apt-get install mpi-default-dev openmpi-bin openmpi-common    
sudo apt-get install libflann1.9 libflann-dev  
sudo apt-get install libeigen3-dev  
sudo apt-get install libboost-all-dev  
sudo apt-get install libqhull* libgtest-dev  
sudo apt-get install freeglut3-dev pkg-config  
sudo apt-get install libxmu-dev libxi-dev   
sudo apt-get install mono-complete  
```

**下载VTK**

​	下载[VTK-8.2.0.tar.gz](https://vtk.org/download/)，解压后执行如下命令：

```shell
mkdir build
cd build
cmake ../ -DBUILD_SHARED_LIBS=ON -DBUILD_TESTING=ON -DCMAKE_BUILD_TYPE=Release 
make
sudo make install
```

**源码编译PCL**

​	下载[源码](https://github.com/PointCloudLibrary/pcl/releases)，解压后执行下列命令：

```shell
cd pcl-pcl-1.9.1 && mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=Release ..
make
sudo make install
```



### 其他

**安装`OpenNI`和`SensorKinect`**

​	本来想用`OpenNI+SensorKinect+Pcl`获取点云数据，使用`pcl_openni_grabber`函数来获取`kinect`的数据，可是运行测试时总是报错，显示获取数据超时或者`USB`接口不支持，无法获得`kinect`的数据，因此最后放弃了这个方案。

1. **OpenNI**

```shell
git clone https://github.com/OpenNI/OpenNI.git
cd OpenNI
git checkout unstable
gedit ./Platform/Linux/Build/Common/Platform.Arm
#delete ‘-mfloat-abi=softfp‘
cd ~/OpenNI/Platform/Linux/CreateRedist
./RedistMaker.Arm
cd ~/OpenNI/Platform/Linux/Redist/OpenNI-Bin-Dev-Linux-Arm-V1.5.8.5
sudo ./install.sh
```



2. **SensorKinect**

   网上说的下载方式一般编译都有问题，从这个[安装包](https://pan.baidu.com/s/1u19ted6Mr0dhWeEf4Fpn9Q)<u>提取码：c9gf</u>编译。

```shell
cd SensorKinect-unstable
git checkout unstable
gedit ./Platform/Linux/Build/Common/Platform.Arm
# delete ‘-mfloat-abi=softfp‘
cd ~/SensorKinect/Platform/Linux/CreateRedist
./RedistMaker
cd ~/SensorKinect/Platform/Linux/Redist/Sensor-Bin-Linux-Arm-v5.1.2.1/Config/
sudo gedit GlobalDefaultsKinect.ini
#modify `;UsbInterface=2` into `UsbInterface=1`
cd ~/SensorKinect/Platform/Linux/Redist/Sensor-Bin-Dev-Linux-Arm-v5.1.2.1/
sudo ./install.sh
```

	### Create_ap

```shell
git clone https://github.com/oblique/create_ap.git
cd create_ap
sudo make install
sudo apt-get install util-linux procps hostapd iproute2 iw haveged dnsmasq
```



-   具体开启方法为`sudo create_ap -- ieee80211ac --freq-band 5 wlan0 eth0 raspberry 123456789`
-   在`/etc/rc.local`中加入`sudo create_ap -- ieee80211ac --freq-band 5 wlan0 eth0 raspberry 123456789 &`就可以开机自启动。