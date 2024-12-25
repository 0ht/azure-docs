---
title: Include file
description: Include file
services: azure-communication-services
author: shamkh
ms.service: azure-communication-services
ms.subservice: advanced-messaging
ms.date: 12/20/2024
ms.topic: include
ms.custom: include file
ms.author: shamkh
---

### Prerequisites

- [WhatsApp Business Account registered with your Azure Communication Services resource](../../connect-whatsapp-business-account.md).

- Active WhatsApp phone number to receive messages.

- [Python](https://www.python.org/downloads/) 3.7+ for your operating system.

### Setting up

#### Create a new Python application

In a terminal or console window, create a new folder for your application and navigate to it.

```console
mkdir messages-quickstart && cd messages-quickstart
```

#### Install the package

You need to use the Azure Communication Messages client library for Python [version 1.0.0](https://pypi.org/project/azure-communication-messages) or above.

From a console prompt, execute the following command:

```console
pip install azure-communication-messages
```

#### Set up the app framework

Create a new file called `messages-quickstart.py` and add the basic program structure.

```console
type nul > messages-quickstart.py   
```

#### Basic program structure
```python
import os

class MessagesQuickstart(object):
    print("Azure Communication Services - Advanced Messages SDK Quickstart")

if __name__ == '__main__':
    messages = MessagesQuickstart()
```

### Send Template message with no parameters
If the template takes no parameters, you don't need to supply the values or bindings when creating the `MessageTemplate`.

#### Example
Here is the sample template named `sample_template` takes no parameters.

:::image type="content" source="../../media/template-messages/sample-template-details-azure-portal.png" lightbox="../../media/template-messages/sample-template-details-azure-portal.png" alt-text="Screenshot that shows template details for template named sample_template.":::

Assemble the `MessageTemplate` by referencing the target template's name and language.

```python
input_template: MessageTemplate = MessageTemplate(name="gathering_invitation", language="ca")  # Name of the WhatsApp Template
```

### Send Template message with text parameters in the body
Use `MessageTemplateText` to define parameters in the body denoted with double brackets surrounding a number, such as `{{1}}`. The number, indexed started at 1, indicates the order in which the binding values must be supplied to create the message template. Including parameters not in the template is invalid.

Template definition with two parameter:
```json
{
  "type": "BODY",
  "text": "Message with two parameters: {{1}} and {{2}}"
}
```

#### Examples
sample_shipping_confirmation template
:::image type="content" source="../../media/template-messages/sample-shipping-confirmation-details-azure-portal.png" lightbox="../../media/template-messages/sample-shipping-confirmation-details-azure-portal.png" alt-text="Screenshot that shows template details for template named sample_shipping_confirmation.":::

In this sample, the body of the template has one parameter:
```json
{
  "type": "BODY",
  "text": "Your package has been shipped. It will be delivered in {{1}} business days."
},
```

Parameters are defined with the `MessageTemplateValue` values and `MessageTemplateWhatsAppBindings` bindings. Use the values and bindings to assemble the `MessageTemplate`.

```python
# Setting template options
templateName = "sample_shipping_confirmation"
templateLanguage = "en_us" 
shippingConfirmationTemplate: MessageTemplate = MessageTemplate(name=templateName, language=templateLanguage )
threeDays = MessageTemplateText(name="threeDays", text="3")
bindings = WhatsAppMessageTemplateBindings(body=[WhatsAppMessageTemplateBindingsComponent(ref_value=threeDays.name)])
shippingConfirmationTemplate.bindings = bindings
shippingConfirmationTemplate.template_values=[threeDays]
template_options = TemplateNotificationContent(
    channel_registration_id=self.channel_id, to=[self.phone_number], template=shippingConfirmationTemplate
)
```

### Send Template message with media parameter in the header
Use `MessageTemplateImage`, `MessageTemplateVideo`, or `MessageTemplateDocument` to define the media parameter in a header.

Template definition with image media parameter in header:
```json
{
  "type": "HEADER",
  "format": "IMAGE"
},
```

The "format" can have different media types supported by WhatsApp. In the .NET SDK, each media type uses a corresponding MessageTemplateValue type.

| Format   | MessageTemplateValue Type | File Type |
|----------|---------------------------|-----------|
| IMAGE    | `MessageTemplateImage`    | png, jpg  |
| VIDEO    | `MessageTemplateVideo`    | mp4       |
| DOCUMENT | `MessageTemplateDocument` | pdf       |

For more information on supported media types and size limits, see [WhatsApp's documentation for message media](https://developers.facebook.com/docs/whatsapp/cloud-api/reference/media#supported-media-types). 


#### Examples
sample_movie_ticket_confirmation template
:::image type="content" source="../../media/template-messages/sample-movie-ticket-confirmation-details-azure-portal.png" lightbox="../../media/template-messages/sample-movie-ticket-confirmation-details-azure-portal.png" alt-text="Screenshot that shows template details for template named sample_movie_ticket_confirmation.":::

In this sample, the header of the template requires an image:
```
{
  "type": "HEADER",
  "format": "IMAGE"
},
```

And the body of the template requires four text parameters:
```json
{
  "type": "BODY",
  "text": "Your ticket for *{{1}}*\n*Time* - {{2}}\n*Venue* - {{3}}\n*Seats* - {{4}}"
},
```

Create one `MessageTemplateImage` and four `MessageTemplateText` variables. Then, assemble your list of `MessageTemplateValue` and your `MessageTemplateWhatsAppBindings` by providing the parameters in the order that the parameters appear in the template content.

```python
 # Setting template options
templateName = "sample_movie_ticket_confirmation"
templateLanguage = "en_us" 
imageUrl = "https://aka.ms/acsicon1"
sample_movie_ticket_confirmation: MessageTemplate = MessageTemplate(name=templateName, language=templateLanguage )
image = MessageTemplateImage(name="image", url=imageUrl)
title = MessageTemplateText(name="title", text="Contoso")
time = MessageTemplateText(name="time", text="July 1st, 2023 12:30PM")
venue = MessageTemplateText(name="venue", text="Southridge Video")
seats = MessageTemplateText(name="seats", text="Seat 1A")

bindings = WhatsAppMessageTemplateBindings(header=[WhatsAppMessageTemplateBindingsComponent(ref_value=image.name)],
                                            body=[WhatsAppMessageTemplateBindingsComponent(ref_value=title.name),
                                                    WhatsAppMessageTemplateBindingsComponent(ref_value=time.name),
                                                    WhatsAppMessageTemplateBindingsComponent(ref_value=venue.name),
                                                    WhatsAppMessageTemplateBindingsComponent(ref_value=seats.name)])
sample_movie_ticket_confirmation.bindings = bindings
sample_movie_ticket_confirmation.template_values=[image,title,time,venue,seats]
template_options = TemplateNotificationContent(
    channel_registration_id=self.channel_id, to=[self.phone_number], template=sample_movie_ticket_confirmation)
```

### Send Template message with quick reply buttons
Use `MessageTemplateQuickAction` to define the payload for quick reply buttons and `MessageTemplateQuickAction` objects have the following three attributes. 

|  Properties   | Description |  Type |
|----------|---------------------------|-----------|
| Name  | The `name` is used to look up the value in `MessageTemplateWhatsAppBindings`. | string|
| Text  | The option quick action 'text'. | string|
| Payload| The `payload` assigned to a button is available in a message reply if the user selects the button.| string |
 
Template definition wth quick reply buttons:
```json
{
  "type": "BUTTONS",
  "buttons": [
    {
      "type": "QUICK_REPLY",
      "text": "Yes"
    },
    {
      "type": "QUICK_REPLY",
      "text": "No"
    }
  ]
}
```

The order that the buttons appear in the template definition should match the order in which the buttons are defined when creating the bindings with `MessageTemplateWhatsAppBindings`.

For more information on the payload in quick reply responses from the user, see WhatsApp's documentation for [Received Callback from a Quick Reply Button](https://developers.facebook.com/docs/whatsapp/cloud-api/webhooks/payload-examples#received-callback-from-a-quick-reply-button).

#### Example
sample_issue_resolution template
:::image type="content" source="../../media/template-messages/sample-issue-resolution-details-azure-portal.png" lightbox="../../media/template-messages/sample-issue-resolution-details-azure-portal.png" alt-text="Screenshot that shows template details for template named sample_issue_resolution.":::

Here, the body of the template requires one text parameter:
```json
{
  "type": "BODY",
  "text": "Hi {{1}}, were we able to solve the issue that you were facing?"
},
```

And the template includes two prefilled reply buttons, `Yes` and `No`.
```json
{
  "type": "BUTTONS",
  "buttons": [
    {
      "type": "QUICK_REPLY",
      "text": "Yes"
    },
    {
      "type": "QUICK_REPLY",
      "text": "No"
    }
  ]
}
```

Create one `MessageTemplateText` and two `MessageTemplateQuickAction` variables. Then, assemble your list of `MessageTemplateValue` and your `MessageTemplateWhatsAppBindings` by providing the parameters in the order that the parameters appear in the template content. The order also matters when defining your binding's buttons.

```python
# Setting template options
templateName = "sample_issue_resolution"
templateLanguage = "en_us" 
shippingConfirmationTemplate: MessageTemplate = MessageTemplate(name=templateName, language=templateLanguage )
name = MessageTemplateText(name="first", text="Joe")
yes = MessageTemplateQuickAction(name="Yes", payload="Joe said yes")
no = MessageTemplateQuickAction(name="No", payload = "Joe said no")
bindings = WhatsAppMessageTemplateBindings(body=[WhatsAppMessageTemplateBindingsComponent(ref_value=name.name)])
bindings.buttons = [WhatsAppMessageTemplateBindingsButton(sub_type=WhatsAppMessageButtonSubType.QUICK_REPLY, ref_value=yes.name),
                    WhatsAppMessageTemplateBindingsButton(sub_type=WhatsAppMessageButtonSubType.QUICK_REPLY, ref_value=no.name)]
shippingConfirmationTemplate.bindings = bindings
shippingConfirmationTemplate.template_values=[name,yes,no]
template_options = TemplateNotificationContent(
    channel_registration_id=self.channel_id, to=[self.phone_number], template=shippingConfirmationTemplate
)
```

### Send Template message with location in the header

Use `MessageTemplateLocation` to define the location parameter in a header.

Template definition for header component requiring location as:
```json
{
  "type": "header",
  "parameters": [
    {
      "type": "location",
      "location": {
        "latitude": "<LATITUDE>",
        "longitude": "<LONGITUDE>",
        "name": "<NAME>",
        "address": "<ADDRESS>"
      }
    }
  ]
}
```

The "format" can require different media types. In the .NET SDK, each media type uses a corresponding MessageTemplateValue type.

|  Properties   | Description |  Type |
|----------|---------------------------|-----------|
| ADDRESS | Address that will appear after the 'NAME' value, below the generic map at the top of the message. | string |
| LATITUDE | Location latitude.  | double       |
| LONGITUDE| Location longitude. | double      |
| LOCATIONNAME | Text that will appear immediately below the generic map at the top of the message. |string|

For more information on location based templates, see [WhatsApp's documentation for message media](https://developers.facebook.com/docs/whatsapp/cloud-api/guides/send-message-templates#location). 

#### Example
sample_movie_location template

:::image type="content" source="../../media/template-messages/sample-location-based-template.jpg" lightbox="../../media/template-messages/sample-location-based-template.jpg" alt-text="Screenshot that shows template details for template named sample_location_template.":::

Location based Message template assembly:
```python
 # Setting template options
        templateName = "sample_movie_location"
        templateLanguage = "en_us" 
        sample_movie_location: MessageTemplate = MessageTemplate(name=templateName, language=templateLanguage )
        name = MessageTemplateText(name="first", text="Joe")
        location = MessageTemplateLocation(name="location", location_name="Pablo Morales",
                                            address="1 Hacker Way, Menlo Park, CA 94025",
                                            latitude=37.483307,longitude=122.148981)
        bindings = WhatsAppMessageTemplateBindings(body=[WhatsAppMessageTemplateBindingsComponent(ref_value=name.name)],
                                                    header=[WhatsAppMessageTemplateBindingsComponent(ref_value=location.name)])
        sample_movie_location.bindings = bindings
        sample_movie_location.template_values=[name,location]
        template_options = TemplateNotificationContent(
            channel_registration_id=self.channel_id, to=[self.phone_number], template=sample_movie_location)
```

### Send Template message with call to action buttons
Use `MessageTemplateQuickAction` to define the url suffix for call to action buttons and `MessageTemplateQuickAction` object have the following three attributes.

|  Properties   | Description |  Type |
|----------|---------------------------|-----------|
| Name  | The `name` is used to look up the value in `MessageTemplateWhatsAppBindings`. | string|
| Text  | The  'text' that is appended to the URL.  | string|

Template definition buttons:
```json
{
  "type": "BUTTONS",
  "buttons": [
    {
      "type": "URL",
      "text": "Take Survey",
      "url": "https://www.example.com/{{1}}"
    }
  ]
}
```

The order that the buttons appear in the template definition should match the order in which the buttons are defined when creating the bindings with `MessageTemplateWhatsAppBindings`.

#### Example
sample_purchase_feedback template
This sample template adds a button with a dynamic URL link to the message. It also uses an image in the header and a text parameter in the body. Create Call to Action button template with **Dynamic** URL type for **View website** action type.
:::image type="content" source="../../media/template-messages/edit-sample-purchase-feedback-whatsapp-manager.png" lightbox="../../media/template-messages/edit-sample-purchase-feedback-whatsapp-manager.png" alt-text="Screenshot that shows editing URL Type in the WhatsApp manager.":::

In this sample, the header of the template requires an image:
```json
{
  "type": "HEADER",
  "format": "IMAGE"
},
```

Here, the body of the template requires one text parameter:
```json
{
  "type": "BODY",
  "text": "Thank you for purchasing {{1}}! We value your feedback and would like to learn more about your experience."
},
```

And the template includes a dynamic URL button with one parameter:
```json
{
  "type": "BUTTONS",
  "buttons": [
    {
      "type": "URL",
      "text": "Take Survey",
      "url": "https://www.example.com/{{1}}"
    }
  ]
}
```

Create one `MessageTemplateImage`, one `MessageTemplateText`, and one `MessageTemplateQuickAction` variable. Then, assemble your list of `MessageTemplateValue` and your `MessageTemplateWhatsAppBindings` by providing the parameters in the order that the parameters appear in the template content. The order also matters when defining your binding's buttons.

```python
# Setting template options
        templateName = "sample_purchase_feedback"
        templateLanguage = "en_us"
        imageUrl = "https://aka.ms/acsicon1" 
        sample_purchase_feedback: MessageTemplate = MessageTemplate(name=templateName, language=templateLanguage )
        name = MessageTemplateText(name="first", text="Joe")
        image = MessageTemplateImage(name="image", url=imageUrl)
        uri_to_click = MessageTemplateQuickAction(name="url", text="questions")

        bindings = WhatsAppMessageTemplateBindings(body=[WhatsAppMessageTemplateBindingsComponent(ref_value=name.name)],
                                                    header=[WhatsAppMessageTemplateBindingsComponent(ref_value=image.name)],
                                                    buttons=[WhatsAppMessageTemplateBindingsButton(sub_type=WhatsAppMessageButtonSubType.URL,
                                                    ref_value=uri_to_click.name)])
        sample_purchase_feedback.bindings = bindings
        sample_purchase_feedback.template_values=[name, image, uri_to_click]
        template_options = TemplateNotificationContent(
            channel_registration_id=self.channel_id, to=[self.phone_number], template=sample_purchase_feedback)
```


## Full example

```python
import os
import sys

sys.path.append("..")

class SendWhatsAppTemplateMessageSample(object):

    connection_string = os.getenv("COMMUNICATION_SAMPLES_CONNECTION_STRING")
    phone_number = os.getenv("RECIPIENT_PHONE_NUMBER")
    channel_id = os.getenv("WHATSAPP_CHANNEL_ID")

    def send_template_message_without_parameters(self):
        from azure.communication.messages import NotificationMessagesClient
        from azure.communication.messages.models import ( TemplateNotificationContent , MessageTemplate )

        # client creation
        messaging_client = NotificationMessagesClient.from_connection_string(self.connection_string)
        input_template: MessageTemplate = MessageTemplate(
            name="<<TEMPLATE_NAME>>",
            language="<<LANGUAGE>>")
        template_options = TemplateNotificationContent(
            channel_registration_id=self.channelRegistrationId,
            to=[self.phone_number],
            template=input_template
        )

        # calling send() with WhatsApp template details.
        message_responses = messaging_client.send(template_options)
        response = message_responses.receipts[0]
        
        if (response is not None):
            print("WhatsApp Templated Message with message id {} was successfully sent to {}"
            .format(response.message_id, response.to))
        else:
            print("Message failed to send")

    def send_template_message_with_parameters(self):

        from azure.communication.messages import NotificationMessagesClient
        from azure.communication.messages.models import (TemplateNotificationContent, MessageTemplate,
        MessageTemplateText, WhatsAppMessageTemplateBindings, WhatsAppMessageTemplateBindingsComponent)

        messaging_client = NotificationMessagesClient.from_connection_string(self.connection_string)

        # Setting template options
        templateName = "sample_shipping_confirmation"
        templateLanguage = "en_us" 
        shippingConfirmationTemplate: MessageTemplate = MessageTemplate(name=templateName, language=templateLanguage )
        threeDays = MessageTemplateText(name="threeDays", text="3")
        bindings = WhatsAppMessageTemplateBindings(body=[WhatsAppMessageTemplateBindingsComponent(ref_value=threeDays.name)])
        shippingConfirmationTemplate.bindings = bindings
        shippingConfirmationTemplate.template_values=[threeDays]
        template_options = TemplateNotificationContent(
            channel_registration_id=self.channel_id, to=[self.phone_number], template=shippingConfirmationTemplate
        )

        # calling send() with whatsapp message details
        message_responses = messaging_client.send(template_options)
        response = message_responses.receipts[0]
        print("WhatsApp text parameters Templated Message with message id {} was successfully sent to {}"
            .format(response.message_id, response.to))

    def send_template_message_with_media(self):

        from azure.communication.messages import NotificationMessagesClient
        from azure.communication.messages.models import (TemplateNotificationContent, MessageTemplate,
        MessageTemplateText, WhatsAppMessageTemplateBindings, WhatsAppMessageTemplateBindingsComponent,
        MessageTemplateImage)

        messaging_client = NotificationMessagesClient.from_connection_string(self.connection_string)

        # Setting template options
        templateName = "sample_movie_ticket_confirmation"
        templateLanguage = "en_us" 
        imageUrl = "https://aka.ms/acsicon1"
        sample_movie_ticket_confirmation: MessageTemplate = MessageTemplate(name=templateName, language=templateLanguage )
        image = MessageTemplateImage(name="image", url=imageUrl)
        title = MessageTemplateText(name="title", text="Contoso")
        time = MessageTemplateText(name="time", text="July 1st, 2023 12:30PM")
        venue = MessageTemplateText(name="venue", text="Southridge Video")
        seats = MessageTemplateText(name="seats", text="Seat 1A")

        bindings = WhatsAppMessageTemplateBindings(header=[WhatsAppMessageTemplateBindingsComponent(ref_value=image.name)],
                                                   body=[WhatsAppMessageTemplateBindingsComponent(ref_value=title.name),
                                                         WhatsAppMessageTemplateBindingsComponent(ref_value=time.name),
                                                         WhatsAppMessageTemplateBindingsComponent(ref_value=venue.name),
                                                         WhatsAppMessageTemplateBindingsComponent(ref_value=seats.name)])

        sample_movie_ticket_confirmation.bindings = bindings
        sample_movie_ticket_confirmation.template_values=[image,title,time,venue,seats]
        template_options = TemplateNotificationContent(
            channel_registration_id=self.channel_id, to=[self.phone_number], template=sample_movie_ticket_confirmation)

        # calling send() with whatsapp message details
        message_responses = messaging_client.send(template_options)
        response = message_responses.receipts[0]
        print("WhatsApp media parameters in templated message header with message id {} was successfully sent to {}"
            .format(response.message_id, response.to))

    def send_template_message_with_buttons(self):

        from azure.communication.messages import NotificationMessagesClient
        from azure.communication.messages.models import (TemplateNotificationContent, MessageTemplate,
        MessageTemplateText, WhatsAppMessageTemplateBindings, WhatsAppMessageTemplateBindingsComponent,
        MessageTemplateQuickAction, WhatsAppMessageTemplateBindingsButton, WhatsAppMessageButtonSubType)

        messaging_client = NotificationMessagesClient.from_connection_string(self.connection_string)

        # Setting template options
        templateName = "sample_issue_resolution"
        templateLanguage = "en_us" 
        shippingConfirmationTemplate: MessageTemplate = MessageTemplate(name=templateName, language=templateLanguage )
        name = MessageTemplateText(name="first", text="Joe")
        yes = MessageTemplateQuickAction(name="Yes", payload="Joe said yes")
        no = MessageTemplateQuickAction(name="No", payload = "Joe said no")
        bindings = WhatsAppMessageTemplateBindings(body=[WhatsAppMessageTemplateBindingsComponent(ref_value=name.name)])
        bindings.buttons = [WhatsAppMessageTemplateBindingsButton(sub_type=WhatsAppMessageButtonSubType.QUICK_REPLY, ref_value=yes.name),
                            WhatsAppMessageTemplateBindingsButton(sub_type=WhatsAppMessageButtonSubType.QUICK_REPLY, ref_value=no.name)]
        shippingConfirmationTemplate.bindings = bindings
        shippingConfirmationTemplate.template_values=[name,yes,no]
        template_options = TemplateNotificationContent(
            channel_registration_id=self.channel_id, to=[self.phone_number], template=shippingConfirmationTemplate
        )

        # calling send() with whatsapp message details
        message_responses = messaging_client.send(template_options)
        response = message_responses.receipts[0]
        print("WhatsApp Quick Button Templated Message with message id {} was successfully sent to {}"
            .format(response.message_id, response.to))

    def send_template_message_with_location(self):

        from azure.communication.messages import NotificationMessagesClient
        from azure.communication.messages.models import (TemplateNotificationContent, MessageTemplate,
        MessageTemplateText, WhatsAppMessageTemplateBindings, WhatsAppMessageTemplateBindingsComponent,
        MessageTemplateLocation)

        messaging_client = NotificationMessagesClient.from_connection_string(self.connection_string)

        # Setting template options
        templateName = "sample_movie_location"
        templateLanguage = "en_us" 
        sample_movie_location: MessageTemplate = MessageTemplate(name=templateName, language=templateLanguage )
        name = MessageTemplateText(name="first", text="Joe")
        location = MessageTemplateLocation(name="location", location_name="Pablo Morales",
                                            address="1 Hacker Way, Menlo Park, CA 94025",
                                            latitude=37.483307,longitude=122.148981)
        bindings = WhatsAppMessageTemplateBindings(body=[WhatsAppMessageTemplateBindingsComponent(ref_value=name.name)],
                                                    header=[WhatsAppMessageTemplateBindingsComponent(ref_value=location.name)])
        sample_movie_location.bindings = bindings
        sample_movie_location.template_values=[name,location]
        template_options = TemplateNotificationContent(
            channel_registration_id=self.channel_id, to=[self.phone_number], template=sample_movie_location)

        # calling send() with whatsapp message details
        message_responses = messaging_client.send(template_options)
        response = message_responses.receipts[0]
        print("WhatsApp Location Templated Message with message id {} was successfully sent to {}"
            .format(response.message_id, response.to))

    def send_template_message_with_call_to_action(self):

        from azure.communication.messages import NotificationMessagesClient
        from azure.communication.messages.models import (TemplateNotificationContent, MessageTemplate,
        MessageTemplateText, WhatsAppMessageTemplateBindings, WhatsAppMessageTemplateBindingsComponent,
        MessageTemplateQuickAction, MessageTemplateImage, WhatsAppMessageTemplateBindingsButton,
        WhatsAppMessageButtonSubType)

        messaging_client = NotificationMessagesClient.from_connection_string(self.connection_string)

        # Setting template options
        templateName = "sample_purchase_feedback"
        templateLanguage = "en_us"
        imageUrl = "https://aka.ms/acsicon1" 
        sample_purchase_feedback: MessageTemplate = MessageTemplate(name=templateName, language=templateLanguage )
        name = MessageTemplateText(name="first", text="Joe")
        image = MessageTemplateImage(name="image", url=imageUrl)
        uri_to_click = MessageTemplateQuickAction(name="url", text="questions")

        bindings = WhatsAppMessageTemplateBindings(body=[WhatsAppMessageTemplateBindingsComponent(ref_value=name.name)],
                                                    header=[WhatsAppMessageTemplateBindingsComponent(ref_value=image.name)],
                                                    buttons=[WhatsAppMessageTemplateBindingsButton(sub_type=WhatsAppMessageButtonSubType.URL,
                                                                                                    ref_value=uri_to_click.name)])
        sample_purchase_feedback.bindings = bindings
        sample_purchase_feedback.template_values=[name, image, uri_to_click]
        template_options = TemplateNotificationContent(
            channel_registration_id=self.channel_id, to=[self.phone_number], template=sample_purchase_feedback)

        # calling send() with whatsapp message details
        message_responses = messaging_client.send(template_options)
        response = message_responses.receipts[0]
        print("WhatsApp Call To Action Templated Message with message id {} was successfully sent to {}"
        .format(response.message_id, response.to))

if __name__ == "__main__":
    sample = SendWhatsAppTemplateMessageSample()
    sample.send_template_message_without_parameters()
    sample.send_template_message_with_parameters()
    sample.send_template_message_with_buttons()
    sample.send_template_message_with_location()
    sample.send_template_message_with_call_to_action()
```

## Run the code

To run the code, make sure you are on the directory where your `messages-quickstart.py` file is.

```console
python messages-quickstart.py
```

```output
Azure Communication Services - Advanced Messages Quickstart
WhatsApp Templated Message with message id <<GUID>> was successfully sent to <<ToRecipient>>
```
