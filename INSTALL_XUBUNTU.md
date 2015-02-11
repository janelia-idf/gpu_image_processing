Installation Xubuntu
====================


###Install Xubuntu

<http://xubuntu.org/getxubuntu/>

Install Latest LTS release: 14.04, Trusty Tahr

```shell
sudo apt-get update
sudo apt-get dist-upgrade -y
sudo apt-get install bash-completion command-not-found git -y
```

###Install CUDA for GPU Support

On a host computer open a browser and navigate to:

<https://developer.nvidia.com/cuda-downloads>

###Download, Configure, and Build OpenCV

```shell
# Some general development libraries
sudo apt-get install build-essential make cmake cmake-curses-gui g++
# libav video input/output development libraries
sudo apt-get install libavformat-dev libavutil-dev libswscale-dev
# Video4Linux camera development libraries
sudo apt-get install libv4l-dev
# OpenGL development libraries (to allow creating graphical windows)
sudo apt-get install libglew-dev libglewmx-dev
# Eigen3 math development libraries
sudo apt-get install libeigen3-dev
# Extras
sudo apt-get install python-dev python-numpy
sudo apt-get install libjpeg-dev libpng-dev libtiff-dev libjasper-dev
sudo apt-get install libtbb-dev libqt4-dev libqt4-opengl-dev
sudo apt-get install checkinstall yasm libdc1394-22-dev libxine-dev
sudo apt-get install libgstreamer0.10-dev libgstreamer-plugins-base0.10-dev
sudo apt-get install libgtk2.0-dev libmp3lame-dev
sudo apt-get install libopencore-amrnb-dev libopencore-amrwb-dev libtheora-dev
sudo apt-get install libvorbis-dev libxvidcore-dev x264 v4l-utils libav-tools
mkdir ~/git
cd ~/git
git clone https://github.com/Itseez/opencv.git
cd opencv
git checkout -b 2.4 origin/2.4
mkdir -p ~/builds/opencv/opencv-2.4
cd ~/builds/opencv/opencv-2.4
cmake -D CUDA_ARCH_BIN="3.2" -D CUDA_ARCH_PTX="" -D BUILD_TESTS=OFF -D BUILD_PERF_TESTS=OFF -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D WITH_TBB=ON -D WITH_QT=ON -D WITH_CUDA=ON -D WITH_CUBLAS=ON -D CUDA_FAST_MATH=ON -D CUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda/ -D WITH_V4L=ON -D WITH_OPENGL=OFF -D INSTALL_C_EXAMPLES=ON -D INSTALL_PYTHON_EXAMPLES=ON -D INSTALL_TESTS=ON -D BUILD_EXAMPLES=OFF -D WITH_OPENCL=ON -D ENABLE_FAST_MATH=ON -D ENABLE_PROFILING=ON ~/git/opencv
make -j2
sudo checkinstall
```

To uninstall:

```shell
sudo dpkg -r opencv
```

###Install ROS

<http://wiki.ros.org/ROS/Installation>

```shell
sudo update-locale LANG=C LANGUAGE=C LC_ALL=C LC_MESSAGES=POSIX
sudo sh -c 'echo "deb http://packages.namniart.com/repos/ros trusty main" > /etc/apt/sources.list.d/ros-latest.list'
wget http://packages.namniart.com/repos/namniart.key -O - | sudo apt-key add -
sudo apt-get update
sudo apt-get install ros-indigo-ros-base
sudo apt-get install python-rosdep
sudo rosdep init
rosdep update
echo "source /opt/ros/indigo/setup.bash" >> ~/.bashrc
source ~/.bashrc
sudo apt-get install python-rosinstall
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/src
catkin_init_workspace
cd ..
catkin_make
source devel/setup.bash
```

###Install Point Grey FlyCapture2 Library

Links and instructions for downloading and installing the latest
FlyCapture2 library from Point Grey for Linux can be found here:

<http://www.ptgrey.com/support/downloads>

```shell
cd ~/Downloads
tar -zxvf flycapture*
cd flycapture-<version>_arm/lib
sudo cp libflycapture.so* /usr/lib
cd ..
sudo cp -n -r include/* /usr/include
cd ..
sudo sh flycap2-conf
# follow the instructions that the script takes you through
sudo reboot
```

On a host computer ssh into ubuntu@tegra-ubuntu:

```shell
mkdir ~/bin
mkdir ~/lib
mkdir ~/flycapture_examples
cd ~/Downloads/flycapture-<version>_arm/
cp -r src/* ~/flycapture_examples
cd ~/flycapture_examples/FlyCapture2Test
make
mkdir ~/Pictures/FlyCapture2Test
# plug in Flea3 camera into USB3 port
cd ~/Pictures/FlyCapture2Test
~/bin/FlyCapture2Test
```

###Install ROS Point Grey Camera Driver

On a host computer ssh into ubuntu@tegra-ubuntu:

```shell
sudo mkdir /usr/include/flycapture
sudo ln -s /usr/include/FlyCapture2.h /usr/include/flycapture/FlyCapture2.h
sudo apt-get install ros-indigo-roslint
sudo apt-get install ros-indigo-camera-info-manager
sudo apt-get install ros-indigo-driver-common
sudo apt-get install ros-indigo-image-proc
cd ~/catkin_ws/src/
git clone https://github.com/ros-drivers/pointgrey_camera_driver.git
cd ..
catkin_make
echo "source ~/catkin_ws/devel/setup.bash" >> ~/.bashrc
source ~/.bashrc
```

####Test ROS Point Grey Camera Driver

Connect Point Grey USB3 camera to Jetson USB3 port.

On host computer run:

```shell
hostname #Use response in <hostname_master_computer> below
roscore
```

On a host computer ssh into ubuntu@tegra-ubuntu:

```shell
rosrun pointgrey_camera_driver list_cameras
export ROS_MASTER_URI=http://<hostname_master_computer>:11311
roslaunch pointgrey_camera_driver camera.launch
```

On another terminal on the host computer run:

```shell
rosrun image_view image_view image:=/camera/image_raw
```

###Install ROS UVC Camera Driver

On a host computer ssh into ubuntu@tegra-ubuntu:

```shell
sudo apt-get install ros-indigo-uvc-camera
```

####Test ROS UVC Camera Driver

Connect USB camera to Jetson USB3 port.

On host computer run:

```shell
hostname #Use response in <hostname_master_computer> below
roscore
```

On a host computer ssh into ubuntu@tegra-ubuntu:

```shell
export ROS_MASTER_URI=http://<hostname_master_computer>:11311
rosrun uvc_camera uvc_camera_node
```

On another terminal on the host computer run:

```shell
rosrun image_view image_view image:=/image_raw
```

