+++
title = "AWS IoT"
date = 2021-03-30T08:47:41-04:00
weight = 3
chapter = true
pre = "<b>3. </b>"
+++

`TODO` standardize on the `kvsWebrtcClientMasterGstSample` across all instructions

`TODO` - two separate paths here. One path is to run the `kvsWebrtcClientMaster` sample application on an EC2 instance. The other path is to fully build and configure the `kvsWebrtcClientMasterGstSample` sample application on a Raspberry Pi v4

`TODO` - discussion about why using the credentials provider endpoint is encouraged, what other benefits that using AWS IoT to manage your devices provides, etc

In this section of the workshop, you will learn how to make use of AWS IoT services in order to securely provision your devices using X.509 Certificates instead of using hard-coded AWS Access Key Pairs. You will also learn how to take advantage of other important aspects of integrating your devices with AWS IoT services, such as Device Management and Monitoring capabilities.

There are different considerations for working with a single device as compared to preparing hundreds, thousands, or even millions of devices for a production deployment. This workshop will focus on quickly enabling development teams to provision single devices as AWS IoT Things, and will point to helpful resources to consider as you prepare for production deployments.

## Single Device Setup

TODO - should these first few steps be automated in CFN templates?

### Create AWS IAM Role
 `KVSCameraCertificateBasedIAMRole` with IAM Role Policy `KVSCameraIAMPolicy`  

### Create IoT Role Alias
`KvsCameraIoTRoleAlias`

### Provision AWS IoT Thing
Thing in Registry, Device Certificates, IoT Policy

### Package configs, send to device

Retrieve IoT Credential Provider endpoint
Zip configs from Cloud9 instance and certs, send to "device"

### Modify sample applications as follows

modify `samples/Common.c`

```
# replace
   PCHAR pAccessKey, pSecretKey, pSessionToken, pLogLevel;
# with
   PCHAR pCredentialsProviderEndpoint, pCertPath, pPrivateKeyPath, pCaCertPath, pIoTRoleAlias, pLogLevel;
```

```
# replace
   CHK_ERR((pAccessKey = getenv(ACCESS_KEY_ENV_VAR)) != NULL, STATUS_INVALID_OPERATION, "AWS_ACCESS_KEY_ID must be set");
   CHK_ERR((pSecretKey = getenv(SECRET_KEY_ENV_VAR)) != NULL, STATUS_INVALID_OPERATION, "AWS_SECRET_ACCESS_KEY must be set");
   pSessionToken = getenv(SESSION_TOKEN_ENV_VAR);
# with
   CHK_ERR((pCredentialsProviderEndpoint = getenv("AWS_IOT_CREDENTIALS_ENDPOINT")) != NULL, STATUS_INVALID_OPERATION, "AWS_IOT_CREDENTIALS_ENDPOINT must be set");
   CHK_ERR((pCertPath = getenv("IOT_CERT_PATH")) != NULL, STATUS_INVALID_OPERATION, "IOT_CERT_PATH must be set");
   CHK_ERR((pPrivateKeyPath = getenv("IOT_PRIVATE_KEY_PATH")) != NULL, STATUS_INVALID_OPERATION, "IOT_PRIVATE_KEY_PATH must be set");
   CHK_ERR((pCaCertPath = getenv("IOT_CA_CERT_PATH")) != NULL, STATUS_INVALID_OPERATION, "IOT_CA_CERT_PATH must be set");
   CHK_ERR((pIoTRoleAlias = getenv("AWS_IOT_ROLE_ALIAS")) != NULL, STATUS_INVALID_OPERATION, "AWS_IOT_ROLE_ALIAS must be set");
```

```
# replace
   createStaticCredentialProvider(pAccessKey, 0, pSecretKey, 0, pSessionToken, 0, MAX_UINT64, &pSampleConfiguration->pCredentialProvider));
# with
   createLwsIotCredentialProvider(pCredentialsProviderEndpoint, pCertPath, pPrivateKeyPath, pCaCertPath, pIoTRoleAlias, channelName, &pSampleConfiguration->pCredentialProvider));
```

### Rebuild the sample application

From within the `amazon-kinesis-video-streams-webrtc-sdk-c/build` directory, rebuild the sample application using the make command:

```
cd amazon-kinesis-video-streams-webrtc-sdk-c/build
make
```

### Run the sample application

All of the AWS IoT configuration is made available to the Sample applications via environment variables. Set these variables and then run the `kvsWebrtcClientMasterGstSample` application.

```
THING_NAME=my-kvs-thing
CERTS_DIR=$HOME/${THING_NAME}-certs
AWS_DEFAULT_REGION=us-east-1
AWS_IOT_CREDENTIALS_ENDPOINT=c1ddj874wepx7m.credentials.iot.us-east-1.amazonaws.com
AWS_IOT_ROLE_ALIAS=KvsCameraIoTRoleAlias
IOT_CERT_PATH=${CERTS_DIR}/${THING_NAME}_thing.cert.pem
IOT_PRIVATE_KEY_PATH=${CERTS_DIR}/${THING_NAME}_thing.private.key
IOT_CA_CERT_PATH=${CERTS_DIR}/AmazonRootCA1.pem
AWS_KVS_CACERT_PATH=$HOME/amazon-kinesis-video-streams-webrtc-sdk-c/certs/cert.pem
LD_LIBRARY_PATH=$HOME/amazon-kinesis-video-streams-webrtc-sdk-c/open-source/lib/:$HOME/amazon-kinesis-video-streams-webrtc-sdk-c/build/

$HOME/amazon-kinesis-video-streams-webrtc-sdk-c/build/kvsWebrtcClientMasterGstSample $THING_NAME
