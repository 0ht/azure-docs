---
title: Azure Database for MySQL bindings for Functions
description: Understand how to use Azure Database for MySQL bindings in Azure Functions.
author: JetterMcTedder
ms.topic: reference
ms.custom:
  - build-2023
  - devx-track-extended-java
  - devx-track-js
  - devx-track-python
  - ignite-2023
ms.date: 30/09/2024
ms.author: bspendolini
ms.reviewer: glenga
zone_pivot_groups: programming-languages-set-functions-lang-workers
---

# Azure Database for MySQL bindings for Azure Functions overview

This set of articles explains how to work with [Azure Database for MySQL](/azure/mysql/index) bindings in Azure Functions. Azure Functions supports input bindings, output bindings, and a function trigger for the Azure Database for MySQL.

| Action | Type |
|---------|---------|
| Trigger a function when a change is detected on a MySQL table | [MYSQL trigger](./functions-bindings-azure-mysql-trigger.md) |
| Read data from a database | [Input binding](./functions-bindings-azure-mysql-input.md) |
| Save data to a database |[Output binding](./functions-bindings-azure-mysql-output.md) |

::: zone pivot="programming-language-csharp"

## Install extension

The extension NuGet package you install depends on the C# mode you're using in your function app:

# [Isolated worker model](#tab/isolated-process)

Functions execute in an isolated C# worker process. To learn more, see [Guide for running C# Azure Functions in an isolated worker process](dotnet-isolated-process-guide.md).

Add the extension to your project by installing this [NuGet package](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Extensions.MySql/1.0.3-preview/).

```bash
dotnet add package Microsoft.Azure.Functions.Worker.Extensions.MySql --version 1.0.3-preview
```
<!-- >
To use a preview version of the Microsoft.Azure.Functions.Worker.Extensions.Sql package, add the `--prerelease` flag to the command. You can view preview functionality on the [Azure Functions SQL Extensions release page](https://github.com/Azure/azure-functions-sql-extension/releases).

```bash
dotnet add package Microsoft.Azure.Functions.Worker.Extensions.Sql --prerelease
```

> [!NOTE]
> Breaking changes between preview releases of the Azure Database for MySQL bindings for Azure Functions requires that all Functions targeting the same database use the same version of the mysql extension package. -->

# [In-process model](#tab/in-process)
<!-- >
[!INCLUDE [functions-in-process-model-retirement-note](../../includes/functions-in-process-model-retirement-note.md)]-->

Functions execute in the same process as the Functions host. To learn more, see [Develop C# class library functions using Azure Functions](functions-dotnet-class-library.md).

Add the extension to your project by installing this [NuGet package](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MySql/1.0.3-preview).

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.MySql --version 1.0.3-preview
```
<!-- >
To use a preview version of the Microsoft.Azure.WebJobs.Extensions.Sql package, add the `--prerelease` flag to the command. You can view preview functionality on the [Azure Functions MySQL Extensions release page](https://github.com/Azure/azure-functions-sql-extension/releases).

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Sql --prerelease
``` 

> [!NOTE]
> Breaking changes between preview releases of the Azure Database for MySQL bindings for Azure Functions requires that all Functions targeting the same database use the same version of the MySQL extension package. -->

---

::: zone-end


::: zone pivot="programming-language-javascript, programming-language-powershell"


## Install bundle

The MySQL bindings extension is part of the v4 [extension bundle], which is specified in your host.json project file.

<!-- >
# [Bundle v4.x](#tab/extensionv4)

The extension bundle is specified by the following code in your `host.json` file:

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[4.*, 5.0.0)"
  }
}
``` -->

# [Preview Bundle v4.x](#tab/extensionv4p)

You can use the preview extension bundle by adding or replacing the following code in your `host.json` file:

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle.Preview",
    "version": "[4.*, 5.0.0)"
  }
}
```
<!-- >
You can view preview functionality on the [Azure Functions SQL Extensions release page](https://github.com/Azure/azure-functions-sql-extension/releases). 

> [!NOTE]
> Breaking changes between preview releases of the Azure Database for MySQL bindings for Azure Functions requires that all Functions targeting the same database use the same version of the MySQL extension package. -->

---

::: zone-end


::: zone pivot="programming-language-python"

## Functions runtime

<!-- > [!NOTE]
> Python language support for the SQL bindings extension is available starting with v4.5.0 of the [functions runtime](./set-runtime-version.md#view-and-update-the-current-runtime-version).  You might need to update your install of Azure Functions [Core Tools](functions-run-local.md) for local development. -->


## Install bundle

The MySQL bindings extension is part of the v4 [extension bundle], which is specified in your host.json project file.

<!-- >
# [Bundle v4.x](#tab/extensionv4)

The extension bundle is specified by the following code in your `host.json` file:

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[4.*, 5.0.0)"
  }
}
``` -->

# [Preview Bundle v4.x](#tab/extensionv4p)

You can use the preview extension bundle by adding or replacing the following code in your `host.json` file:

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle.Preview",
    "version": "[4.*, 5.0.0)"
  }
}
```
<!-- >
You can view preview functionality on the [Azure Functions SQL Extensions release page](https://github.com/Azure/azure-functions-sql-extension/releases). 

> [!NOTE]
> Breaking changes between preview releases of the Azure Database for MySQL bindings for Azure Functions requires that all Functions targeting the same database use the same version of the MySQL extension package. -->

---

::: zone-end


::: zone pivot="programming-language-java"


## Install bundle

The SQL bindings extension is part of the v4 [extension bundle], which is specified in your host.json project file.
<!-- >
# [Bundle v4.x](#tab/extensionv4)

The extension bundle is specified by the following code in your `host.json` file:

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[4.*, 5.0.0)"
  }
}
``` -->

# [Preview Bundle v4.x](#tab/extensionv4p)

You can use the preview extension bundle by adding or replacing the following code in your `host.json` file:

```json
{
  "version": "2.0",
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle.Preview",
    "version": "[4.*, 5.0.0)"
  }
}
```
<!-- >
You can view preview functionality on the [Azure Functions SQL Extensions release page](https://github.com/Azure/azure-functions-sql-extension/releases). 

> [!NOTE]
> Breaking changes between preview releases of the Azure Database for MySQL bindings for Azure Functions requires that all Functions targeting the same database use the same version of the MySQL extension package. -->

---

## Update packages

<!-->Add the Java library for MYSQL bindings to your functions project with an update to the `pom.xml` file in your Java Azure Functions project as seen in the following snippet:

```xml
<dependency>
    <groupId>com.microsoft.azure.functions</groupId>
    <artifactId>azure-functions-java-library-sql</artifactId>
    <version>2.1.0</version>
