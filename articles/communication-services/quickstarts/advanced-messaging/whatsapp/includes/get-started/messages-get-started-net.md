---
title: Include file
description: Include file
services: azure-communication-services
author: memontic
ms.service: azure-communication-services
ms.subservice: advanced-messaging
ms.date: 07/15/2024
ms.topic: include
ms.custom: include file
ms.author: memontic
---

## Prerequisites

- [WhatsApp Business Account registered with your Azure Communication Services resource](../../connect-whatsapp-business-account.md)
- Active WhatsApp phone number to receive messages

- .NET development environment (such as [Visual Studio](https://visualstudio.microsoft.com/downloads/), [Visual Studio Code](https://code.visualstudio.com/Download), or [.NET CLI](https://dotnet.microsoft.com/download))

## Setting up

### Create the .NET project

#### [Visual Studio](#tab/visual-studio)

To create your project, follow the tutorial at [Create a .NET console application using Visual Studio](/dotnet/core/tutorials/with-visual-studio).

To compile your code, press <kbd>Ctrl</kbd>+<kbd>F7</kbd>.

#### [Visual Studio Code](#tab/vs-code)

To create your project, follow the tutorial at [Create a .NET console application using Visual Studio Code](/dotnet/core/tutorials/with-visual-studio-code).

Build and run your program by running the following commands in the Visual Studio Code Terminal (View > Terminal).
```console
dotnet build
dotnet run
```

#### [.NET CLI](#tab/dotnet-cli)

First, create your project.
```console
dotnet new console -o AdvancedMessagingQuickstart
```

Next, navigate to your project directory and build your project.

```console
cd AdvancedMessagingQuickstart
dotnet build
```

---

### Install the package

Install the Azure.Communication.Messages NuGet package to your C# project.

#### [Visual Studio](#tab/visual-studio)
 
1. Open the NuGet Package Manager at `Project` > `Manage NuGet Packages...`.   
2. Search for the package `Azure.Communication.Messages`.   
3. Install the latest release.

#### [Visual Studio Code](#tab/vs-code)

1. Open the Visual Studio Code terminal ( `View` > `Terminal` ).
2. Install the package by running the following command.
```console
dotnet add package Azure.Communication.Messages
```

#### [.NET CLI](#tab/dotnet-cli)

Install the package by running the following command.
```console
dotnet add package Azure.Communication.Messages
```

---

### Set up the app framework

Open the *Program.cs* file in a text editor.   

Replace the contents of your *Program.cs* with the following code:

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Azure;
using Azure.Communication.Messages;

namespace AdvancedMessagingQuickstart
{
    class Program
    {
        public static async Task Main(string[] args)
        {
            Console.WriteLine("Azure Communication Services - Send WhatsApp Messages");

            // Quickstart code goes here
        }
    }
}
```

To use the Advanced Messaging features, we add a `using` directive to include the `Azure.Communication.Messages` namespace.

```csharp
using Azure.Communication.Messages;
```

## Object model
The following classes and interfaces handle some of the major features of the Azure Communication Services Advance Messaging SDK for .NET.

| Name                        | Description                                                                                            |
|-----------------------------|--------------------------------------------------------------------------------------------------------|
| NotificationMessagesClient  | This class connects to your Azure Communication Services resource. It sends the messages.              |
| MessageTemplate             | This class defines which template you use and the content of the template properties for your message. |
| TemplateNotificationContent | This class defines the "who" and the "what" of the template message you intend to send.                |
| TextNotificationContent     | This class defines the "who" and the "what" of the text message you intend to send.                    |
| MediaNotificationContent    | This class defines the "who" and the "what" of the media message you intend to send.                   |

> [!NOTE]
> Please find the SDK reference [here](/dotnet/api/azure.communication.messages).

## Common configuration
Follow these steps to add the necessary code snippets to the messages-quickstart.py python program.

- [Authenticate the client](#authenticate-the-client)
- [Set channel registration ID](#set-channel-registration-id)
- [Set recipient list](#set-recipient-list)
- [Start sending messages between a business and a WhatsApp user](#start-sending-messages-between-a-business-and-a-whatsapp-user)

[!INCLUDE [Common setting for using Advanced Messages SDK](../common-setting.md)]

## Code examples

Follow these steps to add the necessary code snippets to the Main function of your *Program.cs* file.
- [Send a text message to a WhatsApp user](#send-a-text-message-to-a-whatsapp-user)
- [Send a image media message to a WhatsApp user](#send-a-image-media-message-to-a-whatsapp-user)
- [Send a document media message to a WhatsApp user](#send-a-document-media-message-to-a-whatsapp-user)
- [Send an audio media message to a WhatsApp user](#send-an-audio-media-message-to-a-whatsapp-user)
- [Send a video media message to a WhatsApp user](#send-a-video-media-message-to-a-whatsapp-user)

> [!IMPORTANT]
> To send a text message to a WhatsApp user, the WhatsApp user must first send a message to the WhatsApp Business Account. For more information, see [Start sending messages between business and WhatsApp user](#start-sending-messages-between-a-business-and-a-whatsapp-user).

### Send a text message to a WhatsApp user

The Messages SDK allows Contoso to send WhatsApp text messages, which initiated WhatsApp users initiated. To send a text message, you need:
- [Authenticated NotificationMessagesClient](#authenticate-the-client)
- [WhatsApp channel ID](#set-channel-registration-id)
- [Recipient phone number in E16 format](#set-recipient-list)
- Message body/text to be sent

In this example, we reply to the WhatsApp user with the text "Thanks for your feedback.\n From Notification Messaging SDK".
Assemble then send the text message:
```csharp
// Assemble text message
var textContent = 
    new TextNotificationContent(channelRegistrationId, recipientList, "Thanks for your feedback.\n From Notification Messaging SDK");

// Send text message
Response<SendMessageResult> sendTextMessageResult = 
    await notificationMessagesClient.SendAsync(textContent);
```

### Send a image media message to a WhatsApp user

The Messages SDK allows Contoso to send WhatsApp media messages to WhatsApp users. To send an embedded media message, you need:
- [Authenticated NotificationMessagesClient](#authenticate-the-client)
- [WhatsApp channel ID](#set-channel-registration-id)
- [Recipient phone number in E16 format](#set-recipient-list)
- Uri of the Image Media

> [!IMPORTANT]
> As of SDK version 1.1.0, `MediaNotificationContent` is being deprecated for images. We encourage you to use `ImageNotificationContent` for sending images and explore other content-specific classes for other media types like `DocumentNotificationContent`, `VideoNotificationContent`, and `AudioNotificationContent`.

Assemble the image message:  
```csharp
var imageLink = new Uri("https://example.com/image.jpg");
var imageNotificationContent = new ImageNotificationContent(channelRegistrationId, recipientList, imageLink)  
{  
    Caption = "Check out this image."  
};
```

Send the image message:  
```csharp
var imageResponse = await notificationMessagesClient.SendAsync(imageNotificationContent);
```

### Send a document media message to a WhatsApp user
The Messages SDK allows Contoso to send WhatsApp media messages to WhatsApp users. To send an embedded media message, you need:
- [Authenticated NotificationMessagesClient](#authenticate-the-client)
- [WhatsApp channel ID](#set-channel-registration-id)
- [Recipient phone number in E16 format](#set-recipient-list)
- Uri of the Document Media

Assemble the document content:  
```csharp
var documentLink = new Uri("https://example.com/document.pdf");
var documentNotificationContent = new DocumentNotificationContent(channelRegistrationId, recipientList, documentLink)  
{  
    Caption = "Check out this document.",  
    FileName = "document.pdf"  
};
```

Send the document message:  
```csharp
var documentResponse = await notificationMessagesClient.SendAsync(documentNotificationContent);
```

### Send a video media message to a WhatsApp user
The Messages SDK allows Contoso to send WhatsApp media messages to WhatsApp users. To send an embedded media message, you need:
- [Authenticated NotificationMessagesClient](#authenticate-the-client)
- [WhatsApp channel ID](#set-channel-registration-id)
- [Recipient phone number in E16 format](#set-recipient-list)
- Uri of the Video Media

Assemble the video message:  
```csharp
var videoLink = new Uri("https://example.com/video.mp4");
var videoNotificationContent = new VideoNotificationContent(channelRegistrationId, recipientList, videoLink)  
{  
    Caption = "Check out this video."  
};
```

Send the video message:  
```csharp
var videoResponse = await notificationMessagesClient.SendAsync(videoNotificationContent);
```

### Send an audio media message to a WhatsApp user
The Messages SDK allows Contoso to send WhatsApp media messages to WhatsApp users. To send an embedded media message, you need:
- [Authenticated NotificationMessagesClient](#authenticate-the-client)
- [WhatsApp channel ID](#set-channel-registration-id)
- [Recipient phone number in E16 format](#set-recipient-list)
- Uri of the Audio Media

Assemble the audio message:  
```csharp
var audioLink = new Uri("https://example.com/audio.mp3");
var audioNotificationContent = new AudioNotificationContent(channelRegistrationId, recipientList, audioLink);
```

Send the audio message:  
```csharp
var audioResponse = await notificationMessagesClient.SendAsync(audioNotificationContent);
```

### Run the code

Build and run your program.  

To send a text or media message to a WhatsApp user, there must be an active conversation between the WhatsApp Business Account and the WhatsApp user.  
If you don't have an active conversation, for the purposes of this quickstart, you should add a wait between sending the template message and sending the text message. This added delay gives you enough time to reply to the business on the user's WhatsApp account. For reference, the full example at [Sample code](#full-sample-code) prompts for manual user input before sending the next message.
  
If successful, you receive three messages on the user's WhatsApp account.

#### [Visual Studio](#tab/visual-studio)

1. To compile your code, press <kbd>Ctrl</kbd>+<kbd>F7</kbd>.
1. To run the program without debugging, press <kbd>Ctrl</kbd>+<kbd>F5</kbd>.

#### [Visual Studio Code](#tab/vs-code)

Build and run your program by running the following commands in the Visual Studio Code Terminal (View > Terminal).
```console
dotnet build
dotnet run
```

#### [.NET CLI](#tab/dotnet-cli)

Build and run your program.
```console
dotnet build
dotnet run
```

---

## Full sample code

[!INCLUDE [Full code example with .NET](./messages-get-started-full-example-net.md)]
