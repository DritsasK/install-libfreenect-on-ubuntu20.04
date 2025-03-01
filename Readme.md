Last week i got myself the Kinect v1 camera (a used one), and i wanted to connect it to my computer and play a bit. My system was an Ubuntu 20. I installed libfreenect library (with Python 2 wrapper), which is actually a driver for the Microsoft Kinect. It runs on Linux, OSX and Windows. I wanted to share with you how i managed to install the libfreenect library. Below are the steps i followed. I have already installed python 2.7.18 on my system.

1. Update The System

sudo apt update && sudo apt upgrade -y

2. Install Dependencies

sudo apt install build-essential libusb-1.0-0-dev freeglut3-dev libxmu-dev libxi-dev

3. Install cython

sudo apt-get install cython3 cython3-dev

4. Install cmake version 3.12.4 (i build from source, because i saw in CMakeLists.txt located in libfreenect/wrappers/python the cmake minimum required version is 3.12.4 most likely the version from the Ubuntu repo works fine as well)

sudo apt install -y build-essential libssl-dev
wget https://github.com/Kitware/CMake/releases/download/v3.12.4/cmake-3.12.4.tar.gz
tar -xvzf cmake-3.12.4.tar.gz
cd cmake-3.12.4
./bootstrap
make -j$(nproc)
sudo make install

5. Clone the libfreenect Repository (github) and build

git clone https://github.com/OpenKinect/libfreenect.git
cd libfreenect
mkdir build
cd build
cmake .. -DBUILD_PYTHON2=ON -DCYTHON_EXECUTABLE=/usr/bin/cython3 -DPYTHON_EXECUTABLE=/usr/bin/python2
make -j$(nproc)
sudo make install
sudo ldconfig

6. Install Python 2 Wrapper

cd ../wrappers/python
sudo python2 setup.py install

...and then i got the error below...  :(

running install
running build
running build_ext
There is a workaround to now inherit optimization CFLAGS when compiling wheels.
To enable this, set APPLY_LP2002043_UBUNTU_CFLAGS_WORKAROUND in your
environment. See LP: https://launchpad.net/bugs/2002043 for further context.
APPLY_LP2002043_UBUNTU_CFLAGS_WORKAROUND not detected.
cythoning freenect.pyx to freenect.c
/usr/lib/python2.7/dist-packages/Cython/Compiler/Main.py:369: FutureWarning: Cython directive 'language_level' not set, using 2 for now (Py2). This will change in a later release! File: /home/kostas/libfreenect/wrappers/python/freenect.pyx
  tree = Parsing.p_module(s, pxd, full_module_name)

Error compiling Cython file:

    dev_out.ctx = ctx
    return dev_out

_depth_cb, _video_cb = None, None

cdef void depth_cb(freenect_device *dev, void *data, uint32_t timestamp) noexcept with gil:
                                                                        ^

freenect.pyx:324:73: Syntax error in C variable declaration
building 'freenect' extension
x86_64-linux-gnu-gcc -pthread -fno-strict-aliasing -Wdate-time -D_FORTIFY_SOURCE=2 -g -fdebug-prefix-map=/build/python2.7-QOVJtE/python2.7-2.7.18=. -fstack-protector-strong -Wformat -Werror=format-security -fPIC -I/usr/include/python2.7 -c freenect.c -o build/temp.linux-x86_64-2.7/freenect.o -fPIC -I ../../include/ -I /usr/include/libusb-1.0/ -I /usr/local/include/libusb-1.0 -I /usr/local/include -I ../c_sync/ -I /usr/lib/python2.7/dist-packages/numpy/core/include
freenect.c:1:2: error: #error Do not use this file, it is the result of a failed Cython compilation.
    1 | #error Do not use this file, it is the result of a failed Cython compilation.
      |  ^~~~~
error: command 'x86_64-linux-gnu-gcc' failed with exit status 1

The solution i found myself was to erase all keywords 'noexcept' from file libfreenect/wrappers/python/freenect.pyx. Saved the file and re-run sudo python2 setup.py install. 
And its done ! Meaby there was another solution(s) in Ubuntu forums, but i did not search it a lot, i was happy that worked for me (i installed afterwards libfreenect with python wrappers using python3 and i did not get the error above).

7. Verify installation

python [enter]
import freenect

I got no errors. freenect was imported sussesfully. Happy coding !
