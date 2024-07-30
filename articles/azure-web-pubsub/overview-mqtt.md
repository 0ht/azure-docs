---
title: MQTT support in Azure Web PubSub Service
description: Get an overview of Azure Web PubSub's support for the MQTT protocols, understand typical use case scenarios to use MQTT in Azure Web PubSub, and learn the key benefits of MQTT in Azure Web PubSub.
keywords: MQTT, MQTT on Azure Web PubSub
author: Y-Sindo
ms.author: zityang
ms.date: 07/15/2024
ms.service: azure-web-pubsub
ms.topic: overview
---
# Overview: MQTT in Azure Web PubSub Service

## Introduction

Azure Web PubSub service now natively supports MQTT over WebSocket.

You can use MQTT protocols in Web PubSub service for the following scenarios:

1. Pub/Sub among MQTT clients and Web PubSub native clients.
1. Broadcast messages to MQTT clients.
1. Get notifications for MQTT clients lifetime events.

## Key Features

**Standard MQTT Protocols Support**:

Web PubSub service supports MQTT 3.1.1 and 5.0 protocols in a standard way that any MQTT SDK that support WebSocket transport can connect to Web PubSub. Users who wish to use Web PubSub in a programming language that doesn't have a native Web PubSub SDK can still connect and communicate using MQTT.

**Communication Between Various Protocols**:

MQTT clients can communicate with clients of other Web PubSub protocols.

**Easy MQTT adoption for Current Web PubSub Users**:

Current users of Azure Web PubSub can use MQTT protocol  with minimal modifications to their existing upstream servers. The Web PubSub REST API is already equipped to handle MQTT connections, simplifying the transition process.

## Supported MQTT Features
Web PubSub support MQTT protocol version 3.1.1 and 5.0. The supported features include but not limited to :

1. All the levels of Quality Of Service including at most once, at least once and exactly once.
1. Last Will & Testament
1. Client Certificate Authentication

### Additional Features Supported For MQTT 5.0

1. Message Expiry Interval and Session Expiry Interval
2. Subscription Identifier.
3. Assigned Client ID.
4. Flow Control
5. Server-Sent Disconnect

## How MQTT Is Adapted Into Web PubSub's System

This section assumes you have basic knowledge about MQTT protocols and Web PubSub. You can find the definitions of MQTT terms in [MQTT V5.0.0 Spec](https://docs.oasis-open.org/mqtt/mqtt/v5.0/os/mqtt-v5.0-os.html#_Toc3901003). You can also learn basic concepts of Web PubSub in [Basic Concepts](./key-concepts.md).

The following table shows similar or equivalent term mappings between MQTT and Web PubSub. It help you understand how we adapt MQTT concepts into the Web PubSub's system. It's essential if you want to use the [data-plane REST API](./reference-rest-api-data-plane.md) or [client event handlers](./howto-develop-eventhandler.md) to interact with MQTT clients.

[!INCLUDE [MQTT-Term-Mappings](includes/mqtt-term-mappings.md)]

## Client Authentication And Authorization

In general, a server to authenticate and authorize MQTT clients is required. There are two workflows supported by Web PubSub to authenticate and authorize MQTT clients.

* Workflow 1: The MQTT client gets a [JWT(JSON Web Token)](https://jwt.io) from somewhere with its credential, usually from an auth server. Then the client includes the token in the WebSocket upgrading request to the Web PubSub service, and the Web PubSub service validates the token and auth the client. This workflow is enabled by default.

![MQTT Auth Workflow With JWT](./media/howto-develop-mqtt-websocket-clients/mqtt-jwt-auth-workflow.png)

* Workflow 2: The MQTT client sends an MQTT CONNECT packet after it establishes a WebSocket connection with the service, then the service calls an API in the upstream server. The upstream server can auth the client according to the username and password fields in the MQTT connection request, and the TLS certificate from the client. This workflow needs explicit configuration.
<!--Add link to tutorial and configuration-->

![MQTT Auth Workflow With Upstream Server](./media/howto-develop-mqtt-websocket-clients/mqtt-upstream-auth-workflow.png)

These two workflows can be used individually or in combination. If they're used in together, the auth result in the latter workflow would be honored by the service.

For details on client authentication and authorization, see [How To Connect MQTT Clients to Web PubSub](./howto-connect-mqtt-websocket-client.md).

## Client Lifetime Event Notification

You can register event handlers to get notification when a Web PubSub client connection is started or ended, that is, an MQTT session started or ended.

* [Event handler in Azure Web PubSub service](./howto-develop-eventhandler.md)
* [MQTT CloudEvents Protocol](./reference-mqtt-cloud-events.md)

## REST API Support

You can use REST API to do the following things:
1. Publish messages to a topic, a connection, a Web PubSub user, or all the connections.
1. Manage client permissions and subscriptions.

[REST API specification for MQTT](./reference-rest-api-mqtt.md)

## Next step

> [!div class="nextstepaction"]
> [Quickstart: Pub/Sub among MQTT clients](./quickstarts-pubsub-among-mqtt-clients.md)

> [!div class="nextstepaction"]
> [How To Connect MQTT Clients to Web PubSub](./howto-connect-mqtt-websocket-client.md)

