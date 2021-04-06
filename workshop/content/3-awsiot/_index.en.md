+++
title = "AWS IoT"
date = 2021-03-30T08:47:41-04:00
weight = 3
chapter = true
pre = "<b>3. </b>"
+++

`TODO` standardize on the `kvsWebrtcClientMasterGstSample` across all instructions

`TODO` - discussion about why using the credentials provider endpoint is encouraged, what other benefits that using AWS IoT to manage your devices provides, etc

In this section of the workshop, you will learn how to make use of AWS IoT services in order to securely provision your devices using X.509 Certificates instead of using hard-coded AWS Access Key Pairs. You will also learn how to take advantage of other important aspects of integrating your devices with AWS IoT services, such as Device Management and Monitoring capabilities.

There are different considerations for working with a single device as compared to preparing hundreds, thousands, or even millions of devices for a production deployment. This workshop will focus on quickly enabling development teams to provision single devices as AWS IoT Things, and will point to helpful resources to consider as you prepare for production deployments.

## Single Device Setup

### Create AWS IAM Role

```
export AWS_DEFAULT_REGION=us-east-1

aws cloudformation deploy \
  --template-file ./kvs-webrtc-workshop-iot.yml \
  --stack-name kvs-webrtc-workshop-iot \
  --capabilities CAPABILITY_IAM
```

### Create IoT Role Alias

```
aws iot create-role-alias \
  --role-alias KvsWebRtcDeviceIoTRoleAlias \
  --role-arn $(aws cloudformation describe-stacks \
    --output text \
    --stack-name kvs-webrtc-workshop-iot \
    --query 'Stacks[0].Outputs[?OutputKey==`IAMRoleArn`].OutputValue') \
  --credential-duration-seconds 3600
```

### Provision AWS IoT Thing

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

### Package configs, send to device

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

Finally, you will send this zip file to your device. For example, you could use the `scp` utility to copy the zip file from your local machine to a Raspberry Pi:
```
scp kvswebrtcdevice.zip pi@000.000.000.000:/home/pi
```

`TODO` - define where to unzip this file on the remote filesystem, and ensure that the paths generated in the `run-kvs-webrtc.sh` work according to the instructions.

### Modify sample applications as follows

Next, you will need to update the SDK Sample application to allow it to use the AWS IoT Core Credential Provider service instead of using AWS key pairs.

Use your preferred text editor to modify `samples/Common.c`. For each block of code given below, search for and replace the code blocks with the provided code.

#### First
Replace
```
PCHAR pAccessKey, pSecretKey, pSessionToken, pLogLevel;
```
With
```
PCHAR pCredentialsProviderEndpoint, pCertPath, pPrivateKeyPath, pCaCertPath, pIoTRoleAlias, pLogLevel;
```

#### Then
Replace
```
CHK_ERR((pAccessKey = getenv(ACCESS_KEY_ENV_VAR)) != NULL, STATUS_INVALID_OPERATION, "AWS_ACCESS_KEY_ID must be set");
CHK_ERR((pSecretKey = getenv(SECRET_KEY_ENV_VAR)) != NULL, STATUS_INVALID_OPERATION, "AWS_SECRET_ACCESS_KEY must be set");
pSessionToken = getenv(SESSION_TOKEN_ENV_VAR);
```
With
```
CHK_ERR((pCredentialsProviderEndpoint = getenv("AWS_IOT_CREDENTIALS_ENDPOINT")) != NULL, STATUS_INVALID_OPERATION, "AWS_IOT_CREDENTIALS_ENDPOINT must be set");
CHK_ERR((pCertPath = getenv("IOT_CERT_PATH")) != NULL, STATUS_INVALID_OPERATION, "IOT_CERT_PATH must be set");
CHK_ERR((pPrivateKeyPath = getenv("IOT_PRIVATE_KEY_PATH")) != NULL, STATUS_INVALID_OPERATION, "IOT_PRIVATE_KEY_PATH must be set");
CHK_ERR((pCaCertPath = getenv("IOT_CA_CERT_PATH")) != NULL, STATUS_INVALID_OPERATION, "IOT_CA_CERT_PATH must be set");
CHK_ERR((pIoTRoleAlias = getenv("AWS_IOT_ROLE_ALIAS")) != NULL, STATUS_INVALID_OPERATION, "AWS_IOT_ROLE_ALIAS must be set");
```

#### Finally
Replace
```
createStaticCredentialProvider(pAccessKey, 0, pSecretKey, 0, pSessionToken, 0, MAX_UINT64, &pSampleConfiguration->pCredentialProvider));
```
With
```
createLwsIotCredentialProvider(pCredentialsProviderEndpoint, pCertPath, pPrivateKeyPath, pCaCertPath, pIoTRoleAlias, channelName, &pSampleConfiguration->pCredentialProvider));
```

After you are finished replacing the above lines, save your changes and exit your code editor.

### Rebuild the sample application

From within the `amazon-kinesis-video-streams-webrtc-sdk-c/build` directory, rebuild the sample application using the make command:

```
cd amazon-kinesis-video-streams-webrtc-sdk-c/build
make
```

### Run the sample application

All of the AWS IoT configuration is made available to the Sample applications via environment variables. Set these variables and then run the `kvsWebrtcClientMasterGstSample` application.

```
$HOME/run-kvs-webrtc.sh
```


## Cleanup

```

aws iot delete-role-alias --role-alias KvsWebRtcDeviceIoTRoleAlias

CERTIFICATE_ARN=$(aws iot list-thing-principals \
  --thing-name $THING_NAME \
  --output text \
  --query 'principals[0]')

CERTIFICATE_ID="${CERTIFICATE_ARN##*/}"

aws iot detach-policy \
  --target "$CERTIFICATE_ARN" \
  --policy-name $(aws cloudformation describe-stacks \
    --output text \
    --stack-name kvs-webrtc-workshop-iot \
    --query 'Stacks[0].Outputs[?OutputKey==`KvsWebRTCDevicePolicy`].OutputValue')

aws iot update-certificate \
  --certificate-id $CERTIFICATE_ID \
  --new-status INACTIVE

aws iot detach-thing-principal \
  --thing-name "$THING_NAME" \
  --principal "$CERTIFICATE_ARN"

aws iot delete-certificate --certificate-id $CERTIFICATE_ID
aws iot delete-thing --thing-name $THING_NAME
aws iot delete-thing-group --thing-group-name $THING_GROUP_NAME

rm run-kvs-webrtc.sh device.cert.pem device.private.key device.public.key root-CA.crt kvswebrtcdevice.zip

aws cloudformation delete-stack --stack-name kvs-webrtc-workshop-iot
```
