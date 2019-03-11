<img align="left" width="120" height="120" src="https://avatars0.githubusercontent.com/u/15658638?s=200&v=4">

### TensorFlow Lite : native compilation on Raspberry Pi Zero W

An addendum to the native compile section of https://www.tensorflow.org/lite/guide/build_rpi for building Tensorflow Lite on a Raspberry Pi Zero W arm6 target rather than a Raspberry Pi 2+ arm7l target


```sh
# Install the pre-reqs
(rpi) $ sudo apt-get install build-essential

# Clone the repo onto the Pi Zero
(rpi) $ cd ~
(rpi) $ git clone https://github.com/tensorflow/tensorflow.git
(rpi) $ cd ./tensorflow

# Install the dependencies
(rpi) $ ./tensorflow/lite/tools/make/download_dependencies.sh

# FTP/scp the arm6 build script to /home/pi/tensorflow/tensorflow/lite/tools/make
(host)$ scp <local_path_to>/build_rpi_armv6_lib.sh pi@192.168.0.1:<rpi_path_to>/tensorflow/tensorflow/lite/tools/make

# Updated Makefile disables NNAPI to avoid "undefined reference to `NnApiImplementation()'" references
(rpi) $ cd <path_to>/tensorflow/tensorflow/lite/tools/make
(rpi) $ mv Makefile Makefile.orig
(host)$ scp /<local_path_to>/Makefile pi@192.168.0.1:<rpi_path_to>/tensorflow/tensorflow/lite/tools/make

# Increase the RPi swap size otherwise 'make' will fail 
(rpi) $ sudo nano /etc/dphys-swapfile
...CONF_SWAPSIZE=100 --> CONF_SWAPSIZE=2048

(rpi) $ sudo /etc/init.d/dphys-swapfile stop
(rpi) $ sudo /etc/init.d/dphys-swapfile start

# Build...allow ~3 hours
(rpi) $ cd /home/pi/tensorflow
(rpi) $ ./tensorflow/lite/tools/make/build_rpi_armv6_lib.sh

# Change the swap size back to the default setting
(rpi) $ sudo nano /etc/dphys-swapfile
...CONF_SWAPSIZE=100 --> CONF_SWAPSIZE=2048

(rpi) $ sudo /etc/init.d/dphys-swapfile stop
(rpi) $ sudo /etc/init.d/dphys-swapfile start
```

