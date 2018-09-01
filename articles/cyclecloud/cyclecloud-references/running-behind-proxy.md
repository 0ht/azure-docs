---
title: Azure CycleCloud using Web Proxy | Microsoft Docs
description: Running Azure CycleCloud from behind HTTP/HTTPs Proxy.
services: azure cyclecloud
author: mvrequa
ms.prod: cyclecloud
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: mirequa
---

# Configuring CycleCloud to use a HTTP/HTTPs Proxy

CycleCloud can be configured to use a webproxy for all internet-bound traffic. This is 
only supported for password-less web proxies.

## Configuring the CycleCloud JVM

The runtime properties for CycleCloud are stored in
_/opt/cycle_server/config/cycle_server.properties_.

By default this file will have a setting `webServerJvmOptions=`
set to blank. Change this setting to reflect the hostname/ip and port of the web proxy.

As an example, if your web proxy port is reached at `http://10.10.0.2:3127` then
update the configuration as follows:
```ini
webServerJvmOptions= -Dhttp.proxyHost=10.10.0.2 -Dhttp.proxyPort=3127
```

## Configuring Data Transfers

Enabling use of proxy in data transfers is configured in a distinct way. To enable
use of proxy for data transfers, go into the CycleCloud GUI and navigate to the
**Settings** tab from the left frame, and double click on the **Data Transfers** row.
In the configuration dialog that pops up, check that **Proxy Enabled** is checked and enter
the proxy details in the text fields at the bottom.

Finally, restart CycleCloud:  
```bash
/opt/cycle_server/cycle_server restart && /opt/cycle_server/cycle_server await_startup
```

When this operation is completed, CycleCloud web UI will be ready and the
orchestration and data transfer engines.

> [!NOTE]
> The pogo command-line tool doesn't currently support web proxy
