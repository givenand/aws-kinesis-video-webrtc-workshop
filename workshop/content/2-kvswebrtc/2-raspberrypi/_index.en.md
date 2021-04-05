+++
title = "Using a Raspberry Pi 4"
chapter = true
weight = 2
pre = "<b>2. </b>"
+++

{{% notice info %}}
Services used in this workshop will result in charges to your AWS bill.  Please review the AWS pricing pages for more information.
{{% /notice %}}

These labs assume that you have successfully installed the latest version of the Raspbian OS, have connected your Raspberry Pi to a network that is available to you, and that you are able to remotely access your Raspberry Pi either via SSH or VNC.

![Raspberry Pi Overview](/images/RaspberryPiOverview.png)

## Hardware BOM

This portion of the workshop was tested around the use of the Raspberry Pi 4. You can acquire your own hardware either directly through the [Raspberry Pi website](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/), or you can purchase a starter kit from [Amazon.com](https://www.amazon.com/CanaKit-Raspberry-4GB-Starter-Kit/dp/B07V5JTMV9/ref=sr_1_5?dchild=1&keywords=raspberry+pi+v4&qid=1617645178&sr=8-5).

There are a wide array of Camera modules available today for the Raspberry Pi. For this workshop, we have tested the [Camera Module V2](https://www.raspberrypi.org/products/camera-module-v2/), although the instructions provided here should work with any Raspberry Pi CSI port ribbon cable camera module.

## Raspberry Pi Setup

Full instructions for configuring your Raspberry Pi device are outside of the scope of this workshop. If you need help setting up your Raspberry Pi, please follow the instructions from the Raspberry Pi documentation: https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up



If you are going to use a Raspberry Pi Camera for this portion of the workshop, please follow these instructions to first ensure that your camera is configured correctly: https://picamera.readthedocs.io/en/latest/quickstart.html
