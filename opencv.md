# Compile opencv
- Tried on Ubuntu 20.04.

Using this: https://learnopencv.com/install-opencv3-on-ubuntu/

```bash
# update your system
sudo apt update && sudo apt upgrade

# install requirements
sudo apt-get install \
    build-essential checkinstall cmake pkg-config yasm \
    gfortran git\
    libtiff5-dev \
    libavcodec-dev libavformat-dev libswscale-dev libdc1394-22-dev \
    libxine2-dev libv4l-dev \
    libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev \
    qt5-default libgtk2.0-dev libtbb-dev \
    libatlas-base-dev \
    libfaac-dev libmp3lame-dev libtheora-dev \
    libvorbis-dev libxvidcore-dev \
    libopencore-amrnb-dev libopencore-amrwb-dev \
    x264 v4l-utils \
    libprotobuf-dev protobuf-compiler \
    libgoogle-glog-dev libgflags-dev \
    libgphoto2-dev libeigen3-dev libhdf5-dev doxygen \
    python3.8-dev


python3 -m pip install numpy
```

Install CUDA and CUDNN from nvidia website. 
For CUDNN Here I am using the `.deb` files. If 
you are using the tar file, you would need to change 
the paths for `CUDNN_LIBRARY` and `CUDNN_INCLUDE_DIR` in 
the following command.

If you are installing using the `.deb` file, the 
`dpkg -L libcudnn8` is a useful command to find out
where the library and header files are installed.

```bash
git clone git@github.com:opencv/opencv_contrib.git && cd opencv_contrib && 	git checkout 4.5.0
cd ..
git clone git@github.com:opencv/opencv.git && cd opencv && git checkout 4.5.0

mkdir build && cd build


reset
INSTALL_DIR=install
rm -rf ./* && \
cmake -D CMAKE_BUILD_TYPE=RELEASE \
      -D CMAKE_INSTALL_PREFIX=$INSTALL_DIR \
      -D INSTALL_C_EXAMPLES=ON \
      -D INSTALL_PYTHON_EXAMPLES=ON \
      -D WITH_TBB=ON \
      -D WITH_V4L=ON \
      -D WITH_QT=ON \
      -D WITH_OPENGL=ON \
      -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
      -D BUILD_EXAMPLES=ON \
      -D WITH_CUDA=ON \
      -D WITH_CUDNN=ON \
      -D CUDNN_LIBRARY=/usr/lib/x86_64-linux-gnu/libcudnn.so \
      -D CUDNN_INCLUDE_DIR=/usr/include/ \
      -D PYTHON3_EXECUTABLE=$(python3 -c "import sys; print(sys.executable)") \
      ..


make -j`nproc` install

# find the python binary file location
CV_PYTHON=`find $INSTALL_DIR -type f -name "cv2*.so"` && echo $CV_PYTHON
CV_PYTHON=`dirname $(realpath $CV_PYTHON)` && echo $CV_PYTHON

# add its path to the PYTHONPATH
echo 'export PYTHONPATH=$PYTHONPATH:'"$CV_PYTHON" >> ~/.bashrc
source ~/.bashrc
```

Test your installation
```bash
python -c "import cv2; print(cv2.__version__)"
```