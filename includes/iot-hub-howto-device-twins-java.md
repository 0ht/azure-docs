---
title: Get started with Azure IoT Hub device twins (Java)
titleSuffix: Azure IoT Hub
description: How to use Azure IoT Hub device twins and the Azure IoT SDKs for Java to create and simulate devices, add tags to device twins, and execute IoT Hub queries. 
author: kgremban
ms.author: kgremban
ms.service: iot-hub
ms.devlang: java
ms.topic: include
ms.date: 07/20/2024
ms.custom: mqtt, devx-track-java, devx-track-extended-java
---

## Create a device application

Device applications can read and write twin reported properties, and be notified of desired twin property changes that have been set by a backend application or IoT Hub.

This section describes how to create device application code that:

* Retrieves a device twin and examine a reported property
* Updates reported device twin desired properties

The [DeviceClient](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient) class exposes all the methods you require to interact with device twins from the device.

### Import statements

Use the following import statements to access the IoT Hub device SDK.

```java
import com.microsoft.azure.sdk.iot.device.*;
import com.microsoft.azure.sdk.iot.device.DeviceTwin.*;
```

### Connect to the device

To connect to the device:

* Use [IotHubClientProtocol](/java/api/com.microsoft.azure.sdk.iot.device.iothubclientprotocol) to choose a transport protocol. For example:

  ```java
  IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
  ```

* Use the `DeviceClient` constructor to add the device hub primary connection string and protocol. See the prerequisites section for how to look up the device primary connection string.

  ```java
  String connString = "{device connection string}";
  DeviceClient client = new DeviceClient(connString, protocol);
  ```

