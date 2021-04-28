+++
title = "Setup AWS Cloud9"
weight = 1
chapter = true
+++
{{% notice note %}}
Make sure you select the desired AWS region when creating your Cloud9 environment
{{% /notice %}}

Use a [supported browser](https://docs.aws.amazon.com/cloud9/latest/user-guide/browsers.html) to work with Cloud9.


## Verify the region

- Open [AWS Cloud9 console](https://console.aws.amazon.com/cloud9/home)
- From the top right corner of the console, select a region where AWS Cloud9, Amazon Kinesis Video Streams and Amazon Rekognition Video are available
  - You can confirm the regions where the services are available on [AWS Regional Services](https://aws.amazon.com/jp/about-aws/global-infrastructure/regional-product-services/)
  - For example, you can use us-west-2 (Oregon) or ap-northeast-1 (Tokyo) region


## Creating a Cloud9 environment

- Click the `Create environment` in the top right corner of the Cloud9 dashboard

## Create a Cloud9 environment (Step 1)

- Enter a descriptive name for the `Name` to delete it after the workshop (e.g. `kvs-workshop-environment`)
- Click on `Next step`

## Create a Cloud9 environment (Step 2~3)

- `Environment type`: Select `Create a new EC2 instance for environment (direct access)`
- `Instance type`: Select `t3.small`
- `Platform`: Select `Ubuntu Server 18.04 LTS`
- `Cost-saving setting`: Select `After four hours`
- Click on the `Next step` at the bottom of the screen
- On the next screen, click on `Create environment` to start the Cloud9 environment, then wait a minute or two for it to start

![Cloud9 Setup](/images/1-1-b-cloud9.ja.png)

{{% notice "note" %}}
In `Network settings (advanced)`, select a subnet of the default VPC.
If the default VPC does not exist, create the VPC and subnet as follows
[VPC settings for AWS Cloud9 Development Environments](https://docs.aws.amazon.com/ja_jp/cloud9/latest/user-guide/vpc-settings.html).
{{% /notice %}}

-----

At the bottom of the screen, you can see `Admin:~/environment $`, which is the Cloud9 (EC2) terminal.


![Cloud9 Setup](/images/1-1-b-terminal.ja.png)

## Disk Space Expansion

- Execute the following commands in a Cloud9 terminal, it will expand the disk and restart the instance
- Please wait until the instance is restarted, as you will see `Connecting...` while the instance is restarting.

{{% notice note %}}
You may have to refresh the Cloud9 console to reconnect to your environment
{{% /notice %}}


```bash
wget https://awsj-iot-handson.s3-ap-northeast-1.amazonaws.com/kvs-workshop/resize_volume.sh
chmod +x resize_volume.sh
./resize_volume.sh
```

- After the instance restarted, run the following command in the terminal to check the disk space

```bash
df -h
```

In the result of the command, make sure that `/` is 20GB as follows.

```
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1       20G  8.3G   12G  43% /
```

You just set up a Cloud9 environment. Next, we will build the sample applications.
