+++
title = "Using an iOS Device"
chapter = true
weight = 2.3
+++

The following steps assume that you have successfully installed the latest version of the Raspbian OS, have connected your Raspberry Pi to a network that is available to you, and that you are able to remotely access your Raspberry Pi either via SSH or VNC.


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