</dependency>
``` -->

You can use the preview extension bundle with an update to the `pom.xml` file in your Java Azure Functions project as seen in the following snippet:

```xml
<dependency>
<groupId>com.microsoft.azure.functions</groupId>
<artifactId>azure-functions-java-library-mysql</artifactId>
<version>1.0.1-preview</version>
</dependency>
```

::: zone-end

## MySQL connection string

Azure Database for MySQL bindings for Azure Functions have a required property for the connection string on all bindings and triggers. These pass the connection string to the MySql.Data.MySqlClient library and supports the connection string as defined in the [MySqlClient ConnectionString documentation](/dev.mysql.com/doc/refman/8.4/en/connecting-using-uri-or-key-value-pairs.html#connecting-using-uri).  Notable keywords include:

- `user` the MySQL user account to provide for the authentication process
- `host` the host on which the server instance is running. The value can be a host name, IPv4 address, or IPv6 address. 
- `port` the TCP/IP network port on which the target MySQL server is listening for connections. 
- `schema` The default database for the connection. If no database is specified, the connection has no default database

## Considerations

- Azure Database for MySQL binding supports version 4.x and later of the Functions runtime.
- Source code for the Azure Database for MySQL bindings can be found in [this GitHub repository](https://github.com/Azure/azure-functions-mysql-extension/tree/main/src).
- This binding requires connectivity to an Azure Database for MySQL.
- Output bindings against tables with columns of spatial data types `GEOMETRY`, `POINT`, or `POLYGON` aren't supported and data upserts will fail.  

## Samples

In addition to the samples for C#, Java, JavaScript, PowerShell, and Python available in the [Azure SQL bindings GitHub repository](https://github.com/Azure/azure-functions-mysql-extension/tree/main/samples), more are available in Azure Samples.


## Next steps

- [Read data from a database (Input binding)](./functions-bindings-azure-mysql-input.md)
- [Save data to a database (Output binding)](./functions-bindings-azure-mysql-output.md)
- [Run a function when data is changed in a SQL table (Trigger)](./functions-bindings-azure-mysql-trigger.md)
<!-- >
- [Learn how to connect Azure Function to Azure SQL with managed identity](./functions-identity-access-azure-sql-with-managed-identity.md) --https://github.com/Azure/azure-functions-sql-extension/tree/main/samples>

[core tools]: ./functions-run-local.md
[extension bundle]: ./functions-bindings-register.md#extension-bundles
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack
