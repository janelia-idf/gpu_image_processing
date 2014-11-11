Nvidia Jetson TK1
=================


###Linux 4 Tegra

On a host computer open a browser and navigate to:

<https://developer.nvidia.com/jetson-tk1-support>

Use the quick start guide to update the Jetson TK1 board with the
latest Linux 4 Tegra (L4T) release.

On a host computer ssh into ubuntu@tegra-ubuntu (password: ubuntu):

```shell
sudo apt-get update
sudo apt-get dist-upgrade -y
sudo apt-get install bash-completion command-not-found git -y
```

###Install CUDA for GPU Support

On a host computer open a browser and navigate to:

<https://developer.nvidia.com/jetson-tk1-support>

Download latest CUDA Tookit for L4T into ~/Downloads

Open a terminal on a host computer and type:

```shell
scp ~/Downloads/cuda-repo-l4t* ubuntu@tegra-ubuntu:Downloads/.
```

On a host computer ssh into ubuntu@tegra-ubuntu:

```shell
cd ~/Downloads
sudo dpkg -i cuda-repo-l4t*
sudo apt-get update
# A and B are the latest cuda release numbers e.g. cuda-tookit-6-5
sudo apt-get install cuda-toolkit-A-B
sudo apt-get install cuda-samples-A-B
echo "export PATH=/usr/local/cuda-A.B/bin:$PATH" >> ~/.bashrc
echo "export LD_LIBRARY_PATH=/usr/local/cuda-A.B/lib:$LD_LIBRARY_PATH" >> ~/.bashrc
source ~/.bashrc
# verify the CUDA Toolkit is installed on your device
nvcc -V
```

###Download, Configure, and Build OpenCV

On a host computer ssh into ubuntu@tegra-ubuntu:

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

Select UbuntuARM install instructions for the latest ROS release
(matching Linux 4 Tegra Ubuntu version).

<http://wiki.ros.org/ROS/Installation>

On a host computer ssh into ubuntu@tegra-ubuntu:

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

Download the latest version of FlyCapture for ARM Hard Float into the
host computer ~/Downloads directory.

Open a terminal on a host computer and type:

```shell
scp ~/Downloads/flycapture* ubuntu@tegra-ubuntu:Downloads/.
```

On a host computer ssh into ubuntu@tegra-ubuntu:

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
rosrun pointgrey_camera_driver list_cameras
roslaunch pointgrey_camera_driver camera.launch
```
