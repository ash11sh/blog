---
date: "2020-12-02"
draft: false
lastmod: "2020-12-02"
publishdate: "2020-12-02"
tags:
- raspberry
title: Installing Ubuntu 20.04 on raspberry pi for Tensorflow
---

<p align="center"><img src="https://live.staticflickr.com/65535/50672069586_43a96d4da5_c_d.jpg" style="zoom:200%;" />



## Why Ubuntu - 32 bit/ 64 bit ?
Out of plenty of operating systems available for raspberry pi, we need stable one's intended to run  ml projects (tf-lite, opencv). Raspberry Pi group's debian based **raspbian os** provide good support and stable, but it is 32 bit. There is a 64 bit version out but in beta stage. We need a 64 bit OS for increased performance. Ubuntu provides OS in 64 bit version for rpi. It's good to go. 


{{< note >}}
 Note: If your device has low memory (1gb) then it's better to choose 32bit OS.
{{< /note >}}


## Headless installation

If you don't have separate monitor, (or) keyboard, (or) mouse for the regular installation you can choose this process.

1. Download the Ubuntu Server Image from the official site: [link](https://ubuntu.com/download/raspberry-pi/thank-you?version=20.04.1&architecture=server-arm64+raspi)
2. Download [balena etcher](https://www.balena.io/etcher/) and flash the image to the SD card.
3. After flashing is done, open boot folder from the sd card and edit `network-config` file  to connect wifi.

Uncomment the lines below and change according your network.

```
wifis:
  wlan0:
  dhcp4: true
  optional: true
  access-points:
    <wifi network name>:
      password: "<wifi password>"
```

Save the file and remove sd card and place it in raspberry pi. After first boot wait for 20 sec and restart the machine.

Check for rpi's ip address using wifi portal or **fing app** in mobile phone and ssh into the machine using the ip address.




## Desktop flavour install 

We installed the server, next step is to install desktop environment. There are [flavours](https://ubuntu.com/download/flavours) in ubuntu for DE. As we have less computational resources, choose a lightweight flavour like xubuntu/lubuntu. 

I used this awesome [script](https://github.com/wimpysworld/desktopify)  to install desktop environment.

{{< cmd >}}
git clone https://github.com/wimpysworld/desktopify.git
cd desktopify
sudo ./desktopify --de xubuntu
{{< /cmd >}}

Finally install xrdp server to access the gui remotely. (xrdp >>> vnc)
{{< cmd >}}
sudo apt-get install xrdp
{{< /cmd >}}



you can connect remotely using **remmina** by ip-address 

{{< figureCupper
img="im3.png" 
caption="" 
command="Resize" 
options="500x" >}}


## overclocking

{{< warning >}}
Proceed with caution. Overclock your device if you had a proper cooling system
{{< /warning >}}

Add these below lines at end of  `/boot/config.txt`

{{< cmd >}}
sudo nano /boot/config.txt
{{< /cmd >}}

```text
over_voltage = 2
arm_freq = 1750
```


then reboot : - )

{{< figureCupper
img="im2.png" 
caption="" 
command="Resize" 
options="500x" >}}

To check clock speed use this command:
{{< cmd >}}
`lscpu | grep MHz.`
{{< /cmd >}}


## memory swapping

{{< cmd >}}
sudo apt-get install zram-config
sudo nano /usr/bin/init-zram-swapping
{{< /cmd >}}

Add a multiplier (* 5) at the end of the mem.... line. This gives you about 4.5 Gbyte of memory swap ( like this `1024 * 5`)

{{< figureCupper
img="im1.png" 
caption="" 
command="Resize" 
options="500x" >}}

reboot : - )



## tensorflow install

* Build tensorflow using the pre-built binaries from this repo: https://github.com/PINTO0309/Tensorflow-bin

* Build tensorflow-lite using the pre-built binaries from this repo: https://github.com/PINTO0309/TensorflowLite-bin

 
## opencv install

### Option 1

directly installing from  ppa repo:


{{< cmd >}}
sudo apt-get update
sudo apt-get upgrade
sudo apt install libopencv-dev python3-opencv
{{< /cmd >}}


you can verify the installation by following command:
{{< cmd >}}
python3 -c "import cv2; print(cv2.__version__)"
{{< /cmd >}}


### Option 2 - directly from source

Before moving forward remember to set swap space(> 3gb), it is essential for build process.

Install these following dependencies

{{< cmd >}}
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install build-essential cmake gcc g++ git unzip pkg-config
sudo apt-get install libjpeg-dev libpng-dev libtiff-dev
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev
sudo apt-get install libgtk2.0-dev libcanberra-gtk*
sudo apt-get install libxvidcore-dev libx264-dev
sudo apt-get install python3-dev python3-numpy python3-pip
sudo apt-get install libtbb2 libtbb-dev libdc1394-22-dev
sudo apt-get install libv4l-dev v4l-utils
sudo apt-get install libopenblas-dev libatlas-base-dev libblas-dev
sudo apt-get install liblapack-dev gfortran libhdf5-dev
sudo apt-get install libprotobuf-dev libgoogle-glog-dev libgflags-dev
sudo apt-get install protobuf-compiler
{{< /cmd >}}

Next, download and unzip the packages

{{< cmd >}}
cd ~
wget -O opencv.zip https://github.com/opencv/opencv/archive/4.4.0.zip
wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/4.4.0.zip
unzip opencv.zip
unzip opencv_contrib.zip
#rename the folders
mv opencv-4.4.0 opencv
mv opencv_contrib-4.4.0 opencv_contrib
cd opencv
mkdir build
cd build
{{< /cmd >}}

Build make

```shell
$ cmake -D CMAKE_BUILD_TYPE=RELEASE \
	  -D CMAKE_INSTALL_PREFIX=/usr/local \
	  -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib/modules \
	  -D ENABLE_NEON=ON \
	  -D BUILD_TIFF=ON \
      -D WITH_FFMPEG=ON \
	  -D WITH_GSTREAMER=ON \
	  -D WITH_TBB=ON \
      -D BUILD_TBB=ON \
      -D BUILD_TESTS=OFF \
      -D WITH_EIGEN=OFF \
      -D WITH_V4L=ON \
      -D WITH_LIBV4L=ON \
      -D WITH_VTK=OFF \
      -D OPENCV_ENABLE_NONFREE=ON \
      -D INSTALL_C_EXAMPLES=OFF \
      -D INSTALL_PYTHON_EXAMPLES=OFF \
      -D BUILD_NEW_PYTHON_SUPPORT=ON \
      -D BUILD_opencv_python3=TRUE \
      -D OPENCV_GENERATE_PKGCONFIG=ON \
      -D BUILD_EXAMPLES=OFF ..
```

build with these commands

{{< cmd >}}
make -j4
sudo make install
sudo ldconfig
sudo apt-get update
{{< /cmd >}}

Verify installation
{{< cmd >}}
pkg-config --modversion opencv4
python3 -c "import cv2; print(cv2.__version__)"
{{< /cmd >}}

