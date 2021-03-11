# Compile Realsense for Jetson Nano
```bash
# set the location as you like, where do you want to clone the repo?
REALSENSE_DIR=~/librealsense
REALSENSE_DIR=`realpath $REALSENSE_DIR` # to get the absolute path of directory
git clone https://github.com/IntelRealSense/librealsense.git $REALSENSE_DIR
cd $REALSENSE_DIR
```

We need to add the following line to the beginning of `librealsense/CMake/install_config.cmake` 
to make the `install` directory movable that all executables can be executed from everywhere
in the filesystem. If you do not need to move the install directory, 
you can skip changing the cmake file
```cmake
set(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_RPATH}:\$ORIGIN:\$ORIGIN/../lib:\$ORIGIN/../include")
```

then we need to follow

```bash
cd $REALSENSE_DIR

# to make sure you have an empty build directory
# Otherwise the binaries would get compiled 
# for wrong python version
rm -rf build 
mkdir build && cd build

# dependencies
sudo apt-get install \
    xorg-dev \
    libxinerama-dev \
    python3 \
    python3-dev \
    libpython3.6-dev

cmake \
    -DCMAKE_BUILD_TYPE=debug \
    -DCMAKE_INSTALL_PREFIX=$REALSENSE_DIR/install_host \
    -DPYTHON_INSTALL_DIR=$REALSENSE_DIR/install_host/python \
    -DBUILD_EXAMPLES=true \
    -DFORCE_LIBUVC=true \
    -DBUILD_WITH_CUDA=true \
    -DBUILD_PYTHON_BINDINGS=bool:true \
    -DPYBIND11_INSTALL=ON \
    -DCMAKE_CUDA_COMPILER=/usr/local/cuda/bin/nvcc \
    -DPYTHON_EXECUTABLE=$(python3 -c "import sys; print(sys.executable)") \
    ../

# time to compile and install in the specified directory in `$REALSENSE_DIR/install_host`
make -j`nproc` install
```

`-DCMAKE_INSTALL_PREFIX=$REALSENSE_DIR/install_host` and
`-DPYTHON_INSTALL_DIR=$REALSENSE_DIR/install_host/python` is to  make sure that cmake
changes the install directory to a defined location so we would know where are they 
(rather than default version of Ubuntu) and also
to be able to copy it into jetson in case of cross-compiling (read more if curious :) ).

`-DPYTHON_EXECUTABLE=$(python3 -c "import sys; print(sys.executable)")` to make sure 
we are compiling for python3 rather than 2.

If you need to compile for different python versions, you need to install that specific python 
version and then use for example `python3.7` at the last line of above cmake. Then binary
file that is compiled for python 3.6 would not work with python 3.7.

Now we need to add the `PYTHON_INSTALL_DIR` specified above to `PYTHONPATH` for example like this:
```bash
echo '# Intel Realsense PYTHON PATH' >> ~/.bashrc
echo 'PYTHONPATH=$PYTHONPATH:'"$REALSENSE_DIR"'/install_host/python' >> ~/.bashrc
source ~/.bashrc
```

To check that your python installation is correct, the following line should not create
any error message. If it shows error message, that means you have some problems.
```bash
cd
python3 -c 'import pyrealsense2 as rs'
```


Now we need to apply udev rules to be able to access the camera without `sudo`:
```
cd $REALSENSE_DIR
sudo cp config/99-realsense-libusb.rules /etc/udev/rules.d/
sudo udevadm control --reload-rules && udevadm trigger
```

helpful extra resource: https://www.jetsonhacks.com/2019/12/22/install-realsense-camera-in-5-minutes-jetson-nano/

## Cross Compile for Jetson Nano on Ubuntu Host
If you want to cross compile for Jetson nano on host ubuntu machine, first follow the steps in [`cross_compile.md`](https://github.com/yosoufe/SelfStudyRobotics/blob/master/hardware/cross_compile.md) and then follow the previous 
section but on host and emulated ARM environment.

After that we need to test our cross compiled binary directory.

Now lets test one of the example binaries. Open another terminal with your default user,
(not in the emulated environment)
```bash
sudo su
export WS=/home/yousof/robotics/jetson/cross_compile
cd $WS/root/
sudo scp -r $REALSENSE_DIR/install_host <your_jetson_user>@<your_jetson_ip>:/home/<your-user-on-jetson>/libs/librealsense
```

now ssh to the jetson. and run one of the examples like
```bash
ssh <your_jetson_id>@<your_jetson_ip>
cd ~/libs/librealsense/bin
sudo ./rs-depth
```

Yay :)

### To run everything without sudo
To make it work without sudo, run the followings on jetson while there
is no `realsense` sensor connected to your device.
```bash
# first disconnect all realsense sensors from jetson
cd ~/libs/librealsense
sudo chown -R `id -nu`:`id -ng` install_host
sudo wget -O /etc/udev/rules.d/99-realsense-libusb.rules https://raw.githubusercontent.com/IntelRealSense/librealsense/master/config/99-realsense-libusb.rules
sudo udevadm control --reload-rules && udevadm trigger
```

Now you should be able to run all realsense examples in jetson without `sudo`.

### Source:
- https://github.com/jetsonhacks/installRealSenseSDK