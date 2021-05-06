+++
title = "Cloud Resources"
date = 2021-03-30T08:47:41-04:00
weight = 1
chapter = true
+++

{{% notice note %}}
For production deployments, there are a number of device provisioning strategies including automated device provisioning options which are out of scope for this workshop. The [AWS IoT Device Management Workshop](https://iot-device-management.workshop.aws/en/) provides a more holistic overview of these approaches and how to use the various features in AWS IoT Core to automate device provisioning.
{{% /notice %}}

The following steps are meant to allow you to provision a single KVS WebRTC device in AWS IoT Core.

## Create AWS IAM Role

Click below to launch a CloudFormation stack.  This will setup an IoT Device policy, a certificate-based IAM role and a device policy.

Check to acknowledge we might create IAM resources (we are), then click **Create stack.**

+ [Launch CloudFormation stack in us-east-1](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://aws-kvs-webrtc-workshop-resources.s3-us-west-2.amazonaws.com/kvs-webrtc-workshop-iot.yml&stackName=kvs-webrtc-workshop-iot) (N. Virginia)
+ [Launch CloudFormation stack in us-west-2](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/create/review?templateURL=https://aws-kvs-webrtc-workshop-resources.s3-us-west-2.amazonaws.com/kvs-webrtc-workshop-iot.yml&stackName=kvs-webrtc-workshop-iot) (Oregon)

When the stack is in a **CREATE_COMPLETE** state, please move to the next command.

## Create IoT Role Alias

Run this command from your AWS CLI, it will create the role alias from the role you just created with the CF stack above.

```
aws iot create-role-alias \
  --role-alias KvsWebRtcDeviceIoTRoleAlias \
  --role-arn $(aws cloudformation describe-stacks \
    --output text \
    --stack-name kvs-webrtc-workshop-iot \
    --query 'Stacks[0].Outputs[?OutputKey==`IAMRoleArn`].OutputValue') \
  --credential-duration-seconds 3600
```

## Provision AWS IoT Thing

Create the Thing in the Device Registry, generate X.509 Device Certificates, Define a Thing Group, add the Thing to this Thing Group, and attach the IoT Policy previously created to that Thing Group.

```
THING_NAME=my-kvs-device
THING_GROUP_NAME=KvsWebRtcDevice

THING_GROUP_ARN=$(aws iot create-thing-group \
  --output text \
  --thing-group-name $THING_GROUP_NAME \
  --query 'thingGroupArn')

aws iot create-thing \
  --thing-name "${THING_NAME}"

aws iot add-thing-to-thing-group \
  --thing-group-name $THING_GROUP_NAME \
  --thing-name $THING_NAME
```

## Create Thing Certificates

```
curl --silent 'https://www.amazontrust.com/repository/SFSRootCAG2.pem' \
  --output ./root-CA.crt

CERTIFICATE_ARN=$(aws iot create-keys-and-certificate --set-as-active \
  --certificate-pem-outfile ./device.cert.pem \
  --public-key-outfile ./device.public.key \
  --private-key-outfile ./device.private.key \
  --output text \
  --query 'certificateArn')

aws iot attach-policy \
  --policy-name $(aws cloudformation describe-stacks \
    --output text \
    --stack-name kvs-webrtc-workshop-iot \
    --query 'Stacks[0].Outputs[?OutputKey==`KvsWebRTCDevicePolicy`].OutputValue') \
  --target $THING_GROUP_ARN

aws iot attach-thing-principal \
  --thing-name $THING_NAME \
  --principal $CERTIFICATE_ARN
```

## Generate Run Script

The following will generate a convenience script that will be used to run the `kvsWebrtcClientMasterGstSample`. This includes all of the required environment variables that the sample depends on when connecting to the Credentials Provider service from AWS IoT Core. Note that this script also fetches necessary configurations such as your IoT Core Credential Provider endpoint.

```
cat > run-kvs-webrtc.sh <<EOF
#~/bin/sh
THING_NAME=$THING_NAME
CERTS_DIR=\$HOME/\$THING_NAME
AWS_DEFAULT_REGION=$AWS_DEFAULT_REGION
AWS_IOT_CREDENTIALS_ENDPOINT=$(aws iot describe-endpoint --endpoint-type iot:CredentialProvider --output text)
AWS_IOT_ROLE_ALIAS=KvsWebRtcDeviceIoTRoleAlias
IOT_CERT_PATH=\$CERTS_DIR/device.cert.pem
IOT_PRIVATE_KEY_PATH=\$CERTS_DIR/device.private.key
IOT_CA_CERT_PATH=\$CERTS_DIR/root-CA.crt
AWS_KVS_CACERT_PATH=\$HOME/amazon-kinesis-video-streams-webrtc-sdk-c/certs/cert.pem
LD_LIBRARY_PATH=\$HOME/amazon-kinesis-video-streams-webrtc-sdk-c/open-source/lib/:$PWD/amazon-kinesis-video-streams-webrtc-sdk-c/build/
\$HOME/amazon-kinesis-video-streams-webrtc-sdk-c/build/samples/kvsWebrtcClientMasterGstSample \$THING_NAME
EOF
```

## Package Configs and Send to Device

{{% notice note %}}
If you are running through this workshop entirely on Cloud9, then you can ignore this and move on to the next step.
{{% /notice %}}

If working with an actual device for this portion of the workshop, you will want to create an artifact that you can use to send all of this configuration to your device.

The following command will create a zip file with all of the required artifacts to provision your KVS WebRTC device as an IoT thing:
```
zip kvswebrtcdevice.zip \
  device.cert.pem \
  device.private.key \
  device.public.key \
  root-CA.crt \
  run-kvs-webrtc.sh
```

Right click on **kvswebrtcdevice.zip** and select **Download** to download the file to your local environment.

![Cloud9 Download](/images/download-cloud9.png)


Now use the `scp` utility to copy the zip file from your local machine to a Raspberry Pi:
```
scp kvswebrtcdevice.zip pi@raspberrypi-ip-address:/home/pi
```

At this point, ssh into your Pi or other camera device.  Unzip the file to a `my-kvs-device` directory:

```
unzip kvswebrtcdevice.zip -d my-kvs-device
```

Next step is to [modify the sample streaming application](/en/3-awsiot/2-code-changes.html) to use the IoT credentials. (either on your device or on Cloud9)



