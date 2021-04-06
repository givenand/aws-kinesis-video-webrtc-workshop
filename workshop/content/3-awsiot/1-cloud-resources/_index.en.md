+++
title = "Cloud Resources"
date = 2021-03-30T08:47:41-04:00
weight = 1
chapter = true
pre = "<b>1. </b>"
+++

`TODO` add high-level description of what we are creating here, and explain why 

## Create AWS IAM Role

```
export AWS_DEFAULT_REGION=us-east-1

aws cloudformation deploy \
  --template-file ./kvs-webrtc-workshop-iot.yml \
  --stack-name kvs-webrtc-workshop-iot \
  --capabilities CAPABILITY_IAM
```

## Create IoT Role Alias

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

## Package configs, send to device

Retrieve IoT Credential Provider endpoint
Zip configs from Cloud9 instance and certs, send to "device"

First, we will generate a convenience script that will be used to run the `kvsWebrtcClientMasterGstSample` on your target device. This includes all of the required environment variables that the sample depends on when connecting to the Credentials Provider service from AWS IoT Core.

```
cat > run-kvs-webrtc.sh <<EOF
THING_NAME=$THING_NAME
CERTS_DIR=\$HOME/\$THING_NAME/certs
AWS_DEFAULT_REGION=$AWS_DEFAULT_REGION
AWS_IOT_CREDENTIALS_ENDPOINT=$(aws iot describe-endpoint --endpoint-type iot:CredentialProvider --output text)
AWS_IOT_ROLE_ALIAS=KvsWebRtcDeviceIoTRoleAlias
IOT_CERT_PATH=\$CERTS_DIR/device.cert.pem
IOT_PRIVATE_KEY_PATH=\$CERTS_DIR/device.private.key
IOT_CA_CERT_PATH=\$CERTS_DIR/root-CA.crt
AWS_KVS_CACERT_PATH=\$HOME/amazon-kinesis-video-streams-webrtc-sdk-c/certs/cert.pem
LD_LIBRARY_PATH=\$HOME/amazon-kinesis-video-streams-webrtc-sdk-c/open-source/lib/:$PWD/amazon-kinesis-video-streams-webrtc-sdk-c/build/
$HOME/amazon-kinesis-video-streams-webrtc-sdk-c/build/kvsWebrtcClientMasterGstSample \$THING_NAME
EOF
```

Next you will create a zip file with all of the required artifacts to provision your KVS WebRTC device as an IoT thing:
```
zip kvswebrtcdevice.zip device.cert.pem device.private.key device.public.key root-CA.crt run-kvs-webrtc.sh
```

If working with an actual device for this lab, you will send this zip file to your device. If you are running through this workshop entirely on Cloud9, then you can ignore this step.

For example, you could use the `scp` utility to copy the zip file from your local machine to a Raspberry Pi:
```
scp kvswebrtcdevice.zip pi@raspberrypi-ip-address:/home/pi
```

`TODO` - define where to unzip this file on the remote filesystem, and ensure that the paths generated in the `run-kvs-webrtc.sh` work according to the instructions.