* Use [open](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient?#com-microsoft-azure-sdk-iot-device-deviceclient-open()) to connect the device to IoT hub. If the client is already open, the method does nothing.

  ```java
  client.open(true);
  ```

### Retrieve a device twin and examine reported properties

To read reported properties, use [getTwin](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwin?#com-microsoft-azure-sdk-iot-service-devicetwin-devicetwin-gettwin(com-microsoft-azure-sdk-iot-service-devicetwin-devicetwindevice)) to retrieve the current twin reported properties.

For example:

```java
System.out.println("Getting current twin");
twin = client.getTwin();
System.out.println("Received current twin:");
System.out.println(twin);
```

### Update reported device twin properties

After retrieving the current twin, you can begin making reported property updates. You can make reported property updates without getting the current twin as long as you have the correct reported properties version. If you send reported properties and receive a "precondition failed" error, then your reported properties version is out of date. Get the latest version by calling `getTwin` again.

To update reported properties:

* Call [getReportedProperties](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?#com-microsoft-azure-sdk-iot-service-devicetwin-devicetwindevice-getreportedproperties()) to fetch the twin reported properties into a [TwinCollection](/java/api/com.microsoft.azure.sdk.iot.deps.twin.twincollection) object.

* Use [put](/java/api/com.microsoft.azure.sdk.iot.deps.twin.twincollection?#com-microsoft-azure-sdk-iot-deps-twin-twincollection-put(java-lang-string-java-lang-object)) to update a reported property. Call `put` for each reported property update.

* Use [updateReportedProperties](/java/api/com.microsoft.azure.sdk.iot.device.devicetwin.devicetwin?#com-microsoft-azure-sdk-iot-device-devicetwin-devicetwin-updatereportedproperties(java-util-set(com-microsoft-azure-sdk-iot-device-devicetwin-property))) to update the group of reported properties that were updated using the `put` method.

For example:

```java
TwinCollection reportedProperties = twin.getReportedProperties();
int newTemperature = new Random().nextInt(80);
reportedProperties.put("HomeTemp(F)", newTemperature);
System.out.println("Updating reported property \"HomeTemp(F)\" to value " + newTemperature);
ReportedPropertiesUpdateResponse response = client.updateReportedProperties(reportedProperties);
System.out.println("Successfully set property \"HomeTemp(F)\" to value " + newTemperature);
```

### Subscribe to desired property changes

Call [subscribeToDesiredProperties](/java/api/com.microsoft.azure.sdk.iot.device.internalclient?#com-microsoft-azure-sdk-iot-device-internalclient-subscribetodesiredproperties(java-util-map(com-microsoft-azure-sdk-iot-device-devicetwin-property-com-microsoft-azure-sdk-iot-device-devicetwin-pair(com-microsoft-azure-sdk-iot-device-devicetwin-propertycallback(java-lang-string-java-lang-object)-java-lang-object)))) to subscribe to desired properties. This client will receive a callback each time a desired property is updated. That callback will either contain the full desired properties set, or only the updated desired property depending on how the desired property was changed.

This example subscribes to desired propery changes. Any desired property changes will be passed to a handler named `DesiredPropertiesUpdatedHandler`.

```java
client.subscribeToDesiredProperties(new DesiredPropertiesUpdatedHandler(), null);
```

In this example, the `DesiredPropertiesUpdatedHandler` desired property change callback handler calls [getDesiredProperties](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?#com-microsoft-azure-sdk-iot-service-devicetwin-devicetwindevice-getdesiredproperties()) to retrieve the property changes, then prints out the updated twin properties.

```java
  private static class DesiredPropertiesUpdatedHandler implements DesiredPropertiesCallback
  {
      @Override
      public void onDesiredPropertiesUpdated(Twin desiredPropertyUpdateTwin, Object context)
      {
          if (twin == null)
          {
              // No need to care about this update because these properties will be present in the twin retrieved by getTwin.
              System.out.println("Received desired properties update before getting current twin. Ignoring this update.");
              return;
          }

          // desiredPropertyUpdateTwin.getDesiredProperties() contains all the newly updated desired properties as well as the new version of the desired properties
          twin.getDesiredProperties().putAll(desiredPropertyUpdateTwin.getDesiredProperties());
          twin.getDesiredProperties().setVersion(desiredPropertyUpdateTwin.getDesiredProperties().getVersion());
          System.out.println("Received desired property update. Current twin:");
          System.out.println(twin);
      }
  }
```

### SDK sample

The SDK includes this [Device Twin Sample](https://github.com/Azure/azure-iot-sdk-java/tree/main/iothub/device/iot-device-samples/device-twin-sample).

## Create a backend application

A backend application:

* Runs independently of a device and IoT Hub
* Connects to a device through IoT Hub
* Can read device reported and desired properties, write device desired properties, and run device queries

This section describes how to create a backend application that:

* Updates device twin tags
* Queries devices using filters on the tags and properties

The `ServiceClient` [DeviceTwin](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwin) class contains methods that services can use to access device twins.

### Connect to the IoT hub service client

To connect to IoT Hub to view and update device twin information:

* Create a [DeviceTwinClientOptions](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwinclientoptions?#com-microsoft-azure-sdk-iot-service-devicetwin-devicetwinclientoptions-devicetwinclientoptions()) object. These options are passed to the `DeviceTwin` object.
* Use a [DeviceTwin](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwin?#com-microsoft-azure-sdk-iot-service-devicetwin-devicetwin-devicetwin(java-lang-string)) constructor to create the connection to IoT hub. The `DeviceTwin` object handles the communication with your IoT hub.
* The [DeviceTwinDevice](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice) object represents the device twin with its properties and tags.

```java
import com.microsoft.azure.sdk.iot.service.devicetwin.*;
import com.microsoft.azure.sdk.iot.service.exceptions.IotHubException;

import java.io.IOException;
import java.util.HashSet;
import java.util.Set;

public static final String iotHubConnectionString = "{youriothubconnectionstring}";
public static final String deviceId = "myDeviceId";
public static final String region = "US";
public static final String plant = "Redmond43";

// Get the DeviceTwin and DeviceTwinDevice objects
DeviceTwinClientOptions twinOptions = new DeviceTwinClientOptions();
DeviceTwin twinClient = new DeviceTwin(iotHubConnectionString,twinOptions);
DeviceTwinDevice device = new DeviceTwinDevice(deviceId);
```

### Update device twin fields

To update device twin fields:

* Use [getTwin](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwin?#com-microsoft-azure-sdk-iot-service-devicetwin-devicetwin-gettwin(com-microsoft-azure-sdk-iot-service-devicetwin-devicetwindevice)) to retrieve the device twin fields.

  This example retrieves and prints the device twins.

  ```java
  // Get the device twin from IoT Hub
  System.out.println("Device twin before update:");
  twinClient.getTwin(device);
  System.out.println(device);
  ```

* Use a `HashSet` object to `add` a group of twin tag pairs.

* Use [setTags](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwindevice?#com-microsoft-azure-sdk-iot-service-devicetwin-devicetwindevice-settags(java-util-set(com-microsoft-azure-sdk-iot-service-devicetwin-pair))) to add a group of tag pairs from a `tags` object to a `DeviceTwinDevice` object.

* Use [updateTwin](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwin?#com-microsoft-azure-sdk-iot-service-devicetwin-devicetwin-updatetwin(com-microsoft-azure-sdk-iot-service-devicetwin-devicetwindevice)) to update the twin in the IoT hub.

  This example updates the region and plant device twin tags for a device twin.

  ```java
  // Update device twin tags if they are different
  // from the existing values
  String currentTags = device.tagsToString();
  if ((!currentTags.contains("region=" + region) && !currentTags.contains("plant=" + plant))) {

  // Create the tags and attach them to the DeviceTwinDevice object
  Set<Pair> tags = new HashSet<Pair>();
  tags.add(new Pair("region", region));
  tags.add(new Pair("plant", plant));
  device.setTags(tags);

  // Update the device twin in IoT Hub
  System.out.println("Updating device twin");
  twinClient.updateTwin(device);
  }

  // Retrieve the device twin with the tag values from IoT Hub
  System.out.println("Device twin after update:");
  twinClient.getTwin(device);
  System.out.println(device);
  ```

### Create a device twin query

This section demonstrates two device twin queries. Device twin queries are SQL queries that return a result set of device twins.

The [Query](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.query) class contains methods that can be used to create SQL-style queries to IoT Hub for twins, jobs, device jobs or raw data.

To create a device query:

1. Use [createSqlQuery](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.sqlquery?#com-microsoft-azure-sdk-iot-service-devicetwin-sqlquery-createsqlquery(java-lang-string-com-microsoft-azure-sdk-iot-service-devicetwin-sqlquery-fromtype-java-lang-string-java-lang-string)) to build the twins SQL query.

1. Use [queryTwin](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwin?#com-microsoft-azure-sdk-iot-service-devicetwin-devicetwin-querytwin(java-lang-string-java-lang-integer)) to execute the query.

1. Use [hasNextDeviceTwin](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwin?#com-microsoft-azure-sdk-iot-service-devicetwin-devicetwin-hasnextdevicetwin(com-microsoft-azure-sdk-iot-service-devicetwin-query)) to check if there's another device twin in the result set.

1. Use [getNextDeviceTwin](/java/api/com.microsoft.azure.sdk.iot.service.devicetwin.devicetwin?#com-microsoft-azure-sdk-iot-service-devicetwin-devicetwin-getnextdevicetwin(com-microsoft-azure-sdk-iot-service-devicetwin-query)) to retrieve the next device twin from the result set.

This example queries two IoT hub queries. Each query returns a maximum of 100 devices.

```java
// Query the device twins in IoT Hub
System.out.println("Devices in Redmond:");

// Construct the query
SqlQuery sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43'", null);

// Run the query, returning a maximum of 100 devices
Query twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 100);
while (twinClient.hasNextDeviceTwin(twinQuery)) {
  DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
  System.out.println(d.getDeviceId());
}

System.out.println("Devices in Redmond using a cellular network:");

// Construct the query
sqlQuery = SqlQuery.createSqlQuery("*", SqlQuery.FromType.DEVICES, "tags.plant='Redmond43' AND properties.reported.connectivityType = 'cellular'", null);

// Run the query, returning a maximum of 100 devices
twinQuery = twinClient.queryTwin(sqlQuery.getQuery(), 3);
while (twinClient.hasNextDeviceTwin(twinQuery)) {
  DeviceTwinDevice d = twinClient.getNextDeviceTwin(twinQuery);
  System.out.println(d.getDeviceId());
}
```
