# README for CTRA

## Installation

Start with a (more or less) fresh install of Raspbian. Update and install the following packages:

```bash
#!/bin/bash
sudo apt update && sudo apt upgrade -y
sudo apt-get install -y build-essential cmake git pkg-config emacs-nox tmux htop
sudo apt-get install -y libjpeg-dev libtiff5-dev libjasper-dev libpng-dev
sudo apt-get install -y libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
sudo apt-get install -y libxvidcore-dev libx264-dev
sudo apt-get install -y libfontconfig1-dev libcairo2-dev
sudo apt-get install -y libgdk-pixbuf2.0-dev libpango1.0-dev
sudo apt-get install -y libgtk2.0-dev libgtk-3-dev
sudo apt-get install -y libatlas-base-dev gfortran
sudo apt-get install -y libhdf5-dev libhdf5-serial-dev libhdf5-103
sudo apt-get install -y libqtgui4 libqtwebkit4 libqt4-test python3-pyqt5
sudo apt-get install -y python3-dev
```

## Raspberry pi

Enable the camera by going to `Interface Options` and choosing option `P1 Camera`

```bash
sudo raspi-config
```

## VNC (headless)

Enable VNC (if you are not using the PI as a desktop).

```
sudo apt install -y tightvncserver
```

Run the VNC server after you log in with `ssh`. The first time you run the server, it will ask you for a password.

```
vncserver :1
```

Then connect using RPI-IP-ADDRESS:5901 and entering the password you
configured just previously. If you do not know your ip addres, you can run
`ifconfig` at the command line.

## VNC Client (headless)

**Remmina** is a good Linux based VNC Client. **TightCNV** is a free VNC that also works on Windows. On macOS, you can follow the instructions at <https://raspberrypi.stackexchange.com/questions/59605/access-to-raspberry-pi-vnc-session-from-mac-os-x>.

## Configure Python 3

```bash
#!/bin/bash
wget https://bootstrap.pypa.io/get-pip.py
sudo python3 get-pip.py
sudo rm -rf ~/.cache/pip
sudo python3 -m pip install virtualenv virtualenvwrapper
printf "\n#virtualenv and virtualenvwrapper configuration\nWORKON_HOME=$HOME/.virtualenvs\nVIRTUALENVWRAPPER_PYTHON=/usr/bin/python3\nsource /usr/local/bin/virtualenvwrapper.sh\n" >> ~/.bashrc
source ~/.bashrc
mkvirtualenv ctra -p python3
```

## Configure Python

Create the virtual environment:

```bash
workon ctra
python3 -m pip install --upgrade pip
python3 -m pip install jupyterlab
python3 -m pip install scipy
python3 -m pip install picamera[array]
python3 -m pip install json_minify
python3 -m pip install imutils
python3 -m pip install --upgrade opencv-contrib-python==4.4.0.46
```

## Clone the repository

Clone the repository from GitHub.

```bash
git clone https://github.com/microwerx/uaf-ctra
```

Then go ahead and execute the traffic counter file.

```bash
workon ctra
cd uaf-ctra/traffic_counter
python3 ./traffic_counter.py -c config/config.json -m horizontal
```

You will need to click and hold the mouse button where you want the splits to
occur. While holding down the mouse button, type the letter `s`. Then the
traffic counter will start. There is an *up* and *down* counter. Watch how the
centroid counter looks for new blobs to track. They will start as red and when
they cross and get counted, then they will turn to green. When you are
finished, then press `q` to exit the demo.

## Advanced Steps Follow

## Compile OpenCV 4.5.1

To download OpenCV sources, perform the following steps:

```bash
wget -O opencv.zip https://github.com/opencv/opencv/archive/4.5.1.zip
wget -O opencv_contrib.zip https://github.com/opencv/opencv_contrib/archive/4.5.1.zip
unzip opencv.zip
unzip opencv_contrib.zip
mv opencv-4.5.1 opencv
mv opencv_contrib-4.5.1 opencv_contrib
```

To compile, make sure you have increased the swap space to 2048 or higher.
This is beyond the scope of this README, a simple Google search should help
you out. Make sure to reverse this when you are finished so that you don't
cause too much wear and tear on your SD card.

```bash
workon ctra
mkdir opencv/build
cd opencv/build
cmake -D CMAKE_BUILD_TYPE=RELEASE \
    -D CMAKE_INSTALL_PREFIX=/usr/local \
    -D OPENCV_EXTRA_MODULES_PATH=~/opencv_contrib/modules \
    -D ENABLE_NEON=ON \
    -D ENABLE_VFPV3=ON \
    -D BUILD_TESTS=OFF \
    -D INSTALL_PYTHON_EXAMPLES=OFF \
    -D OPENCV_ENABLE_NONFREE=ON \
    -D CMAKE_SHARED_LINKER_FLAGS=-latomic \
    -D BUILD_EXAMPLES=OFF ..
```


