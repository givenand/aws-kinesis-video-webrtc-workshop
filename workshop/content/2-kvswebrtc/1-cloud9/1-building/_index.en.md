+++
title = "Building Sample Applications"
chapter = true
weight = 1
pre = "<b>1. </b>"
+++



## Installing the library dependencies



Execute the following commands to update the package list and install the libraries needed to build the SDK and sample applications.

```
sudo apt update
sudo apt install -y \
  cmake \
  gstreamer1.0-plugins-bad \
  gstreamer1.0-plugins-good \
  gstreamer1.0-plugins-ugly \
  gstreamer1.0-tools \
  libgstreamer-plugins-base1.0-dev
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
