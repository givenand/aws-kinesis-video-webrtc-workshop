+++
title = "Code Changes"
date = 2021-03-30T08:47:41-04:00
weight = 2
chapter = true
+++

Next, you will need to update the SDK Sample application to allow it to use the AWS IoT Core Credential Provider service instead of using AWS key pairs. This will allow your KVS WebRTC device to use X.509 certificates to securely access AWS services by way of AWS IoT Core.

In the following steps, you will change the sample application to allow it to consume all of the necessary configuration via environment variables, and to make use of the `createLwsIotCredentialProvider` instead of the default `createStaticCredentialProvider`.

## Modify the Sample Application

Use your preferred text editor to modify `samples/Common.c`. For each block of code given below, search for and replace the code blocks with the provided code.

### First
Replace
```
PCHAR pAccessKey, pSecretKey, pSessionToken, pLogLevel;
```
With
```
PCHAR pCredentialsProviderEndpoint, pCertPath, pPrivateKeyPath, pCaCertPath, pIoTRoleAlias, pLogLevel;
```

### Then
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

### Finally
Replace
```
createStaticCredentialProvider(pAccessKey, 0, pSecretKey, 0, pSessionToken, 0, MAX_UINT64, &pSampleConfiguration->pCredentialProvider));
```
With
```
createLwsIotCredentialProvider(pCredentialsProviderEndpoint, pCertPath, pPrivateKeyPath, pCaCertPath, pIoTRoleAlias, channelName, &pSampleConfiguration->pCredentialProvider));
```

After you are finished replacing the above lines, save your changes and exit your code editor.

## Rebuild the sample application

From within the `amazon-kinesis-video-streams-webrtc-sdk-c/build` directory, rebuild the sample application using the make command:

```
cd amazon-kinesis-video-streams-webrtc-sdk-c/build
make
```

## Run the sample application

All of the AWS IoT configuration is made available to the Sample applications via environment variables. Set these variables and then run the `kvsWebrtcClientMasterGstSample` application.

```
$HOME/run-kvs-webrtc.sh
```
