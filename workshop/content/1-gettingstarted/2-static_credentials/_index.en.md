+++
title = "Obtaining credentials"
weight = 2
chapter = true
pre = "<b>2. </b>"
+++



## Obtaining AWS Credentials

{{% notice "note" %}}
Credentials are required for the sample applications to communicate with the KVS Service.  For the purpose of this workshop, we will be using static credentials.  However, a real solution would utilize a more secure and scalable method.  Fortunately, AWS IoT offers a solution using X.509 device certificates.
[How to Eliminate the Need for Hardcoded AWS Credentials in Devices by Using the AWS IoT Credentials Provider](https://aws.amazon.com/jp/blogs/security/how-to-eliminate-the-need-for-hardcoded-aws-credentials-in-devices-by-using-the-aws-iot-credentials-provider/)
{{% /notice %}}

### Please select one of the following methods to obtain credentials.



### If you are using your own AWS account.
{{%expand "Click to expand" %}}


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
{{%/expand%}}

### If you are using an account by AWS Event Engine
{{%expand "Click to expand" %}}
- Copy the credentials like below from `Team Dashboard` > `AWS Console` of [Event Engine](https://dashboard.eventengine.run) and paste them to **Raspberry Pi terminal**

```bash
export AWS_DEFAULT_REGION=...
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
export AWS_SESSION_TOKEN=...
```
{{%/expand%}}

### TODO: Create a temporary user
{{%expand "Click to expand" %}}
TODO
{{%/expand%}}
