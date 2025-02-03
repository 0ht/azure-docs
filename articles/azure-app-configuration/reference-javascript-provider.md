---
title: JavaScript Configuration Provider
titleSuffix: Azure App Configuration
description: Learn to load configurations and feature flags from the Azure App Configuration service in JavaScript.
services: azure-app-configuration
author: zhiyuanliang-ms
ms.author: zhiyuanliang
ms.service: azure-app-configuration
ms.devlang: javascript
ms.custom: devx-track-javascript
ms.topic: tutorial
ms.date: 02/02/2025
#Customer intent: I want to learn how to use Azure App Configuration JavaScript client library.
---

# JavaScript Configuration Provider

[![configuration-provider-npm-package](https://img.shields.io/npm/v/@azure/app-configuration-provider?label=@azure/app-configuration-provider)](https://www.npmjs.com/package/@azure/app-configuration-provider)

Azure App Configuration is a managed service that helps developers centralize their application configurations simply and securely. The JavaScript configuration provider library enables loading configuration from an Azure App Configuration store in a managed way. This client library adds additional functionality above the Azure sdk for JavaScript.

## Load configuration

The `load` method exported in the `@azure/app-configuration-provider` package is used to load configuration from the Azure App Configuration. The `load` method allows you to either use Microsoft Entra ID or connection string to connect to the App Configuration store.

### [Microsoft Entra ID](#tab/entra-id)

You use the `DefaultAzureCredential` to authenticate to your App Configuration store. Follow the [instructions](./concept-enable-rbac.md#authentication-with-token-credentials) to assign your credential the **App Configuration Data Reader** role.

```javascript
const { load } = require("@azure/app-configuration-provider");
const { DefaultAzureCredential } = require("@azure/identity");
const endpoint = process.env.AZURE_APPCONFIG_ENDPOINT;
const credential = new DefaultAzureCredential(); // For more information, see https://learn.microsoft.com/azure/developer/javascript/sdk/credential-chains#use-defaultazurecredential-for-flexibility

async function run() {
    // Connect to Azure App Configuration using a token credential and load all key-values with null label.
    const settings = await load(endpoint, credential);
    console.log('settings.get("message"):', settings.get("message"));
}

run();
```

### [Connection string](#tab/connection-string)

```javascript
const { load } = require("@azure/app-configuration-provider");
const connectionString = process.env.AZURE_APPCONFIG_CONNECTION_STRING;

async function run() {
    // Connect to Azure App Configuration using a connection string and load all key-values with null label.
    const settings = await load(connectionString);
    console.log('settings.get("message"):', settings.get("message"));
}

run();
```

---

The `load` method returns an instance of `AzureAppConfiguration` type which is defined as follows:

```typescript
type AzureAppConfiguration = {
    refresh(): Promise<void>;
    onRefresh(listener: () => any, thisArg?: any): Disposable;
} & IGettable & ReadonlyMap<string, any> & IConfigurationObject;
```

For more information about `refresh` and `onRefresh` methods, see the [Dynamic refresh](#dynamic-refresh) section.

### Consume configuration

The `AzureAppConfiguration` type extends the following interfaces:

- `IGettable`

    ```typescript
    interface IGettable {
        get<T>(key: string): T | undefined;
    }
    ```

    The `IGettable` interface provides `get` method to retrieve the value of a key-value from the Map-styled data structure.

    ```typescript
    const settings = await load(endpoint, credential);
    const fontSize = settings.get("app:font:size"); // value of the key "app:font:size" from the App Configuration store
    ```

- `ReadonlyMap`

    The `AzureAppConfiguration` type also extends the [`ReadonlyMap`](https://typestrong.org/typedoc-auto-docs/typedoc/interfaces/TypeScript.ReadonlyMap.html) interface, providing read-only access to key-value pairs.

- `IConfigurationObject`

    ```typescript
    interface IConfigurationObject {
        constructConfigurationObject(options?: ConfigurationObjectConstructionOptions): Record<string, any>;
    }
    ```

    The `IConfigurationObject` interface provides `constructConfigurationObject` method to construct a configuration object based on a Map-styled data structure and hierarchical keys. The optional `ConfigurationObjectConstructionOptions` parameter can be used to specify the separator for converting hierarchical keys to object properties. By default, the separator is `"."`.

    ```typescript
    interface ConfigurationObjectConstructionOptions {
        separator?: "." | "," | ";" | "-" | "_" | "__" | "/" | ":"; // supported separators
    }
    ```

   In JavaScript, objects or maps are commonly used as the primary data structures to represent configurations. The JavaScript configuration provider library supports both of the configuration approaches, providing developers with the flexibility to choose the option that best fits their needs.

    ```typescript
    const settings = await load(endpoint, credential);
    const settingsObj = settings.constructConfigurationObject({separator: ":"});
    const fontSize1 = settings.get("app:font:size"); // map-style configuration representation
    const fontSize2 = settingsObj.app.font.size; // object-style configuration representation
    ```

### Load specific key-values using selectors

By default, the `load` method will load all configurations with null label from the configuration store. You can configure the behavior of the `load` method through the optional parameter of [`AzureAppConfigurationOptions`](https://github.com/Azure/AppConfiguration-JavaScriptProvider/blob/main/src/AzureAppConfigurationOptions.ts) type.

To refine or expand the configurations loaded from the App Configuration store, you can specify the key or label selectors under the `AzureAppConfigurationOptions.selectors` property.

```typescript
const settings = await load(endpoint, credential, {
    selectors: [
        { // load the subset of keys starting with "app1." prefix and "test" label
            keyFilter: "app1.*",
            labelFilter: "test"
        },
        { // load the subset of keys starting with "dev" label"
            labelFilter: "dev*"
        }
    ]
});
```

> [!NOTE]
> Key-values are loaded in the order the selectors are listed. If multiple selectors retrieve key-values with the same key, the value from the last one will override any previously loaded value.

### Trim prefix from keys

## Use Key Vault reference

## Dynamic refresh

### Watch sentinel key for refresh (Deprecated)

## Use feature flag

You can [create feature flags](./manage-feature-flags.md#create-a-feature-flag) in the Azure App Configuration. By default, the feature flags will not be loaded by configuration provider. You can enable loading and refreshing feature flags through `AzureAppConfigurationOptions.featureFlagOptions` property when calling the `load` method.

```typescript
const settings = await load(endpoint, credential, {
    featureFlagOptions: {
        enabled: true,
        selectors: [ { keyFilter: "*" } ],
        refresh: {
            enabled: true,
            refreshIntervalInMs: 10_000
        }
    }
});
```

> [!NOTE]
> Selectors for feature flags must be explicitly provided. Otherwise, an exception will be thrown.

### Feature management

Feature management library provides a way to develop and expose application functionality based on feature flags. The feature management libary is designed to work in conjunction with configuration provider library. It consumes the feature flags loaded from Azure App Configuration store and manage the feature flags for your application. For more information about how to use JavaScript feature management library, please go to the [quickstart](./quickstart-feature-flag-javascript.md).

## Enable failover with geo replicas

### Enable load balancing

## Next steps

To learn how to use JavaScript configuration provider, continue to the following tutorials.

> [!div class="nextstepaction"]
> [Use dynamic configuration in JavaScript](./enable-dynamic-configuration-javascript.md)