+++
title = "Cleanup"
date = 2021-03-30T08:47:41-04:00
weight = 3
chapter = true
pre = "<b>3. </b>"
+++

`TODO` add high-level description

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
