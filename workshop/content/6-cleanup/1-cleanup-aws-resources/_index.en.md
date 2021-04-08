+++
title = "Cleanup AWS Resources"
date = 2021-03-30T08:47:41-04:00
weight = 1
chapter = true
+++

After you have completed this portion of the workshop, we recommend that you clean up all of the resources generated. The following commands, executed in order, will delete them all.


```
# delete the IoT Role Alias
aws iot delete-role-alias --role-alias KvsWebRtcDeviceIoTRoleAlias

# fetch the IoT certificate ARN
CERTIFICATE_ARN=$(aws iot list-thing-principals \
  --thing-name $THING_NAME \
  --output text \
  --query 'principals[0]')

CERTIFICATE_ID="${CERTIFICATE_ARN##*/}"

# detach the IoT policy from the certificate
aws iot detach-policy \
  --target "$CERTIFICATE_ARN" \
  --policy-name $(aws cloudformation describe-stacks \
    --output text \
    --stack-name kvs-webrtc-workshop-iot \
    --query 'Stacks[0].Outputs[?OutputKey==`KvsWebRTCDevicePolicy`].OutputValue')

# deactivate the certificate (required before you can delete it)
aws iot update-certificate \
  --certificate-id $CERTIFICATE_ID \
  --new-status INACTIVE

# detach the certificate from the thing in the registry
aws iot detach-thing-principal \
  --thing-name "$THING_NAME" \
  --principal "$CERTIFICATE_ARN"

# delete the device certificate
aws iot delete-certificate --certificate-id $CERTIFICATE_ID

# delete the thing in the registry
aws iot delete-thing --thing-name $THING_NAME

# delete thing group
aws iot delete-thing-group --thing-group-name $THING_GROUP_NAME

# remove local resources
rm run-kvs-webrtc.sh \
  device.cert.pem \
  device.private.key \
  device.public.key \
  root-CA.crt \
  kvswebrtcdevice.zip
```
