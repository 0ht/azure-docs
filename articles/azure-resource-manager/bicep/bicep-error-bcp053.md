---
title: BCP053
description: Error - The type <resource-type> does not contain property <property-name>. Available properties include <property-names>.
ms.topic: reference
ms.custom: devx-track-bicep
ms.date: 07/12/2024
---

# Bicep error code - BCP053

This error occurs when you reference a property that is not defined in the resource type.

## Error description

`The type <resource-type> does not contain property <property-name>. Available properties include <property-names>.`

## Solution

Reference the correct property name

## Examples

The following example raises the error because `Microsoft.Storage/storageAccounts` doesn't contain a property called `bar`.

```bicep
param location string 

resource storage 'Microsoft.Storage/storageAccounts@2023-04-01' = {
  name: 'myStorage'
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
}

output foo string = storage.bar 
```

You can fix the error by referencing a valid property, such as `name`:

```bicep
param location string 

resource storage 'Microsoft.Storage/storageAccounts@2023-04-01' = {
  name: 'myStorage'
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
}

output foo string = storage.name
```

## Next steps

For more information about Bicep warning and error codes, see [Bicep warnings and errors](./bicep-error-codes.md).