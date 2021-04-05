+++
title = "Building Sample Applications"
chapter = true
weight = 1
pre = "<b>1. </b>"
+++



## Installing the library dependencies



Execute the following commands to update the package list and install the libraries needed to build the SDK and sample applications.

```bash
sudo apt update
sudo apt install -y cmake gstreamer1.0-plugins-bad gstreamer1.0-plugins-good gstreamer1.0-plugins-ugly gstreamer1.0-tools libgstreamer-plugins-base1.0-dev
```

## Clone the Github repository


```git
git clone --recursive https://github.com/awslabs/amazon-kinesis-video-streams-webrtc-sdk-c.git
```


## Configure and create build directory

```bash
mkdir -p amazon-kinesis-video-streams-webrtc-sdk-c/build; cd amazon-kinesis-video-streams-webrtc-sdk-c/build; cmake ..
```

## Build the library and sample applications

```bash
make
```

## Export environment variables

Export the environment variables obtained in the Getting Started section.
```
export AWS_ACCESS_KEY_ID= <AWS account access key>
export AWS_SECRET_ACCESS_KEY= <AWS account secret key>
```

Optionally, set AWS_SESSION_TOKEN if integrating with temporary token
```
export AWS_SESSION_TOKEN=<session token>
```

Region is optional, if not being set, then us-west-2 will be used as default region.
```
export AWS_DEFAULT_REGION= <AWS region>
```
