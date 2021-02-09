+++
title = "Obtaining credentials"
weight = 13
+++



## Obtaining AWS Credentials


Credentials are required for the sample applications to communicate with the KVS Service.  For the purpose of this workshop, we will be using static credentials.  However, a real solution would utilize a more secure and scalable method.  Fortunately, AWS IoT offers a solution using X.509 device certificates. 

Please select a step for the AWS environment you use.

### A) If you are using your own AWS account.

Use the AWS CLI to get temporary credentials to upload the video.

Note that if you have not already set up the AWS CLI on **the PC for operation**, follow the steps:
[Installing the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html) and
[Configuring the AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html).

In the terminal on the **PC for operation,** run the following commands.

```bash
aws sts get-session-token
```

You will get the following results, so we will set this value to the Raspberry Pi's environment variable.

```json
{
    "Credentials": {
        "AccessKeyId": "...",
        "SecretAccessKey": "...",
        "SessionToken": "...",
        "Expiration": "2020-01-01T00:00:01Z"
    }
}
```

Next, open **Raspberry Pi terminal** and execute the following commands.

```bash
export AWS_DEFAULT_REGION="The region you use (e.g. ap-northeast-1, us-west-2)"
export AWS_ACCESS_KEY_ID="The AccessKeyId value of the result above"
export AWS_SECRET_ACCESS_KEY="The SecretAccessKey value of the result above"
export AWS_SESSION_TOKEN="The SessionToken value of the result above"
```

{{% notice "note" %}}
In this workshop, the AWS CLI is used to get temporary credentials of the IAM user to simplify the procedure,
but it is difficult to do this every time on a real camera device.
In a real-world use case, you can use a client certificate managed by AWS IoT to get the credentials.
[How to Eliminate the Need for Hardcoded AWS Credentials in Devices by Using the AWS IoT Credentials Provider](https://aws.amazon.com/jp/blogs/security/how-to-eliminate-the-need-for-hardcoded-aws-credentials-in-devices-by-using-the-aws-iot-credentials-provider/)
{{% /notice %}}

### B) If you are using an account by AWS Event Engine

- Copy the credentials like below from `Team Dashboard` > `AWS Console` of [Event Engine](https://dashboard.eventengine.run) and paste them to **Raspberry Pi terminal**

```bash
export AWS_DEFAULT_REGION=...
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
export AWS_SESSION_TOKEN=...
```

## Uploading to a video stream

Connect the USB camera to the Raspberry Pi.

Next, execute the following commands on the Raspberry Pi terminal.

```bash
cd ~/amazon-kinesis-video-streams-producer-sdk-cpp/build
./kvs_gstreamer_sample kvs-workshop-stream
```

If you get an error, make sure you have set the AWS credentials as the environment variables and the stream name (`kvs-workshop-stream`) is correct.

This video stream will be used in the following steps, leave the command running, or rerun it when you run the next step.

## Checking the video stream

- Open [Amazon Kinesis Video Streams console](https://console.aws.amazon.com/kinesisvideo/home#/dashboard)
- Click on the `Video streams` in the left menu > Click on `kvs-workshop-stream` in the list of streams
- Click on the `Media playback` section to expand it, and you will see the video from the camera

![Media Playback](/images/1-3-a-playback.ja.png)

That's it. You learned how to upload your videos with Amazon Kinesis Video Streams Producer SDK C++.
Next, let's jump to 1-4 to download the video in MP4 format.




