---
title: Get started with Azure IoT Hub device twins (Node.js)
titleSuffix: Azure IoT Hub
description: How to use Azure IoT Hub device twins and the Azure IoT SDKs for Node.js to create and simulate devices, add tags to device twins, and execute IoT Hub queries. 
author: kgremban
ms.author: kgremban
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: how-to
ms.date: 07/20/2024
ms.custom: mqtt, devx-track-js
---

## Create a device application

This section describes how to use the [azure-iot-device](/javascript/api/azure-iot-device) package in the Azure IoT SDK for Node.js to create a device application that:

* Retrieves a device twin and examine a reported property
* Updates a reported device twin desired property

### Install SDK packages

Run these command to install the **azure-iot-device** device SDK, the **azure-iot-device-mqtt** on your development machine:

```cmd/sh
npm init --yes
npm install azure-iot-device azure-iot-device-mqtt --save
```

The [azure-iot-device](/javascript/api/azure-iot-device) package contains objects that interface with IoT devices. This section describes `Client` class code that is used to read and write device twin data.

### Create modules

Create Client, Protocol, errors, and path modules using the installed packages.

```javascript
const Client = require('azure-iot-device').Client;
const Protocol = require('azure-iot-device-mqtt').Mqtt;
```

### Choose a transport protocol

The `Client` object supports these protocols:

* `Amqp`
* `Http` - When using `Http`, the `Client` instance checks for messages from IoT Hub infrequently (a minimum of every 25 minutes).
* `Mqtt`
* `MqttWs`
* `AmqpWs`

For more information about the differences between MQTT, AMQP, and HTTPS support, see [Cloud-to-device communications guidance](../articles/iot-hub/iot-hub-devguide-c2d-guidance.md) and [Choose a communication protocol](../articles/iot-hub/iot-hub-devguide-protocols.md).

This example assigns the AMQP protocol to a `Protocol` variable.

```javascript
const Protocol = require('azure-iot-device-mqtt').Amqp;
```

### Add the IoT Hub string and transport protocol

Call [fromConnectionString](/javascript/api/azure-iot-device/client?#azure-iot-device-client-fromconnectionstring) to establish a device-to-IoT hub connection using these parameters:

* **connStr** - A connection string which encapsulates "device connect" permissions for an IoT hub. The connection string contains Hostname, Device ID & Shared Access Key in this format:
"HostName=<iothub_host_name>;DeviceId=<device_id>;SharedAccessKey=<device_key>". See the prerequisites section for how to look up the device primary connection string.
* **transportCtor** - The transport protocol.

```javascript
const Protocol = require('azure-iot-device-mqtt').Amqp;
let client = Client.fromConnectionString(deviceConnectionString, Protocol);
```

### Open the connection to IoT Hub

Use the [open](/javascript/api/azure-iot-device/client?#azure-iot-device-client-open) method to open a connection between an IoT device and IoT Hub.
Use `.catch(err)` to catch an error and call handler code.

For example:

```javascript
client.open()  //open the connection
.catch((err) => {
  console.error('Could not connect: ' + err.message);
});
```

### Retrieve a device twin and examine reported properties

Use [getTwin](/javascript/api/azure-iot-device/client?#azure-iot-device-client-gettwin-1) to assign the twin object. This object can be used to read device twin information.

```javascript
client.getTwin(function(err, twin) {
if (err)
    console.error('could not get twin');
```

### Update reported device twin properties

Format a variable with the device twin patch. In this example, the patch contains a device twin `connectivity` update value of `cellular`.

Use [update](/javascript/api/azure-iothub/twin?#azure-iothub-twin-update) to update the device twin with the patch provided as the first parameter.

```javascript
var patch = {
    connectivity: {
        type: 'cellular'
    }
}
twin.properties.reported.update(patch, function(err) {
    if (err) {
        console.error('could not update twin');
    } else {
        console.log('twin state reported');
        process.exit();
    }
});
```

### SDK samples

* [Simple sample device twin](https://github.com/Azure/azure-iot-sdk-node/blob/main/device/samples/javascript/simple_sample_device_twin.js)

* [Simple sample device twin with SAS](https://github.com/Azure/azure-iot-sdk-node/blob/main/device/samples/javascript/simple_sample_device_with_sas.js)

## Create a backend application

This section describes how to create a backend application that:

* Updates device twin tags
* Queries devices using filters on the tags and properties

### Install SDK packages

Run these command to install **azure-iothub** on your development machine:

```cmd/sh
npm init --yes
npm install azure-iothub --save
```

The [ServiceClient](/javascript/api/azure-iothub/client) class contains methods that services can use to receive file upload notifications.

### Connect to IoT hub

The `Registry` object exposes all the methods required to interact with device twins from the service.

Use [fromConnectionString](/javascript/api/azure-iothub/client?#azure-iothub-client-fromconnectionstring) to connect to IoT hub. See the prerequisites section for how to look up the IoT hub primary connection string.

```javascript
'use strict';
var iothub = require('azure-iothub');
var connectionString = '{iot hub connection string}';
var registry = iothub.Registry.fromConnectionString(connectionString);
```

### Retrieve device twin tags and update a device twin

Use [getTwin](/javascript/api/azure-iothub/registry?#azure-iothub-registry-gettwin-1) to retrieve the device twin object.

In this example, the device twin is retrieved for `myDeviceId`.

Format a variable that contains the device twin patch.

Use [update](/javascript/api/azure-iothub/twin?#azure-iothub-twin-update-1) to update a device twin tag.

In this example, the patch contains a device twin `location` update of `region: 'US', plant: 'Redmond43'`.

```javascript
     registry.getTwin('myDeviceId', function(err, twin){
         if (err) {
             console.error(err.constructor.name + ': ' + err.message);
         } else {
             var patch = {
                 tags: {
                     location: {
                         region: 'US',
                         plant: 'Redmond43'
                   }
                 }
             };

             twin.update(patch, function(err) {
               if (err) {
                 console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
               } else {
                 console.log(twin.deviceId + ' twin updated successfully');
                 queryTwins();
               }
             });
         }
     });
```

### Create a device twin query

Use [createQuery](/javascript/api/azure-iothub/registry?#azure-iothub-registry-createquery) to create a query that can be run on an IoT hub instance to find information about devices or jobs.

`createQuery` includes two parameters:

* **sqlQuery** - The query written as an SQL string.
* **pageSize** - The desired number of results per page (optional. default: 1000, max: 10000).

When the code creates the query object, it specifies the maximum number of returned documents in the second parameter. The query object contains a `hasMoreResults` boolean property that you can use to invoke the `nextAsTwin` methods multiple times to retrieve all results. A method called `next` is available for results that are not device twins, for example, the results of aggregation queries.

This example query selects only the device twins of devices located in the `Redmond43` plant.

```javascript
var queryTwins = function() {
var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
query.nextAsTwin(function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
    }
});
```

This example query refines the first query to select only the devices that are also connected through cellular network.

```javascript
query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
query.nextAsTwin(function(err, results) {
    if (err) {
        console.error('Failed to fetch the results: ' + err.message);
    } else {
        console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
    }
});
};
```
