<img align="left" width="120" height="120" src="https://avatars0.githubusercontent.com/u/15658638?s=200&v=4">

### TensorFlow Lite : native compilation on Raspberry Pi Zero W

An addendum to the native compile section of https://www.tensorflow.org/lite/guide/build_rpi for building Tensorflow Lite 1.14.0 on a Raspberry Pi Zero W arm6 target.

Clone this repo and move the files to the relevant paths on the Raspiberry Pi Zero as shown below with the `scp` commands

```sh
# From https://www.tensorflow.org/lite/guide/build_rpi > "Compile natively on Raspberry Pi" section...
    (rpi) $ sudo apt-get install build-essential

    # Clone the repo onto the Pi Zero
    (rpi) $ cd ~
    (rpi) $ git clone https://github.com/tensorflow/tensorflow.git
    (rpi) $ git checkout v1.14.0
    (rpi) $ cd ./tensorflow
    or download it directly from https://github.com/tensorflow/tensorflow/releases/tag/v1.14.0

    # Install the dependencies
    (rpi) $ ./tensorflow/lite/tools/make/download_dependencies.sh

# FTP/scp the arm6 build script to /home/pi/tensorflow/tensorflow/lite/tools/make
(host)$ scp <local_path_to>/build_rpi_armv6_lib.sh pi@[pi's ip]:<rpi_path_to>/tensorflow/tensorflow/lite/tools/make

# FTP/scp the updated Makefile that builds the label_image example as well
(rpi) $ cd <path_to>/tensorflow/tensorflow/lite/tools/make
(rpi) $ mv Makefile Makefile.orig
(host)$ scp <local_path_to>/Makefile pi@[pi's ip]:<rpi_path_to>/tensorflow/tensorflow/lite/tools/make
(rpi) $ cd <path_to>/tensorflow/tensorflow/lite/tools/make/target
(rpi) $ mv rpi_makefile.inc rpi_makefile.inc.orig
(host)$ scp <local_path_to>/rpi_makefile.inc pi@[pi's ip]:<rpi_path_to>/tensorflow/tensorflow/lite/tools/make/target

# FTP/scp the updated  that builds the label_image example as well
(host)$ scp <local_path_to>/Makefile pi@[pi's ip]:<rpi_path_to>/tensorflow/tensorflow/lite/tools/make
# Increase the RPi swap size otherwise 'make' will fail 
(rpi) $ sudo nano /etc/dphys-swapfile
...CONF_SWAPSIZE=100 --> CONF_SWAPSIZE=2048

(rpi) $ sudo /etc/init.d/dphys-swapfile stop
(rpi) $ sudo /etc/init.d/dphys-swapfile start

# Build...allow ~6 hours on a RPi Zero W...often needing restarts!
(rpi) $ cd /home/pi/tensorflow
(rpi) $ ./tensorflow/lite/tools/make/build_rpi_armv6_lib.sh

# Change the swap size back to the default setting
(rpi) $ sudo nano /etc/dphys-swapfile
...CONF_SWAPSIZE=2048 --> CONF_SWAPSIZE=100

(rpi) $ sudo /etc/init.d/dphys-swapfile stop
(rpi) $ sudo /etc/init.d/dphys-swapfile start
```

The output from the above should be error-free and result in `minimal`, `benchmark` and `label_image` executables placed in  `../tensorflow/tensorflow/lite/tools/make/gen/rpi_armv6/bin` ...plus `libtensorflow-lite.a` and ` benchmark-lib.a` placed in `../tensorflow/tensorflow/lite/tools/make/gen/rpi_armv6/lib`

Test the build using a `.tflite` model (see https://www.tensorflow.org/lite/models/object_detection/overview) by running `./minimal <model_file_name>.tflite`

This should generate a summary of the model architecture on stdout. If this isn't the case, check the build output for errors.

Assuming `./minimal` produces the expected output, run the `label_image` example as below...

```sh
# Copy required files for the label_image example to the Rasperry Pi Zero
(host)$ scp <local_path_to>/labels.txt pi@<rpi_ip_addr>:<rpi_path_to>/tensorflow/tensorflow/lite/tools/make/gen/rpi_armv6/bin
(host)$ scp <local_path_to>/mobilenet_quant_v1_224.tflite pi@<rpi_ip_addr>:<rpi_path_to>/tensorflow/tensorflow/lite/tools/make/gen/rpi_armv6/bin
(host)$ scp <local_path_to>/grace_hopper.bmp pi@<rpi_ip_addr>:<rpi_path_to>/tensorflow/tensorflow/lite/tools/make/gen/rpi_armv6/bin

# if you need to (re)build the label_image example...
(rpi) $ ./tensorflow/lite/tools/make/build_rpi_armv6_label_image.sh

# Run the example : defaults to the image, tflite model and labels files downloaded above
(rpi) $ ./label_image
```

This will run the Mobilenet-based object classifier on the image of Grace Hopper and return the top 5 list of detected objects with a confidence value plus the overall inference time. 
Use `./label_image --help` to see the command line options.

Happy TensorFlow-Lite-development-on-arm6-devices!
