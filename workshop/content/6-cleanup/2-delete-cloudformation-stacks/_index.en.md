+++
title = "Delete CloudFormation Stacks"
date = 2021-03-30T08:47:41-04:00
weight = 2
chapter = true
+++

After you have completed this portion of the workshop, we recommend that you clean up all of the resources generated. The following commands, executed in order, will delete them all.

In the Cloud9 console:
```
aws cloudformation delete-stack --stack-name kvs-webrtc-workshop-iot
```
