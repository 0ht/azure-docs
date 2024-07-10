---
title: Upload files from devices to Azure IoT Hub (Java)
titleSuffix: Azure IoT Hub
description: How to upload files from a device to the cloud using Azure IoT device SDK for Java. Uploaded files are stored in an Azure storage blob container.
author: kgremban
ms.author: kgremban
ms.service: iot-hub
ms.devlang: java
ms.topic: how-to
ms.date: 07/01/2024
ms.custom: amqp, mqtt, devx-track-java, devx-track-extended-java
---

## Upload a file from a device

Follow this procedure for uploading a file from a device to IoT Hub:

* Connect to IoT Hub
* Get a SAS URI from IoT Hub
* Upload the file to Azure Storage
* Send file upload status notification to IoT Hub

The [DeviceClient](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient) class contains methods that a device can use to upload files to IoT Hub.

### Connection protocol

File upload operations always use HTTPS. `DeviceClient` can define the `IotHubClientProtocol` for other services like telemetry, device method, and device twin.

For example:

```java
IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;
```

### Connect to IoT Hub

```java
String connString = "Your device connection string here";
DeviceClient client = new DeviceClient(connString, protocol);
```

### Get a SAS URI from Iot Hub

Call [getFileUploadSasUri](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient?#com-microsoft-azure-sdk-iot-device-deviceclient-getfileuploadsasuri(com-microsoft-azure-sdk-iot-deps-serializer-fileuploadsasurirequest)) to obtain a [FileUploadSasUriResponse](/java/api/com.microsoft.azure.sdk.iot.deps.serializer.fileuploadsasuriresponse) object.

`FileUploadSasUriResponse` includes these methods and return values. The return values can be passed to file upload methods.

* `getCorrelationId())` - Correlation ID
* `getContainerName())` - Container name
* `getBlobName())` - Blob name
* `getBlobUri())` - Blob URI

For example:

```java
FileUploadSasUriResponse sasUriResponse = client.getFileUploadSasUri(new FileUploadSasUriRequest(file.getName()));

System.out.println("Successfully got SAS URI from IoT Hub");
System.out.println("Correlation Id: " + sasUriResponse.getCorrelationId());
System.out.println("Container name: " + sasUriResponse.getContainerName());
System.out.println("Blob name: " + sasUriResponse.getBlobName());
System.out.println("Blob Uri: " + sasUriResponse.getBlobUri());
```

### Upload the file to Azure Storage

Pass the blob URI endpoint to [BlobClientBuilder](/java/api/com.azure.storage.blob.blobclientbuilder?#com-azure-storage-blob-blobclientbuilder-buildclient()) to create the [BlobClient](/java/api/com.azure.storage.blob.blobclient) object.

```java
BlobClient blobClient =
    new BlobClientBuilder()
        .endpoint(sasUriResponse.getBlobUri().toString())
        .buildClient();
```

Call [uploadFromFile](/java/api/com.azure.storage.blob.blobclient?#com-azure-storage-blob-blobclient-uploadfromfile(java-lang-string)) to upload the file to blob storage.

```java
String fullFileName = "Path of the file to upload";
blobClient.uploadFromFile(fullFileName);
```

## Send file upload status notification to IoT Hub

Send an upload status notification to IoT hub after a file upload attempt.

Create a [FileUploadCompletionNotification](/java/api/com.microsoft.azure.sdk.iot.deps.serializer.fileuploadcompletionnotification?#com-microsoft-azure-sdk-iot-deps-serializer-fileuploadcompletionnotification-fileuploadcompletionnotification(java-lang-string-java-lang-boolean)) object. Pass the `correlationId` and `isSuccess` file upload success status. Pass an `isSuccess` `true` value when file upload was successful, `false` when not.

`FileUploadCompletionNotification` must be called even when the file upload fails. IoT Hub has a fixed number of SAS URI allowed to be active at any given time. Once you're done with the file upload, you should free your SAS URI so that other SAS URI can be generated. If a SAS URI isn't freed through this API, then it frees itself eventually based on how long SAS URI are configured to live on an IoT Hub.

This example passes a successful status.

```java
FileUploadCompletionNotification completionNotification = new FileUploadCompletionNotification(sasUriResponse.getCorrelationId(), true);
client.completeFileUpload(completionNotification);
```

### Close the client

Free the `client` resources.

```java
client.closeNow();
```

## Receive a file upload notification

You can create an application to receive file upload notifications.

The [ServiceClient](/java/api/com.azure.core.annotation.serviceclient) class contains methods that services can use to receive file upload notifications.

### Connect the client to IoT hub

Create a `IotHubServiceClientProtocol` object. The connection uses the `AMQPS` protocol.

Call `createFromConnectionString` to connect to IoT hub.

```java
private static final String connectionString = "{Your service connection string here}";
private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;
ServiceClient sc = ServiceClient.createFromConnectionString(connectionString, protocol);
```

### Check for file upload status

* Create a [getFileUploadNotificationReceiver](/java/api/com.microsoft.azure.sdk.iot.service.fileuploadnotificationreceiver) object.
* Use [open](/java/api/com.microsoft.azure.sdk.iot.service.fileuploadnotificationreceiver?#com-microsoft-azure-sdk-iot-service-fileuploadnotificationreceiver-open()) to connect to IoT Hub.
* Call [receive](/java/api/com.microsoft.azure.sdk.iot.service.fileuploadnotificationreceiver?#com-microsoft-azure-sdk-iot-service-fileuploadnotificationreceiver-receive()) to check for the file upload status. This method returns a [fileUploadNotification](/java/api/com.microsoft.azure.sdk.iot.service.fileuploadnotification?view=azure-java-stable) object. If an upload notice is received, you can view upload status fields using [fileUploadNotification](/java/api/com.microsoft.azure.sdk.iot.service.fileuploadnotification) methods.

For example:

```java
FileUploadNotificationReceiver receiver = sc.getFileUploadNotificationReceiver();
receiver.open();
FileUploadNotification fileUploadNotification = receiver.receive(2000);

if (fileUploadNotification != null)
{
    System.out.println("File Upload notification received");
    System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
    System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
    System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
    System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
    System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
    System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
}
else
{
    System.out.println("No file upload notification");
}

// Close the receiver object
receiver.close();
```

## Samples

There are two Java file upload [samples](https://github.com/Azure/azure-iot-sdk-java/tree/main/iothub/device/iot-device-samples/file-upload-sample/src/main/java/samples/com/microsoft/azure/sdk/iot).
