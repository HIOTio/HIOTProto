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

##Sensor
Sensors gather telemetry data either locally (e.g. attached directly to GPIO pins) or remotely (e.g. via an RF receiver) and publish this data on a defined MQTT topic in the form of "s/sensor_path" - where sensor_path is a unique path for the sensor)

##Controller
Much like sensors, controllers connect to local or remote actuators. Commands are published on defined topics in the form "X/controller_path" where controller_path is unique to the individual controller

##Aggregator
Aggregators subscribe to sensor topics and process the sensor data in some way. Aggregators can subscribe to any number of sensors topics, and equally, the same sensor data can be fed into multiple aggregators. 

This allows deployment data to be processed in many different ways before being sent to the platform. An aggregator could, for example, provide a daily summary of data across a number of different sensors or calculate the minimum, maximum and mean reading from a collection of sensors.

HIP is not perscriptive on how data is processed by an aggregator. Instead, HIP is concerned about how Aggregator roles can be defined and deployed. 

Handlers are transferred to the device from the platform and are responsible for providing the specific aggregator functionality (e.g. summarise the data across a number of sensors). Handlers could be OSGi bundles, python/javascript files etc. 

Configuration data is sent to the underlying device as part of the device configuration. This data will define the channels to subscribe to and publish on, the required handlers to use as well as any configuration requirements.

In order to move an aggregator from one device to another, each device is sent an updated configuration, removing the aggregator from one device and adding it to another

##Delegator

##Commander

##Coordinator

#Messaging
Work to date has leverage MQTT as the underlying messaging protocol. Each device and deployed role has a unique set of MQTT topics to publish on or subscribe to.

MQTT wildcards are not used as part of the protocol.

Messages from the platform to the deployment start with an uppercase letter, while messages sent from devices start with a lowercase letter. This allows for responses to each message class (e.g. "X/some_path" message to execute a command will be replied to with "x/same_path")


## Device Messaging

###Onboarding

###Configuration

###Heath

In order to interact with devices, the following communication topics are used

- Configuration ("C/device_path")
Each role has associated configuration data
Work to date has focused on leveraging MQTT as the underlying messaging protocol, but there should be no reason why other messaging protocols or standards could not be used.


Take a look at specification.md for the latest draft. It's still very early days, so feel free to help out and contribute to the specification
