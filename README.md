# HIP - the Hierarchical IoT Protocol

HIP is a high level protocol for the Internet of Things. 

At the core of HIP is the device, an abstract representation of an IoT thing/device/gateway. Rather than focusing on the physical device configuration, HIP is concerned about how each device can add value to an IoT deployment. HIP achieves this by defining a number of roles which can be deployed and moved across devices.

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

Configuration data is sent to the underlying device as part of the device configuration. This data defines the topics to subscribe to and publish on, the required handlers to use as well as any configuration requirements (e.g. polling frequency for a sensor).

In order to move an aggregator from one device to another, each device is sent an updated configuration, removing the aggregator from one device and adding it to another.

In addition to the specific functionality provided by the aggregator handler, all communications from devices to the platform is routed through one or more aggregators.

## Delegator
Delegators act as intermediaries between the platform and controllers and are used to group "similar" controllers together. By similar, this could be all controllers interfacing with a specific type of real-world device, or all controllers interfacing with devices in a particular location (so, for a smart office, this could be all lighting controllers in hte building or all controllers - lighting, AC etc. on a particular floor of the building)

The rationale for delegators is two-fold. A single command can be used to update a group of controllers (e.g. set the desired temperature across a number of HVAC units) and also allow for local or offline interaction with the real-world (this is discussed in more detail when looking at the Commander)

All communications from the platform to a device (with the exception of the coordinator detailed below) is routed through one or more delegators.

## Commander

Commanders can communicate directly with specified aggregators and delegators and allow for monitoring and control of the local environment without any reliance on the platform. In this way, commanders support off-line access to an IOT deployment as well as reducing the bandwidth requirements between the deployment and the platform.

Unlike other roles, the Commander does not rely solely on MQTT messaging. Rest endpoints are deployed to the device as part of the role and are used to interact with user-facing front-ends (e.g. web or mobile applications)

## Coordinator

The coordinator is the edge device of the deployment and the only device which can communicate directly with the platform. There is only one active Coordinator in a deployment at any time, but any number of devices can be configured with the role. Coordinators communicate on specific message topics to monitor the status of the active coordinator and "elect" a replacement in the event that the active coordinator is unavailable.
 
# Messaging
Work to date has leverage MQTT as the underlying messaging protocol. Each device and deployed role has a unique set of MQTT topics to publish on or subscribe to.

MQTT wildcards are not used as part of the protocol.

Messages from the platform to the deployment start with an uppercase letter, while messages sent from devices start with a lowercase letter. This allows for responses to each message class (e.g. "X/some_path" message to execute a command will be replied to with "x/same_path")

The messaging within HIP is  detailed in the [specification document](specification.md) (this is still very early Work in Progress)

