+++
title = "Running Sample Applications"
chapter = true
weight = 11
pre = "<b>2. </b>"
+++



## Run the sample application

We are now ready to run the sample applications.  For this workshop we will run the *kvsWebrtcClientMasterGstSample*.

```bash
./kvsWebrtcClientMaster TestChannel
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


