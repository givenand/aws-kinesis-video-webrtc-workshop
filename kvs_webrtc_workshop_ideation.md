KVS WebRTC Workshop ideation session


# Goal for the workshop

Understand how to think about ...

## Devices for the workshop itself

- EC2 as an option using h264 / opus sample frames instead of attached camera
- RaspberryPi 4 / 3 / Zero

## Workshop template repo



## Development -> Prod lifecycle

Dev phase
Manufacturing
Onboarding
Managing & Monitoring

## Device side application / KVS WebRTC client

Initial device configuration - how do I get wifi creds on the device?
- BLE based mobile integration to provide network configs, etc
- QR code based provisioning (preferred)

Is kvsWebrtcClientMasterGstSample meant to be long-running?
* Short answer: yes
* Reduces time to connect latency
* Cost implication? connected device doesn't incur cost until they consume video from the device
* But... does cost the service team money
* One customer, Denso, using Device Shadows to turn this on/off

Bryan - dev phase aspects of designing C based applications for the device itself; thinking about writing a dev guide

## IoT integrations that we want to build

Health of their devices; current memory / cpu utilization of each process
Is each process running, and are all of the expected processes running?
Back channel to restart processes (ssh tunnel?)

OTA updates
- Jobs to remote update the applications running on the device - KVS client itself, etc
- future incorporation of Unified Agent

Device Defender - surveillance cameras are notoriously bad at implementing

Device/Fleet Management at scale (Spyglass?)

Device Provisioning & Claiming flows (?)

## Web / Mobile apps

Amplify based:
* Amplify kvs webrtc plugin
* Web apps
* IdP incorporation cognito
* automatically generate admin & non-admin users
* Need help: Cognito Groups and Fine-Grained RBAC. Per Bryan, met with Cognito team, they recommend using a custom API with STS to control access.
* TODO - design what we want the UI to look like and the flows between the screens  

Non-amplify based:
* IdP incorporation

## SaaS implementations / Multi-tenant Security

ie Wyze who provides the platform, how do we ensure that end users can only access their cameras that belong to them

- how could we implement an example on EC2 to demonstrate this aspect of the architecture using multiple camera feeds?

## Alexa Smart Home Skill using Echo Show capable devices

Bryan has guide from Alexa team for this

## Surveillance monitoring tablet application for multiple camera feeds
