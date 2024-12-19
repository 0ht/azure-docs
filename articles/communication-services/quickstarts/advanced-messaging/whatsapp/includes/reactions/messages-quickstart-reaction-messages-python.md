---
title: Include file
description: Include file
services: azure-communication-services
author: shamkh
manager: camilo.ramirez
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.subservice: advanced-messaging
ms.date: 02/20/2024
ms.topic: include
ms.custom: Include file
ms.author: shamkh
---

## Prerequisites

- [WhatsApp Business Account registered with your Azure Communication Services resource](../../connect-whatsapp-business-account.md).

- Active WhatsApp phone number to receive messages.

- [Python](https://www.python.org/downloads/) 3.7+ for your operating system.

## Setting up

### Create a new Python application

In a terminal or console window, create a new folder for your application and navigate to it.

```console
mkdir messages-quickstart && cd messages-quickstart
```

### Install the package

You need to use the Azure Communication Messages client library for Python [version 1.2.0](https://pypi.org/project/azure-communication-messages) or above.

From a console prompt, execute the following command:

```console
pip install azure-communication-messages
```

### Set up the app framework

Create a new file called `reaction-messages-quickstart.py` and add the basic program structure.

```console
type nul > reaction-messages-quickstart.py   
```
#### Basic program structure
```python
import os

class MessagesQuickstart(object):
    print("Azure Communication Services - Advanced Messages SDK Quickstart For Reaction Types.")

if __name__ == '__main__':
    messages = MessagesQuickstart()
```

## Object model
The following classes and interfaces handle some of the major features of the Azure Communication Services Messages SDK for Python.

| Name                        | Description                                                                                            |
|-----------------------------|--------------------------------------------------------------------------------------------------------|
| NotificationMessagesClient  | This class connects to your Azure Communication Services resource. It sends the messages.              |
| ReactionNotificationContent | This class defines the reaction content of the messages with emoji and reply message id.|

## Common configuration
Follow these steps to add the necessary code snippets to the messages-quickstart.py python program.

- [Authenticate the client](#authenticate-the-client)
- [Set channel registration ID](#set-channel-registration-id)
- [Set recipient list](#set-recipient-list)

[!INCLUDE [Common setting for using Advanced Messages SDK](../common-setting.md)]

## Code examples
Follow these steps to add the necessary code snippets to the messages-quickstart.py python program.

- [Send a Reaction messages to a WhatsApp user message](#send-a-reaction-messages-to-a-whatsapp-user-message)

## Send a Reaction messages to a WhatsApp user message
Advanced Messages SDK allows Contoso to send reaction WhatsApp messages, which initiated by WhatsApp users. To send text messages below details are required:
- [WhatsApp Channel ID](#set-channel-registration-id)
- [Recipient Phone Number in E16 format](#set-recipient-list)
- Reaction content can be created using given properties:

| Action type   | Description |
|----------|---------------------------|
| ReactionNotificationContent    | This class defines title of the group content and array of the group.    |
| Emoji    | This property defines the unnicode for emoji character.   |
| Reply Message Id | This property defines Id of the message to be replied with emoji. |

> [!IMPORTANT]
> To send a text message to a WhatsApp user, the WhatsApp user must first send a message to the WhatsApp Business Account. For more information, see [Start sending messages between business and WhatsApp user](#start-sending-messages-between-a-business-and-a-whatsapp-user).

In this example, business sends reaction to the user message"
```python
    def send_reaction_message(self):

        from azure.communication.messages import NotificationMessagesClient
        from azure.communication.messages.models import ReactionNotificationContent

        messaging_client = NotificationMessagesClient.from_connection_string(self.connection_string)

        video_options = ReactionNotificationContent(
            channel_registration_id=self.channel_id,
            to=[self.phone_number],
            emoji="\uD83D\uDE00",
            message_id="<<ReplyMessageIdGuid>>",
        )

        # calling send() with whatsapp message details
        message_responses = messaging_client.send(video_options)
        response = message_responses.receipts[0]
        print("Message with message id {} was successful sent to {}".format(response.message_id, response.to))
```

To run send_reaction_message(), update the [main method](#basic-program-structure)
```python
    #Calling send_reaction_message()
    messages.send_reaction_message()
```

:::image type="content" source="../../media/interactive-reaction-sticker/reaction-message.png" lightbox="../../media/interactive-reaction-sticker/reaction-message.png" alt-text="Screenshot that shows WhatsApp CTA interactive message from Business to User.":::

## Run the code

To run the code, make sure you are on the directory where your `reaction-messages-quickstart.py` file is.

```console
python reaction-messages-quickstart.py
```

```output
Azure Communication Services - Advanced Messages Quickstart
WhatsApp Reaction Message with message id <<GUID>> was successfully sent to <<ToRecipient>>
```

## Full sample code

```python
import os

class MessagesQuickstart(object):
    print("Azure Communication Services - Advanced Messages SDK Quickstart using connection string.")
    # Advanced Messages SDK implementations goes in this section.
   
    connection_string = os.getenv("COMMUNICATION_SERVICES_CONNECTION_STRING")
    phone_number = os.getenv("RECIPIENT_PHONE_NUMBER")
    channelRegistrationId = os.getenv("WHATSAPP_CHANNEL_ID")

    def send_reaction_message(self):

        from azure.communication.messages import NotificationMessagesClient
        from azure.communication.messages.models import ReactionNotificationContent

        messaging_client = NotificationMessagesClient.from_connection_string(self.connection_string)

        video_options = ReactionNotificationContent(
            channel_registration_id=self.channel_id,
            to=[self.phone_number],
            emoji="\uD83D\uDE00",
            message_id="<<ReplyMessageIdGuid>>",
        )

        # calling send() with whatsapp message details
        message_responses = messaging_client.send(video_options)
        response = message_responses.receipts[0]
        print("WhatsApp Reaction Message with message id {} was successful sent to {}".format(response.message_id, response.to))

if __name__ == '__main__':
    messages = MessagesQuickstart()
    messages.send_reaction_message()
```

> [!NOTE]
> Please update all placeholder variables in the above code.

### Other Samples

You can review and download other sample codes for Python Messages SDK on [GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/messages-quickstart).
