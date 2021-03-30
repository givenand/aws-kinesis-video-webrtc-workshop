aws iot create-job \
  --job-id run-curl-and-execute-6 \
  --document file://curl-and-execute.json \
  --targets arn:aws:iot:us-east-1:068311527115:thing/rpi_v4_192_168_4_66

aws iot describe-job-execution \
  --region us-east-1 \
  --job-id run-curl-and-execute-5 \
  --thing-name rpi_v4_192_168_4_66

# TODO - need to generate KVS IAM Policy, IoT Policy, and attach to existing Thing Cert
# based on this: https://github.com/dave-malone/aws-kvs-webrtc-demo-for-raspberry-pi/blob/main/iot/provision-thing.sh


# run-kvs-webrtc.sh

# Would be ideal to take the following values as arguments for this script:
# AWS_REGION
# AWS_IOT_CREDENTIALS_ENDPOINT
# AWS_IOT_ROLE_ALIAS
# THING_NAME

export THING_NAME=rpi_v4_192_168_4_66
export CERTS_DIR=$HOME/${THING_NAME}-certs
export AWS_DEFAULT_REGION=us-east-1
export AWS_IOT_CREDENTIALS_ENDPOINT=c1ddj874wepx7m.credentials.iot.us-east-1.amazonaws.com
export AWS_IOT_ROLE_ALIAS=KvsCameraIoTRoleAlias
export IOT_CERT_PATH=${CERTS_DIR}/${THING_NAME}_thing.cert.pem
export IOT_PRIVATE_KEY_PATH=${CERTS_DIR}/${THING_NAME}_thing.private.key
export IOT_CA_CERT_PATH=${CERTS_DIR}/AmazonRootCA1.pem
export AWS_KVS_CACERT_PATH=$HOME/amazon-kinesis-video-streams-webrtc-sdk-c/certs/cert.pem
export LD_LIBRARY_PATH=$HOME/amazon-kinesis-video-streams-webrtc-sdk-c/open-source/lib/:$HOME/amazon-kinesis-video-streams-webrtc-sdk-c/build/

$HOME/amazon-kinesis-video-streams-webrtc-sdk-c/build/kvsWebrtcClientMasterGstSample $THING_NAME


# TODO - modify samples/Common.c
# replace
#   PCHAR pAccessKey, pSecretKey, pSessionToken, pLogLevel;
# with
#   PCHAR pCredentialsProviderEndpoint, pCertPath, pPrivateKeyPath, pCaCertPath, pIoTRoleAlias, pLogLevel;

# replace
#   CHK_ERR((pAccessKey = getenv(ACCESS_KEY_ENV_VAR)) != NULL, STATUS_INVALID_OPERATION, "AWS_ACCESS_KEY_ID must be set");
#   CHK_ERR((pSecretKey = getenv(SECRET_KEY_ENV_VAR)) != NULL, STATUS_INVALID_OPERATION, "AWS_SECRET_ACCESS_KEY must be set");
#   pSessionToken = getenv(SESSION_TOKEN_ENV_VAR);
# with
#   CHK_ERR((pCredentialsProviderEndpoint = getenv("AWS_IOT_CREDENTIALS_ENDPOINT")) != NULL, STATUS_INVALID_OPERATION, "AWS_IOT_CREDENTIALS_ENDPOINT must be set");
#   CHK_ERR((pCertPath = getenv("IOT_CERT_PATH")) != NULL, STATUS_INVALID_OPERATION, "IOT_CERT_PATH must be set");
#   CHK_ERR((pPrivateKeyPath = getenv("IOT_PRIVATE_KEY_PATH")) != NULL, STATUS_INVALID_OPERATION, "IOT_PRIVATE_KEY_PATH must be set");
#   CHK_ERR((pCaCertPath = getenv("IOT_CA_CERT_PATH")) != NULL, STATUS_INVALID_OPERATION, "IOT_CA_CERT_PATH must be set");
#   CHK_ERR((pIoTRoleAlias = getenv("AWS_IOT_ROLE_ALIAS")) != NULL, STATUS_INVALID_OPERATION, "AWS_IOT_ROLE_ALIAS must be set");

# replace
#   createStaticCredentialProvider(pAccessKey, 0, pSecretKey, 0, pSessionToken, 0, MAX_UINT64, &pSampleConfiguration->pCredentialProvider));
# with
#   createLwsIotCredentialProvider(pCredentialsProviderEndpoint, pCertPath, pPrivateKeyPath, pCaCertPath, pIoTRoleAlias, channelName, &pSampleConfiguration->pCredentialProvider));
