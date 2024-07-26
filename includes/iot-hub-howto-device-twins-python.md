---
title: Get started with Azure IoT Hub device twins (Python)
titleSuffix: Azure IoT Hub
description: How to use Azure IoT Hub device twins and the Azure IoT SDKs for Python to create and simulate devices, add tags to device twins, and execute IoT Hub queries. 
author: kgremban
ms.author: kgremban
ms.service: iot-hub
ms.devlang: python
ms.topic: how-to
ms.date: 07/12/2024
ms.custom: mqtt, devx-track-python, py-fresh-zinc
---

## Create a device application

This section describes how to create a device application that:

* Retrieves a device twin and examine a reported property
* Updates a reported device twin desired property

### Connect to IoT Hub

Call [create_from_connection_string](/python/api/azure-iot-device/azure.iot.device.iothubdeviceclient?#azure-iot-device-iothubdeviceclient-create-from-connection-string) to add the IoT hub primary connection string. See the prerequisites section for how to look up the IoT hub primary connection string.

Then call [connect](/python/api/azure-iot-device/azure.iot.device.iothubdeviceclient?#azure-iot-device-iothubdeviceclient-connect) to connect the device client to an Azure IoT hub.

```python
import asyncio
from azure.iot.device.aio import IoTHubDeviceClient

conn_str = "IOTHUB_DEVICE_CONNECTION_STRING"
device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

# connect the client.
await device_client.connect()
```

### Retrieve a device twin and examine reported properties

Call [get_twin](/python/api/azure-iot-device/azure.iot.device.iothubdeviceclient?#azure-iot-device-iothubdeviceclient-get-twin) to get the device or module twin from the Azure IoT Hub service. This is a synchronous call, meaning that this function will not return until the twin has been retrieved from the service.

This example retrieves the device twin and uses the `print` command to view the device twin in JSON format.

```python
# get the twin
twin = await device_client.get_twin()
print("Twin document:")
print("{}".format(twin))
```

### Update reported device twin properties

Call [patch_twin_reported_properties](/python/api/azure-iot-device/azure.iot.device.iothubdeviceclient?#azure-iot-device-iothubdeviceclient-patch-twin-reported-properties) to update reported properties with the Azure IoT Hub.

This is a synchronous call, meaning that this function will not return until the patch has been sent to the service and acknowledged.

If the service returns an error on the patch operation, this function will raise the appropriate error.

```python
# update the reported properties
reported_properties = {"temperature": random.randint(320, 800) / 10}
print("Setting reported temperature to {}".format(reported_properties["temperature"]))
await device_client.patch_twin_reported_properties(reported_properties)
```

### SDK samples

[get_twin](https://github.com/Azure/azure-iot-sdk-python/blob/main/samples/async-hub-scenarios/get_twin.py) - Connect to a device and retrieve twin information.

[update_twin_reported_properties](https://github.com/Azure/azure-iot-sdk-python/blob/main/samples/async-hub-scenarios/update_twin_reported_properties.py) - Update twin reported properties

[receive_twin_desired_properties](https://github.com/Azure/azure-iot-sdk-python/blob/main/samples/async-hub-scenarios/receive_twin_desired_properties_patch.py) - Update reported properties with the Azure IoT Hub service.

## Create a backend application

This section describes how to create a backend application that:

* Updates device twin tags
* Queries devices using filters on the tags and properties

The [IoTHubRegistryManager](/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager) class exposes all methods required to create a backend application to interact with device twins from the service.

### Connect to IoT hub

First, connect to IoT hub using [from_connection_string](/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?#azure-iot-hub-iothubregistrymanager-from-connection-string).

```python
import sys
from time import sleep
from azure.iot.hub import IoTHubRegistryManager
from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult

# Connect to IoT hub
IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
iothub_registry_manager = IoTHubRegistryManager.from_connection_string(IOTHUB_CONNECTION_STRING)
```

### Update device twin tag

To update a device twin tag:

* Create a tag in JSON format.

* Call [get_twin](/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?#azure-iot-hub-iothubregistrymanager-get-twin) to get a device twin.

* Call [update_twin](/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?#azure-iot-hub-iothubregistrymanager-update-twin) to update tags and desired properties of a device twin.

```python
new_tags = {
        'location' : {
            'region' : 'US',
            'plant' : 'Redmond43'
        }
    }

DEVICE_ID = "[Device Id]"
twin = iothub_registry_manager.get_twin(DEVICE_ID)
twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)
```

### Create a device twin query

This section demonstrates two device twin queries. Device twin queries are SQL queries that return a result set of device twins.

Use a [QuerySpecification](/python/api/azure-iot-hub/azure.iot.hub.protocol.models.queryspecification) object to define a JSON query request.

Use [query_iot_hub](/python/api/azure-iot-hub/azure.iot.hub.iothubregistrymanager?#azure-iot-hub-iothubregistrymanager-query-iot-hub) to query an IoTHub and retrieve device twin information using the query specification.

For example:

```python
query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

print()

query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))
```
