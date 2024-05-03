---
author: cherylmc
ms.author: cherylmc
ms.date: 05/03/2024
ms.service: vpn-gateway
ms.topic: include

---
Microsoft now has an authorized registered Microsoft Entra ID Enterprise app for the latest version of the Azure VPN Client. With previous versions of the Azure VPN Client, you were required to manually register (or integrate) the Azure VPN Client with your Microsoft Entra tenant. This created an App ID representing the identity of the Azure VPN Client application. This type of manual registration is considered less secure due to necessary required permissions on the tenant. Additionally, the registration process is susceptible to user errors.

When you configure a P2S VPN gateway using the new Microsoft-registered App ID and corresponding Audience Value, you skip the manual app registration process. The App ID is already created for you and your tenant is automatically able to use it with no additional registration steps. To better understand the difference between the types of application objects, see [How and why applications are added to Microsoft Entra ID](https://learn.microsoft.com/entra/identity-platform/how-applications-are-added). When possible, we recommend that you configure new P2S gateways using Microsoft-registered App ID and corresponding Audience values instead of manually registering the Azure VPN Client app.

Considerations and limitations:

* A P2S VPN gateway can only support one Audience value. It can't support multiple Audience values simultaneously.

* At this time, the newer Microsoft-registered app doesn't support as many Audience values as the older, manually registered app. If you need an Audience value for anything other than Azure Public or a Custom audience value, use the older manually registered method and values.

* The Azure VPN Client for Linux isn't backward compatible with P2S gateways configured to use the older Audience values that align with the manually registered app. The Linux client does support custom Audience values.

* The latest version of the Azure VPN Client for both Windows and macOS are backward compatible. To use the appropriate Audience value for the Microsoft-registered app, you must update the Azure VPN Client to the latest version.

The following table shows the versions of the Azure VPN Client that are supported for each App ID and the corresponding available Audience values.

|App ID | Supported Audience value| Supported clients|
|---|---|---|
|Microsoft-registered| - Azure Public: `c632b3df-fb67-4d84-bdcf-b95ad541b5c8` |- Linux<br>- Windows<br>- macOS |
|Manually registered| - Azure Public: `41b23e61-6c1e-4545-b367-cd054e0ed4b4`<br>- Azure Government: `51bb15d4-3a4f-4ebf-9dca-40096fe32426`<br>- Azure Germany: `538ee9e6-310a-468d-afef-ea97365856a9`<br>- Microsoft Azure operated by 21Vianet: `49f817b6-84ae-4cc0-928c-73f27289b3aa` | - Windows<br> - macOS|
|Custom | `<custom-app-id>` | - Linux<br>- Windows<br> - macOS |