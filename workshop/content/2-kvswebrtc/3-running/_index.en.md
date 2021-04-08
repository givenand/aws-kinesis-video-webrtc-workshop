+++
title = "Running Sample Applications"
chapter = true
weight = 3
+++

The steps for running the sample application are the same regardless of where you are running them. If you are running the sample applications on Cloud9, you will follow the below instructions by executing the given commands on the Cloud9 console. If you are using a physical device such as a Raspberry Pi, then you will execute the given commands on the Raspberry Pi.

## Set the required Environment Variables

Export the environment variables obtained in the Getting Started section.
```
export AWS_ACCESS_KEY_ID=<AWS account access key>
export AWS_SECRET_ACCESS_KEY=<AWS account secret key>
```

Optionally, set AWS_SESSION_TOKEN if integrating with temporary token
```
export AWS_SESSION_TOKEN=<session token>
```

Region is optional, and if not being set will default to the us-west-2 region.
```
export AWS_DEFAULT_REGION=<AWS region>
```

## Run the sample application

We are now ready to run the sample applications.  For this workshop we will run the `kvsWebrtcClientMasterGstSample`.

```bash
./kvsWebrtcClientMasterGstSample TestChannel
```


If you see text that looks like below, then you are ready to stream video.


```bash
[KVS GStreamer Master] Signaling client created successfully
[KVS GStreamer Master] Signaling client connection to socket established
[KVS Gstreamer Master] Beginning streaming...check the stream over channel TestChannel
```

## Stream video using the AWS Management Console


Open the AWS Management Console and navigate to the Kinesis Video Streams service and select **Signaling channels** from the panel on the left of the screen.


![KVSDashboard](/images/KVSConsoleSignalingClicked.png)


Click on **TestChannel** in the Signaling channels selection

![KVSTestChannel](/images/KVSConsoleSignalingChannels.png)


Expand the section titled **Media playback viewer**

![KVSMediaPlayback](/images/StreamingVideoPlay.png)


Click on the Play icon to start a streaming session.  You can also expand the **Viewer statistics** section to view details about the media stream.

![KVSMediaPlaying](/images/StreamingVideoWithStats.png)
