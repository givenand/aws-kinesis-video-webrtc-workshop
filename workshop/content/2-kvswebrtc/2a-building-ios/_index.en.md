+++
title = "Using an iOS Device"
chapter = true
weight = 2.3
+++

The following steps assume that you have XCode 10 or later installed on your computer and your device is running iOS 11 and later


## Clone and Build the SDK Sample Applications

```
git
git clone --recursive https://github.com/awslabs/amazon-kinesis-video-streams-webrtc-sdk-ios.git
```

Using XCode to build the project:

1. The AWS Mobile SDK for iOS is available through CocoaPods. If you have not installed CocoaPods, install CocoaPods: (Requires Ruby installed)
```
sudo gem install cocoapods
pod setup
```
2. To install the AWS Mobile SDK for iOS run the following command in the directory containing this sample:
```
pod install
```
3. Create an Amazon Cognito User Pool. 

    a. From the Amazon Cognito console, click Manage your user pool and Create a user pool to create a new user pool.
    b. Provide a name for your pool. You can opt to customize the settings of your user pool by selecting the Step through settings option. However, for this workshop, we will proceed with the default option by clicking Review 
    c. Next, you will create an app client. Every app and web app that accesses your pool requires an app client ID and optionally an app client secret. You can create multiple app clients for a user pool, and you typically create one per platform (i.e., iOS, Android, JavaScript). To begin this process, click Apps on the left navigation bar and then click Add an app. Enter your app name. Select Generate client secret and then click Create app and Save changes.
    d. Click Review on the left navigation bar and then click Create pool.
    e. Note your user pool id from the screen that appears. You can find the app client id and app client secret under Apps on the left navigation bar.  You will use these later in your iOS code.

4. Open KinesisVideoWebRTCDemoApp.xcworkspace (File location: KVSiOS/Swift/AWSKinesisVideoWebRTCDemoApp.xcworkspace).

5. Open Constants.swift. Set CognitoIdentityUserPoolRegion, CognitoIdentityUserPoolId, CognitoIdentityUserPoolAppClientId and CognitoIdentityUserPoolAppClientSecret to the values obtained when you created your user pool.

```
        let CognitoIdentityUserPoolRegion: AWSRegionType = .Unknown
        let CognitoIdentityUserPoolId = "YOUR_USER_POOL_ID"
        let CognitoIdentityUserPoolAppClientId = "YOUR_APP_CLIENT_ID"
        let CognitoIdentityUserPoolAppClientSecret = "YOUR_APP_CLIENT_SECRET"
```

6. Open `awsconfiguration.json` and replace the values with the appropriate onces from your Cognito pool `PoolId, Region, AppClientSecret, AppClientId`


![KVSMediaPlayback](/images/appconfiguration.png)

7. To build and run, click the play button on the XCode menu. 

Note: This sample will only work if you have a physical iOS device connected via a lightening cable.

## Run the iOS Sample Application

Building the iOS sample application installs the AWSKinesisVideoWebRTCDemoApp on your iOS device. Using this app, you can verify live audio/video streaming between mobile, web and IoT device clients (camera). The procedure below describes some of these scenarios.

Complete the following steps:

On your iOS device, open AWSKinesisVideoWebRTCDemoApp and login using the AWS user credentials from Set Up an AWS Account and Create an Administrator. (Note: Cognito settings can be tuned through your Cognito User Pool in the AWS management Console)

On successful sign-in, the channel configuration view is displayed where the channel-name, client-id (optional) and region-name have to be configured.
