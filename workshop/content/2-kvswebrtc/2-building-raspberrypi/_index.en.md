+++
title = "Using a Raspberry Pi 4"
chapter = true
weight = 2
pre = "<b>2. </b>"
+++

The following steps assume that you have successfully installed the latest version of the Raspbian OS, have connected your Raspberry Pi to a network that is available to you, and that you are able to remotely access your Raspberry Pi either via SSH or VNC.

The following diagram illustrates how you will interact with your device as well as with AWS Cloud resources. 
![Raspberry Pi Overview](/images/RaspberryPiOverview.png)

## Hardware BOM

This portion of the workshop was tested around the use of the Raspberry Pi 4. You can acquire your own hardware either directly through the [Raspberry Pi website](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/), or you can purchase a starter kit from [Amazon.com](https://www.amazon.com/CanaKit-Raspberry-4GB-Starter-Kit/dp/B07V5JTMV9/ref=sr_1_5?dchild=1&keywords=raspberry+pi+v4&qid=1617645178&sr=8-5).

There are a wide array of Camera modules available today for the Raspberry Pi. For this workshop, we have tested the [Camera Module V2](https://www.raspberrypi.org/products/camera-module-v2/), although the instructions provided here should work with any Raspberry Pi CSI port ribbon cable camera module.

## Raspberry Pi Setup

Full instructions for configuring your Raspberry Pi device are outside of the scope of this workshop. If you need help setting up your Raspberry Pi, please follow the instructions from the Raspberry Pi documentation: https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up

If you are going to use a Raspberry Pi Camera for this portion of the workshop, please follow these instructions to first ensure that your camera is configured correctly: https://picamera.readthedocs.io/en/latest/quickstart.html

# Build the Sample Applications

Once you have your Raspberry Pi device configured and connected to the network, you can remotely access the device either via Secure Shell or VNC in order to install the KVS WebRTC SDK dependencies, and then build the sample applications.

## Installing the library dependencies

Using either a Secure Shell session, or using the Terminal application on the Raspberry Pi, you will need to execute the following commands to update the package list and install the libraries needed to build the KVS WebRTC SDK and sample applications.

```
sudo apt update
sudo apt install -y \
  git \
  pkg-config \
  cmake \
  zip \
  libssl-dev \
  libcurl4-openssl-dev \
  liblog4cplus-dev \
  libgstreamer1.0-dev \
  libgstreamer-plugins-base1.0-dev \
  gstreamer1.0-plugins-base-apps \
  gstreamer1.0-plugins-bad \
  gstreamer1.0-plugins-good \
  gstreamer1.0-plugins-ugly \
  gstreamer1.0-tools
```

## Clone and Build the SDK Sample Applications

```git
git clone --recursive https://github.com/awslabs/amazon-kinesis-video-streams-webrtc-sdk-c.git
```

Configure and create build directory:
```
mkdir -p amazon-kinesis-video-streams-webrtc-sdk-c/build
cd amazon-kinesis-video-streams-webrtc-sdk-c/build
cmake ..
```

Build the library and sample applications:
```
make
```
