---
title: Include file
description: Include file
services: azure-communication-services
author: glorialimicrosoft
ms.service: azure-communication-services
ms.subservice: advanced-messaging
ms.date: 02/02/2024
ms.topic: include
ms.custom: include file
ms.author: memontic
---

## Templates with no parameters
If the template takes no parameters, you don't need to supply the values or bindings when creating the `MessageTemplate`.

```csharp
var messageTemplate = new MessageTemplate(templateName, templateLanguage); 
``````

### Example
Here is the sample template named `sample_template` takes no parameters.

:::image type="content" source="../../media/template-messages/sample-template-details-azure-portal.png" lightbox="../../media/template-messages/sample-template-details-azure-portal.png" alt-text="Screenshot that shows template details for template named sample_template.":::

Assemble the `MessageTemplate` by referencing the target template's name and language.

```csharp
string templateName = "sample_template"; 
string templateLanguage = "en_us"; 

var sampleTemplate = new MessageTemplate(templateName, templateLanguage); 
``````

## Templates with text parameters in the body
Use `MessageTemplateText` to define parameters in the body denoted with double brackets surrounding a number, such as `{{1}}`. The number, indexed started at 1, indicates the order in which the binding values must be supplied to create the message template. Including parameters not in the template is invalid.

Template definition with two parameter:
```json
{
  "type": "BODY",
  "text": "Message with two parameters: {{1}} and {{2}}"
}
``````

### Examples
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

```csharp
string templateName = "sample_shipping_confirmation"; 
string templateLanguage = "en_us"; 

var threeDays = new MessageTemplateText("threeDays", "3");

WhatsAppMessageTemplateBindings bindings = new();
bindings.Body.Add(new(threeDays.Name));

MessageTemplate shippingConfirmationTemplate  = new(templateName, templateLanguage);
shippingConfirmationTemplate.Bindings = bindings;
shippingConfirmationTemplate.Values.Add(threeDays);
``````

## Templates with media parameter in the header
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

Message template assembly for image media:
```csharp
var url = new Uri("< Your media URL >");

var media = new MessageTemplateImage("image", url);
WhatsAppMessageTemplateBindings bindings = new();
bindings.Header.Add(new(media.Name));

var messageTemplate = new MessageTemplate(templateName, templateLanguage);
template.Bindings = bindings;
template.Values.Add(media);
``````

### Examples
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

```csharp
string templateName = "sample_movie_ticket_confirmation"; 
string templateLanguage = "en_us"; 
var imageUrl = new Uri("https://aka.ms/acsicon1");

var image = new MessageTemplateImage("image", imageUrl);
var title = new MessageTemplateText("title", "Contoso");
var time = new MessageTemplateText("time", "July 1st, 2023 12:30PM");
var venue = new MessageTemplateText("venue", "Southridge Video");
var seats = new MessageTemplateText("seats", "Seat 1A");

WhatsAppMessageTemplateBindings bindings = new();
bindings.Header.Add(new(image.Name));
bindings.Body.Add(new(title.Name));
bindings.Body.Add(new(time.Name));
bindings.Body.Add(new(venue.Name));
bindings.Body.Add(new(seats.Name));

MessageTemplate movieTicketConfirmationTemplate = new(templateName, templateLanguage);
movieTicketConfirmationTemplate.Values.Add(image);
movieTicketConfirmationTemplate.Values.Add(title);
movieTicketConfirmationTemplate.Values.Add(time);
movieTicketConfirmationTemplate.Values.Add(venue);
movieTicketConfirmationTemplate.Values.Add(seats);
movieTicketConfirmationTemplate.Bindings = bindings;
``````

### More Examples
- VIDEO: [Use sample template sample_happy_hour_announcement](#use-sample-template-sample_happy_hour_announcement)
- DOCUMENT: [Use sample template sample_flight_confirmation](#use-sample-template-sample_flight_confirmation)

## Templates with location in the header

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

### Example
sample_movie_location template

:::image type="content" source="../../media/template-messages/sample-location-based-template.jpg" lightbox="../../media/template-messages/sample-location-based-template.jpg" alt-text="Screenshot that shows template details for template named sample_location_template.":::

Location based Message template assembly:
```csharp
 var location = new MessageTemplateLocation("location");
 location.LocationName = "Pablo Morales";
 location.Address = "1 Hacker Way, Menlo Park, CA 94025";
 location.Position = new Azure.Core.GeoJson.GeoPosition(longitude: 122.148981, latitude: 37.483307);

 WhatsAppMessageTemplateBindings location_bindings = new();
 location_bindings.Header.Add(new(location.Name));

 var messageTemplateWithLocation = new MessageTemplate(templateNameWithLocation, templateLanguage);
 messageTemplateWithLocation.Values.Add(location);
 messageTemplateWithLocation.Bindings = location_bindings;
``````

## Templates with quick reply buttons
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
```csharp
string templateName = "sample_issue_resolution";
string templateLanguage = "en_us";

var name = new MessageTemplateText(name: "name", text: "Kat");
var yes = new MessageTemplateQuickAction(name: "Yes"){ Payload =  "Kat said yes" };
var no = new MessageTemplateQuickAction(name: "No") { Payload = "Kat said no" };

WhatsAppMessageTemplateBindings bindings = new();
bindings.Body.Add(new(name.Name));
bindings.Buttons.Add(new(WhatsAppMessageButtonSubType.QuickReply.ToString(), yes.Name));
bindings.Buttons.Add(new(WhatsAppMessageButtonSubType.QuickReply.ToString(), no.Name));

MessageTemplate issueResolutionTemplate = new(templateName, templateLanguage);
issueResolutionTemplate.Values.Add(name);
issueResolutionTemplate.Values.Add(yes);
issueResolutionTemplate.Values.Add(no);
issueResolutionTemplate.Bindings = bindings;
``````

## Templates with call to action buttons
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

### Example
sample_purchase_feedback template
This sample template adds a button with a dynamic URL link to the message. It also uses an image in the header and a text parameter in the body.
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
```csharp
string templateName = "sample_purchase_feedback";
string templateLanguage = "en_us";
var imageUrl = new Uri("https://aka.ms/acsicon1");

var image = new MessageTemplateImage(name: "image", uri: imageUrl);
var product = new MessageTemplateText(name: "product", text: "coffee");
var urlSuffix = new MessageTemplateQuickAction(name: "text") { Text = "survey-code" };

WhatsAppMessageTemplateBindings bindings = new();
bindings.Header.Add(new(image.Name));
bindings.Body.Add(new(product.Name));
bindings.Buttons.Add(new(WhatsAppMessageButtonSubType.Url.ToString(), urlSuffix.Name));

MessageTemplate purchaseFeedbackTemplate = new("sample_purchase_feedback", "en_us");
purchaseFeedbackTemplate.Values.Add(image);
purchaseFeedbackTemplate.Values.Add(product);
purchaseFeedbackTemplate.Values.Add(urlSuffix);
purchaseFeedbackTemplate.Bindings = bindings;
``````
