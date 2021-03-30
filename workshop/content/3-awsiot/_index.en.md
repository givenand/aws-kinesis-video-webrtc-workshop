+++
title = "AWS IoT"
date = 2021-03-30T08:47:41-04:00
weight = 3
chapter = true
pre = "<b>3. </b>"
+++

In this section of the workshop, you will learn how to make use of AWS IoT services in order to securely provision your devices using X.509 Certificates instead of using hard-coded AWS Access Key Pairs. You will also learn how to take advantage of other important aspects of integrating your devices with AWS IoT services, such as Device Management and Monitoring capabilities.

## Steps:

### Create AWS IAM Role
 `KVSCameraCertificateBasedIAMRole` with IAM Role Policy `KVSCameraIAMPolicy`  

### Create IoT Role Alias
`KvsCameraIoTRoleAlias`

### Provision AWS IoT Thing
Thing in Registry, Device Certificates, IoT Policy

### Package configs, send to device
Retrieve IoT Credential Provider endpoint

Zip configs and certs, send to "device"

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
