+++
title = "Amazon Kinesis Video Streams WebRTC Workshop"
chapter = true
weight = 1
+++
This workshop will teach you how to compile the open-source Amazon Kinesis Video Streams WebRTC SDK's utilizing a Cloud9 development environment, allow you to create a producer stream from your Raspberry Pi, iOS or Android device.  You will also learn how to utilize the management console to initiate a WebRTC media session.

## WebRTC Terms

**Signaling channel**
A resource that enables applications to discover, set up, control, and terminate a peer-to-peer connection by exchanging signaling messages. Signaling messages are metadata that two applications exchange with each other to establish peer-to-peer connectivity.   We will establish a single Signaling Channel that our producer (or master) will stream the video to.

**Peer**
Any device or application (for example, a mobile or web application, webcam, home security camera, baby monitor, etc.) that is configured for real-time, two-way streaming through a Kinesis Video Streams with WebRTC. 

**Master**
A peer that initiates the connection and is connected to the signaling channel with the ability to discover and exchange media with any of the signaling channel's connected viewers.  In this example, we will not create a master channel, but it can be created several different ways, either use the webcam on your browser, the multiple KVS SDKs that are provided, or build your own.

**Viewer**
A peer that is connected to the signaling channel with the ability to discover and exchange media only with the signaling channel's master. A viewer cannot discover or interact with other viewers through a given signaling channel. A signaling channel can have up to 10 connected viewers.  In this example, the browser in CMS will be the Viewer.


## Prerequisites

The following hardware and AWS environment are required.

+ AWS environment
+ An IAM user with administrator privileges.
+ To run the CMS demo, you'll need to have CMS installed in your AWS account

Hardware (PC for operation)

A PC (Windows, Mac or Linux) with the following apps installed
+ Browser (Chrome or Firefox)
+ AWS CLI (1.18.49 or higher)
+ Python (3.6 or higher)
+ SSH client (to connect to the Raspberry Pi via SSH)
+ microSD card reader (if you are using a Raspberry Pi)

Hardware to be prepared (Device to connect a camera)
If you want to upload the video of actual camera (using hardware)

+ Raspberry Pi 3 B+ or Raspberry Pi 4
+ 8GB or larger microSD card
+ USB camera or Raspberry Pi camera
+ Instead of a Raspberry Pi, you can use a Mac, Ubuntu or Debian PC. In that case, please replace the word “Raspberry Pi” in the procedure as needed. 

Uploading an existing video feed instead of a camera device (using AWS Cloud9)

+ You can use AWS Cloud9 to proceed without cameras

Required Skills

+ Understanding of basic Linux command line operations (cd, ls, mkdir, etc.)
