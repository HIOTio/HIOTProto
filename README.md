# HIP - the Hierarchical IoT Protocol

HIP is a high level protocol for the Internet of Things. 

At the core of HIP is the device, an abstract representation of an IoT thing/device/gateway. Rather than focusing on the physical device configuration, HIP is concerned about how each device can add value to an IoT deployment. HIP achieves this by defining a number of roles which can be deployed and moved across any device.

- <b>Sensor</b>, to collect telemetry data
- <b>Controller</b>, to interact with real-world controls
- <b>Aggregator</b>, to process telemetry data 
- <b>Delegator</b>, (previously referred to as a broker) to forward commands to individual or multiple controllers
- <b>Commander</b>, to allow local interaction with Sensors and Controllers without relying on the cloud platform 
- <b>Coordinator</b>, Edge device responsible for all communications in to and out of a deployment

## Device
HIP is interested in devices as a host for the above roles. Rather than being concerned with the configuration of the device (e.g. network and OS configuration), HIP is focused on deploying and removing roles on devices and identifying bottlenecks or underutilised devices across the deployment.

## Sensor
Sensors gather telemetry data either locally (e.g. attached directly to GPIO pins) or remotely (e.g. via an RF receiver) and publish this data on a defined MQTT topic in the form of "s/sensor_path" - where sensor_path is a unique path for the sensor)

## Controller
Much like sensors, controllers connect to local or remote actuators. Commands are published on defined topics in the form "X/controller_path" where controller_path is unique to the individual controller

## Aggregator
Aggregators subscribe to sensor topics and process the sensor data in some way. Aggregators can subscribe to any number of sensors topics, and equally, the same sensor data can be fed into multiple aggregators. 

This allows deployment data to be processed in many different ways before being sent to the platform. An aggregator could, for example, provide a daily summary of data across a number of different sensors or calculate the minimum, maximum and mean reading from a collection of sensors.

HIP is not perscriptive on how data is processed by an aggregator. Instead, HIP is concerned about how Aggregator roles can be defined and deployed. 

Handlers are transferred to the device from the platform and are responsible for providing the specific aggregator functionality (e.g. summarise the data across a number of sensors). Handlers could be OSGi bundles, python/javascript files etc. 

Configuration data is sent to the underlying device as part of the device configuration. This data will define the channels to subscribe to and publish on, the required handlers to use as well as any configuration requirements.

In order to move an aggregator from one device to another, each device is sent an updated configuration, removing the aggregator from one device and adding it to another

## Delegator
Delegators act as intermediaries between the platform and controllers and are used to group "similar" controllers together. By similar, this could be all controllers interfacing with a specific type of real-world device, or all controllers interfacing with devices in a particular location (so, for a smart office, this could be all lighting controllers or all controllers - lighting, AC etc. on a particular floor of the building)

## Commander
Commanders can communicate directly with specified aggregators and delegators and allow for monitoring and control of the local environment without any reliance on the platform. In this way, commanders support off-line access to an IOT deployment as well as reducing the bandwidth requirements between the deployment and the platform.
## Coordinator
THe coordinator is the edge device of the deployment and the only device which can communicate directly with the platform.
# Messaging
Work to date has leverage MQTT as the underlying messaging protocol. Each device and deployed role has a unique set of MQTT topics to publish on or subscribe to.

MQTT wildcards are not used as part of the protocol.

Messages from the platform to the deployment start with an uppercase letter, while messages sent from devices start with a lowercase letter. This allows for responses to each message class (e.g. "X/some_path" message to execute a command will be replied to with "x/same_path")

The messaging within HIP is outlined below, more details can be found in the <a href="specification.md">specification document</a> (this is still very early Work in Progress)
## Device Messaging

These messages support the underlying device and manage the deployment/removal of roles on a device.

Each message below can pass through zero or more aggregators (when sent TO the platform from the specified device) or delegators (when sent FROM the platform to the specified device)
### Onboarding
Devices can be added to an HIP implementation through the Onboarding message topic ("O")

As part of the Onboarding process, a unique identifier and associated paths (MQTT topics) are assigned to the device.
 
### Configuration
Configuration messages ("C/<device_path>") are sent from the platform to update the configuration of a device (e.g. add/remove roles, enable/disable an MQTT broker on the device etc.)

After applying the updated configuration, the device replies to the platform with its updated configuration (on topic "c/<device_path>")
### Health
Healtn message ("h/<device_path"), are periodically sent from devices to the platform and contain summary information on the device resources (e.g. memory usage, disk I/O and free space and/or the output from commands like "top" etc.)

The platform may also poll a particular device (e.g. before deploying additional roles) by publishing on the topic "H/<device_path>"
## Aggregator Messaging
Aggregators subscribe to one or more sensors or  other aggregators. Each implementation of an aggregator has a specific aggregator path (MQTT topic) associated with it. 

All output from the aggregator is published on "a/<agg_path", where agg_path is the path assigned to a particular aggregator.

Handlers are deployed as part of the aggregator and provide the functionality to process the input data (e.g. statistical or mathematical calculations, filtering of audio/video streams etc.)

A core goal of HIP is to help standardise how IOT is deployed without restricting or prescribing how it is used. The ability of Aggregators to process any type of input data in any way is key to achieving this goal.

## Controller Messaging

## Coordinator Messaging

## Delegator Messaging

## Sensor Messaging

## Commander Messaging

In order to interact with devices, the following communication topics are used

- Configuration ("C/device_path")
Each role has associated configuration data
Work to date has focused on leveraging MQTT as the underlying messaging protocol, but there should be no reason why other messaging protocols or standards could not be used.


Take a look at specification.md for the latest draft. It's still very early days, so feel free to help out and contribute to the specification
