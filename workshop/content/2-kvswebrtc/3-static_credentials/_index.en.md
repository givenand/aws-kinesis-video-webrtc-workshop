+++
title = "Obtaining credentials"
weight = 3
chapter = true
+++

{{% notice "note" %}}
Credentials are required for the sample applications to communicate with the KVS Service.  For the purpose of this workshop, we will be using static credentials. This workshop also provides modules demonstrating how to eliminate the need for hardcoded AWS Credentials on your devices by instead using AWS IoT and X.509 device certificates.  
{{% /notice %}}

{{% notice "warning" %}} If you are using an existing account, either personal or a company account, make sure you understand the implications and policy of provisioning resources into this account. {{% /notice %}}

For this workshop, we will create a user that has a KVS specific policy, the following steps will create that user.

1. Sign in to your AWS account

2. Go to the AWS IAM console and create a new user.

3. Type a name for your user (e.g. kvs-workshop) and choose **Programmatic access**.

![image info](/images/newUser1.png)

4. Click **Next: Permissions** to continue to the next step.

5. Click **Attach existing policies directly** and choose AdministratorAccess.

![new user 2](/images/newUser2.png)

Click **Next: Tags**

Click **Next: Review**

Click **Create User**

In the next screen, you'll see your Access key ID, and you will have the option to click Show to show the Secret access key.

![new user 3](/images/newUser3.png)

Download the CSV and copy your access key/secret to your Cloud9 instance

The platform is ready to stream a sample video from your Cloud9 instance.  To begin that process [Next](/en/2-kvswebrtc/4-running.html)

{{%expand "If you are using an account by AWS Event Engine (Click to expand)" %}}
- Copy the credentials like below from `Team Dashboard` > `AWS Console` of [Event Engine](https://dashboard.eventengine.run) and paste them to **Raspberry Pi terminal**

```bash
export AWS_DEFAULT_REGION=...
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
export AWS_SESSION_TOKEN=...
```
{{%/expand%}}
