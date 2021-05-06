+++
title = "Connected Mobility Solution + WebRTC"
date = 2021-03-30T08:47:41-04:00
weight = 4
chapter = true
pre = "<b>4. </b>"
+++

In this section of the workshop, you will learn how to integrate your WebRTC stream from your iOS or Android application into the [AWS Connected Mobility Solution](https://aws.amazon.com/automotive/solutions/connected-mobility/). 

## Create Signaling Channel 

The signaling channel will be our convergence point for the master and viewer to negotiate and begin streaming live video.  We will create the signaling channel in the AWS Kinesis Video Streams console.

1. In the AWS Console, select *Kinesis Video Streams* then select *Signaling Channels.*
2. Select *Create Signalling Channel.* In Signaling channel name, let’s name it `cmsDemo`
3. Click *Create Signalling Channel*

## Setup IAM User

As with all AWS services, Kinesis Video Streams utilizes AWS Identity and Access Management (IAM) to provide access to its services.  (https://docs.aws.amazon.com/kinesisvideostreams/latest/dg/akvs-release-notes.rss) By using IAM with Amazon Kinesis Video Streams, you can control whether users in your organization can perform a task using specific Kinesis Video Streams API operations and whether they can use specific AWS resources. For this example, we will setup a user with a KVS viewer policy.

1. Create a new policy called `KVSViewerPolicy`.  Replace the account number with your account number (and region, if necessary).  If you named your signalling channel cmsDemo no other changes should be necessary.
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "kinesisvideo:GetSignalingChannelEndpoint",
                "kinesisvideo:GetIceServerConfig",
                "kinesisvideo:GetDataEndpoint",
                "kinesisvideo:ConnectAsViewer",
                "kinesisvideo:DescribeSignalingChannel"
            ],
            "Resource": [
                "arn:aws:kinesisvideo:us-west-2:REPLACE:channel/cmsDemo/*",
                "arn:aws:kinesisvideo:us-west-2:REPLACE:stream/cmsDemo/*"
            ]
        }
    ]
}
```
2. Create a new user called `kvsviewer` and attach the KVSViewerPolicy to this user.  We will use programmatic access for this user to keep things simple so we will need to download the Access Key and Secret Access Key to be used later in this guide.

## Setup Producer of video stream

There are several ways that AWS provides to produce the video pushing into the signalling channel as discussed and demonstrated through earlier steps in this workshop.  Select one of these methods, iOS, Raspberry Pi and change the signaling channel to `cmsDemo`

## Modifications to CMS

The Connected Mobility Solution is a git repository provided by AWS as a solution to our customers and partners.  It provides out of the box fleet management capabilities and this guide demonstrates how we can modify the base UI to include a live video component to the fleet manager UI by utilizing Kinesis Video Streams.  Subsequent versions of this document will show how to save media to KVS based on a detected event.

This guide will operate under the assumption the end user has a development environment on Cloud9 setup for CMS and the latest version of CMS is available to begin editing the base code.  This code work should be done on a separate branch and not committed back to the main repository in case you are pulling from the cdf-auto-solution main repository.


1. Open up your Cloud9 CMS instance and navigate to  cdf-auto-solution/source/packages/fleetmanager-ui  
2. The user might need adjust security group settings on the Cloud9 EC2 instance to allow port 3000 incoming from 0.0.0.0/0
3. The public/assets/appVariables.js provides deployment-specific variables and bindings for the UI. These values can be discovered from a deployment using scripts or inspecting CloudFormation outputs.  They will need to be modified to your deployment’s unique values to develop the UI locally.

```
export user_pool_id=$(aws cloudformation list-exports --query "Exports[?Name=='cms-$env_name-userPoolId'].Value" --output text)
export user_pool_client_id=$(aws cloudformation list-exports --query "Exports[?Name=='cms-$env_name-userPoolClientId'].Value" --output text) 
export cdf_auto_endpoint=$(aws cloudformation list-exports --query "Exports[?Name=='cms-$env_name-fleetmanager-apiGatewayUrl'].Value" --output text)
```
```
echo $user_pool_id
echo $user_pool_client_id 
echo $cdf_auto_endpoint
```

4. Install the Node modules
```
cd cdf-auto-solution/source/packages/fleetmanager-ui

npm install

npm install aws-sdk
npm install @material-ui/styles
npm install mapbox-gl
npm install viewport-mercator-project

npm install amazon-kinesis-video-streams-webrtc
```

5. Start the project locally
```
npm start
```
6. Once it starts successfully you will see the following.  Then you can go to *Preview* on the top toolbar and select *Preview Running Application*

![Success](/images/compiled.png)

7. Now you are ready to make some modifications to the Node Typescript files.  The first thing to do is create the *View Live Video* button.  This will replace the RightSidebar.jsx code found in the src/components/sidebars directory. with the code found below.
8. Find the three lines where we are defining the opts and replace all the values with the `SECRET_ACCESS_KEY` and `ACCESS_KEY` and `REGION` from the kvsviewer user you created earlier in this guide.  
9. Next step is to create a new file called KVSPlayerModal.jsx under `src/components/global`.  This will be the Modal that will contain all of our code for our viewer.
10. Copy/Paste the code from below for the KVSPlayerModal.jsx into that file.
11. At this point, start your producer as a master stream to begin streaming video to the `cmsDemo` signalling channel.  Now open up the Fleet Manager UI, select a VIN and click on View Live Video.

```
npm start
```
