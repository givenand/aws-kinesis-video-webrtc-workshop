+++
title = "Using AWS Cloud9 as a WebRTC Producer"
chapter = true
weight = 1
+++

In this part of the workshop, you will use the the AWS Cloud9 instance you setup in the Getting Started portion of this workshop to build and run the KVS WebRTC sample applications. While there will not be a camera attached to your Cloud9 instance, the sample application was designed to generate a mock video stream, so you will still be able to view that video stream and learn how to build your WebRTC based applications using the C SDK.

![Cloud9 Overview](/images/Cloud9Overview.png)

# Build the Sample Applications

## Installing the library dependencies

Execute the following commands in the Cloud9 console to update the package list and install the libraries needed to build the SDK and sample applications.

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

Setup gstreamer on your Cloud9 Instance

First install Java and set the path.


```
sudo apt-get install -y openjdk-8-jdk
sudo apt-get install -y default-jdk
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

The platform is ready to stream a sample video from your Cloud9 instance.  If you want to setup other producers click [Next](/en/2-kvswebrtc/2-building-raspberrypi.html), otherwise go to [Running Cloud9 + Pi Applications](/en/2-kvswebrtc/4-running.html)