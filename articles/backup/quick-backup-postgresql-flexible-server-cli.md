---
title: "Quickstart: Configure Backup for an Azure PostgreSQL - Flexible server using Azure CLI"
description: Learn how to back up your Azure PostgreSQL - Flexible server with Azure CLI.
ms.devlang: terraform
ms.custom:
  - ignite-2024
ms.topic: quickstart
ms.date: 02/18/2025
ms.service: azure-backup
author: jyothisuri
ms.author: jsuri
---

#  Quickstart: Configure backup Azure Database for PostgreSQL - Flexible Server using Azure CLI

This quickstart describes how to configure backup for Azure Database for  PostgreSQL - Flexible Server using Azure CLI.

[Azure Backup](backup-azure-database-postgresql-flex-overview.md) allows you to back up your Azure PostgreSQL - Flexible Server using multiple clients, such as Azure portal, PowerShell, CLI, Azure Resource Manager, Bicep, and so on. 

## Prerequisites

Before you configure backup for Azure Database for PostgreSQL Flexible Server, ensure that the following prerequisites are met:

- Review the [supported scenarios and limitations for backing up Azure Database for PostgreSQL - Flexible Servers](backup-azure-database-postgresql-flex-support-matrix.md).
- [Create a Backup vault](back-up-azure-database-postgresql-flex-backup-cli.md#create-a-backup-vault) to store the recovery points for the database.
- [Configure a backup policy](back-up-azure-database-postgresql-flex-backup-cli.md#create-a-backup-policy) to schedule the backup operations.

## Configure backup

Once the vault and policy are created, there are three critical points that you need to consider to protect an Azure PostgreSQL database.

1. Backup vault has to connect to the PostgreSQL - flexible server, therefore, it requires access to this server. Access is granted to the Managed System Identity (MSI) of the Backup Vault.

See the [permissions](.\backup-azure-database-postgresql-flex-overview.md#permissions-for-backup) you should grant to the Managed System Identity (MSI) of the Backup Vault on the PostgreSQL - flexible server.

2. Once all the required permissions are set, we prepare the relevant request to configure backup for the PostgreSQL - Flexible server.
 
    ```azurecli
    az dataprotection backup-instance initialize --datasource-id $ossId --datasource-type AzureDatabaseForPostgreSQLFlexibleServer -l <vault-location> --policy-id <policy_arm_id>   > OSSBkpInstance.JSON
    ```

3. Then submit the request to protect the server with Azure Backup.

    ```azurecli
    az dataprotection backup-instance create --resource-group testBkpVaultRG --vault-name TestBkpVault TestBkpvault --backup-instance .\OSSBkpInstance.JSON
    ```

After the backup configuration is complete, you can [run an on-demand backup](back-up-azure-database-postgresql-flex-backup-cli.md#run-an-on-demand-backup) to create the first full backup for the database.

## Next steps

[Restore Azure Database for PostgreSQL - flexible server using Azure CLI](backup-azure-database-postgresql-flex-restore-cli.md)

