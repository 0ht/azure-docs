---
title: MSI Support to Access Azure services
description: Learn how to provide MSI Support to Access Azure services.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 06/04/2024
---

# MSI Support to access Azure services

Presently in Azure HDInsight non-ESP cluster, User Job accessing Azure resources like SqlDB, Cosmos DB, EH, KV, Kusto either using username and password or using MSI certificate key. This isnn't in line with Microsoft security guidelines.

This article explains the  HDInsight interface and code details to fetch OAuth tokens in a non-ESP cluster.

## Prerequisites

* This feature is available in the latest HDInsight-5.1, 5.0, and 4.0 versions. Make sure you recreated or installed this cluster versions.
* HDInsight Cluster must be with ADL-Gen2 storage as primary storage, which enables MSI based access for this storage. This same MSI used for all job resources access. Ensure the required IAM permissions given to this MSI to access Azure resources.


There are two methods to implement this task.

* Option 1 HDInsight utility and  API usage to fetch access token.
* Option 2 HDInsight utility, TokenCredential Implementation to fetch Access Token.

## Option 1 - HDInsight utility and  API usage to fetch access token

Implemented a convenient utility class to fetch MSI access token by providing target resource URI, which can be EH, KV, Kusto, SqlDB, Cosmos DB etc.

### Prerequisites

1. Make sure the Hadoop's `core-site.xml` and all the client jars from this cluster location `/usr/hdp/<hdi-version>/hadoop/client/*` in the classpath.
1. Make sure the utility jar dependency is added into the application and compile time dependency.

### How to use the API

To fetch the token, you can invoke the API in their Job application code.

```
import com.microsoft.azure.hdinsight.oauthtoken.utils.HdiIdentityTokenServiceUtils;
import com.azure.core.credential.AccessToken;

// uri can be EH, Kusto etc. 
// By default, the Scope is “.default”. 
// We will provide a mechanism to take user supplied scope, in future.
String msiResourceUri = https://vault.azure.net/;
HdiIdentityTokenServiceUtils tokenUtils = new HdiIdentityTokenServiceUtils();
AccessToken token = tokenUtils.getAccessToken(msiResourceUri);
```

### Structure of access token

```
package com.azure.core.credential;
import java.time.OffsetDateTime;
 
/** Represents an immutable access token with a token string and an expiration time 
* in date format. By default, 24hrs is the expiration time out.
*/
public class AccessToken {
  
  public String getToken();
  public OffsetDateTime getExpiresAt();
}
```
## Option 2 -Utility, TokenCredential implementation to fetch access token

Provided `HdiIdentityTokenCredential` feature class, which is the standard implementation of `com.azure.core.credential.TokenCredential` interface.
Make sure, Hadoop's `core-site.xml` and all the client jars from this cluster location `/usr/hdp/<hdi-version>/hadoop/client/*
, azure-core-1.49.0.jar and its transitive deps jars` in classpath.

### Prerequisites

* Hadoop's `core-site.xml` and all the client jars from this cluster location `/usr/hdp/<hdi-version>/hadoop/client/*` in classpath.
* Make sure the utility jar dependency is added into the application and compile time dependency.

### If the client is a Key Vault

We take an example of Azure Key Vault, SecretClient instance, which doesn't directly fetch an access token. It uses a TokenCredential to authenticate, and this credential handles fetching the access token.

```
import com.azure.core.credential.TokenCredential;
import com.azure.security.keyvault.secrets.SecretClient;
import com.azure.security.keyvault.secrets.SecretClientBuilder;
import com.microsoft.azure.hdinsight.oauthtoken.credential.HdiIdentityTokenCredential;
import com.microsoft.azure.hdinsight.oauthtoken.utils.HdiIdentityTokenServiceUtils;

// replace <resource-uri> with your key vault uri
TokenCredential hdiTokenCredential = new HdiIdentityTokenCredential("<resource-uri>");
 
// Create a SecretClient that will be used to call the service.
SecretClient secretClient = new SecretClientBuilder()
              .vaultUrl("<your-key-vault-url>") // replace with your Key Vault URL
              .credential(hdiTokenCredential) // add hdi identity token credential
              .buildClient();

// Retrieve a secret from the key vault.
// Replace with your secret name.
KeyVaultSecret secret = secretClient.getSecret("<your-secret-name>");
```

> [!NOTE]
> This is a standard token credential interface, the Azure resource clients which supported `TokenCredential` is the recommended approach.
