# Compile and install Pangolin

https://github.com/stevenlovegrove/Pangolin#required-dependencies

```bash
sudo apt install libgl1-mesa-dev \
    libglew-dev \
    cmake \
    libpython3.8-dev \
    ffmpeg libavcodec-dev libavutil-dev libavformat-dev libswscale-dev libavdevice-dev \
    libdc1394-22-dev libraw1394-dev \
    libjpeg-dev libpng++-dev libtiff5-dev libopenexr-dev

git clone https://github.com/stevenlovegrove/Pangolin.git
cd Pangolin
git submodule init && git submodule update
mkdir build && cd build-
cmake -DCMAKE_BUILD_TYPE=Debug .. -DPYTHON_EXECUTABLE=$(python3 -c "import sys; print(sys.executable)") -DCMAKE_INSTALL_PREFIX=install
make -j`nproc` install

echo -e "\n\nexport PYTHONPATH=\$PYTHONPATH:`realpath src`" >> ~/.bashrc

# for jetson you would need the following as well
export LD_PRELOAD=/usr/lib/aarch64-linux-gnu/libgomp.so.1
# or 
echo -e "\nexport LD_PRELOAD=/usr/lib/aarch64-linux-gnu/libgomp.so.1" >> ~/.bashrc
source ~/.bashrc
```