#HIP - the Hierarchical IoT Protocol

HIP is a high level protocol for the Internet of Things. 

At the core of HIP is the device, an abstract representation of an IoT thing/device/gateway. Rather than focusing on the physical device configuration, HIP is concerned about how each device can add value to an IoT deployment. HIP achieves this by defining a number of roles which can be deployed and moved across any device.

- Sensor
- Controller
- Aggregator
- Delegator (previously referred to as a broker, but changed to avoid confusion)
- Commander
- Coordinator

##Device
As an abstract device, 
Each role has associated configuration data
Work to date has focused on leveraging MQTT as the underlying messaging protocol, but there should be no reason why other messaging protocols or standards could not be used.
Take a look at specification.md for the latest draft. It's still very early days, so feel free to help out and contribute to the specification
