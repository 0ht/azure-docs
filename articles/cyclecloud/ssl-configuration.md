---
title: Azure CycleCloud SSL Configuration | Microsoft Docs
description: Enable SSL for secure transfers in Azure CycleCloud.
services: azure cyclecloud
author: KimliW
ms.prod: cyclecloud
ms.devlang: na
ms.topic: conceptual
ms.date: 08/01/2018
ms.author: a-kiwels
---

# SSL Configuration

## Enable SSL

SSL can easily be enabled by editing the `cycle_server.properties` file found within the CycleCloud installation directory. Open the `cycle_server.properties` file with a text editor and set the following values appropriately:

``` properties
# True if SSL is enabled
webServerEnableHttps=true
```

Please note that when editing the `cycle_server.properties` file, it is important that you first look for pre-existing keyvalue definitions in the file. If there is more than one definition, the **last** one is in effect.

The default SSL port for CycleCloud is port 8443. If you'd like to run encrypted web communications on some other port, you can change the `webServerSslPort` property to the new port value. Please make sure the `webServerSslPort` and the `webServerPort` values **DO NOT CONFLICT**.

After editing your `cycle_server.properties` file, you will need to restart CycleCloud for the encrypted communication channel to activate. On Windows, use `C:\Program Files\CycleServer\cycle_server.cmd restart` and in Linux, use `/opt/cycle_server/cycle_server restart`.

Assuming you did not change the SSL port for CycleCloud when configuring it for encrypted communications, you can now go to `http://<my CycleCloud address>:8443/` to verify the SSL connection.

> [!NOTE]
> If the HTTPS URL does not work, check the`<CycleCloud Home>/logs/tomcat.log` and `<CycleCloud Home>/logs/cycle_server.log` for error messages that might indicate why the encrypted channel is not responding.

## Self-Generated Certificates

If you do not have a certificate from a Certificate Authority (CA) such as VeriSign, you can use the auto-generated self-signed certificate provided with Azure CycleCloud. This is a quick way to start using SSL at no cost, but most web browsers will display a warning stating a trusted authority has not verified the certificate being used to encrypt the channel. For some cases, like internal CycleCloud deployments on secure networks, this is acceptable. Users will have to add an exception to their browser to view the site, but the contents and the session will otherwise be encrypted as expected.

> [!WARNING]
> The Azure CycleCloud self-signed certificate has a shortened shelf life. When it expires, browsers will re-issue the warning about the SSL certificates being untrusted. Users will have to explicitly accept them to view the web console.

## Working With CA-Generated Certificates

Using a CA-generated certificate will allow web access to your CycleCloud installation without displaying the trusted certificate error. To start the process, first run:

``` CLI
./cycle_server keystore create_request <FQDN>
```

You will be asked to provide a domain name, which is the "Common Name" field on the signed certificate. This will generate a new self-signed certificate for the specified domain and write a cycle_server.csr file. You must provide the CSR to a certificate authority, and they will provide the final signed certificate (which will be referred to as server.crt below). You will also need the root certificates and any intermediate ones used in the chain between your new certificate and the root certificate. The CA should provide these for you. If they have provided them bundled as a single certificate file, you can import them with the following command:

``` CLI
./cycle_server keystore import server.crt
```

If they provided multiple certificate files, you should import them all at once appending the names to that same command, separated by spaces:

``` CLI
./cycle_server keystore import server.crt ca_cert_chain.crt
```

### Import Existing Certificates

If you have previously created a CA or self-signed certificate, you can update CycleCloud to use it with the following command:

``` CLI
./cycle_server keystore update
```

## Configuring CycleCloud to use Native HTTPS

By default, Azure CycleCloud is configured to use the standard Java IO HTTPS
implementation. This default works well on all supported platforms.

To improve performance when running on Linux platforms, CycleCloud may
optionally be configured to use the Tomcat Native HTTPS implementation.

To enable Native HTTPS on Linux, add the `webServerEnableHttps` and `webServerUseNativeHttps` attributes to your cycle_server.properties file. Open the cycle_server.properties file with a text editor and set the following values:

``` properties
# Turn on HTTPS
webServerEnableHttps = true

# Use Native HTTPS connector
webServerUseNativeHttps = true
```

## Backwards compatibility for TLS 1.0 and 1.1

By default, the Java and Native HTTPS connectors will be configured to use only the
TLS 1.2 protocol. If you need to offer TLS 1.0 or 1.1 protocols for older web clients, you may
opt-in for backwards compatibility.

Open the cycle_server.properties file with a text editor, and look for the `sslEnabledProtocols`
attribute:

``` properties
sslEnabledProtocols="TLSv1.2"
```

Change the attribute to a `+` delimited list of protocols you wish to support.

``` properties
sslEnabledProtocols="TLSv1.0+TLSv1.1+TLSv1.2"
```

## Turning Off Unencrypted Communications

Once you have tested the encrypted communication channel you may wish
to prevent unencrypted (HTTP) access to your CycleCloud installation. To
turn off unencrypted communications open your cycle_server.properties
file in a text editor. Look for the `webServerEnableHttp` property and
change it to:

``` properties
# HTTP
webServerEnableHttp=false
```

Save the changes and restart CycleCloud. HTTP access to CycleCloud will be disabled.
