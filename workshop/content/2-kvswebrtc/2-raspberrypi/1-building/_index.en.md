+++
title = "Building Sample Applications"
chapter = true
weight = 1
pre = "<b>1. </b>"
+++

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
