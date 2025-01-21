---
title: Custom data ingestion and transformation in Microsoft Sentinel
description: Learn about how Azure Monitor's custom log ingestion and data transformation features can help you get any data into Microsoft Sentinel and shape it the way you want.
author: yelevin
ms.author: yelevin
ms.topic: conceptual
ms.date: 09/25/2024

#Customer intent: As a security engineer, I want to customize data ingestion and transformation in Microsoft Sentinel so that analysts can filter, enrich, and secure log data efficiently.

---

# Custom data ingestion and transformation in Microsoft Sentinel

Azure Monitor Logs serves as the platform behind the Microsoft Sentinel workspace. All logs ingested into Microsoft Sentinel are stored in a Log Analytics workspace. From Microsoft Sentinel, you can access the stored logs and run Kusto Query Language (KQL) queries to detect threats and monitor your network activity.

Log Analytics' custom data ingestion process gives you a high level of control over the data that gets ingested. It uses [**data collection rules (DCRs)**](/azure/azure-monitor/essentials/data-collection-rule-overview) to collect your data and manipulate it even before it's stored in your workspace. This allows you to filter or enrich data being collected in standard tables and to create highly customizable tables for storing data from sources that produce unique log formats.

Microsoft Sentinel uses two tools from the underlying Azure Monitor platform to control this process:

- [**Transformations**](/azure/azure-monitor/essentials/data-collection-transformations) are defined in DCRs and apply KQL queries to incoming data before it's stored in your workspace. These transformations can filter out irrelevant data, enrich existing data with analytics or external data, or mask sensitive or personal information.

- The [**Logs ingestion API**](/azure/azure-monitor/logs/logs-ingestion-api-overview) allows you to send custom-format logs from any data source to your Log Analytics workspace, and store those logs either in certain standard tables, or in custom-formatted tables that you create. You have full control over the creation of these custom tables, down to specifying the column names and types. The API uses [**DCRs**](/azure/azure-monitor/essentials/data-collection-rule-overview) to define, configure, and apply transformations to these data flows.

These two tools will be explained in more detail below.

## Use cases and sample scenarios

### Filtering

Ingestion-time transformation provides you with the ability to filter out irrelevant data even before it's first stored in your workspace.

You can filter at the record (row) level, by specifying criteria for which records to include, or at the field (column) level, by removing the content for specific fields. Filtering out irrelevant data can:

- Help to reduce costs, as you reduce storage requirements
- Improve performance, as fewer query-time adjustments are needed

Ingestion-time data transformation supports [multiple-workspace scenarios](extend-sentinel-across-workspaces-tenants.md).

### Normalization

Ingest-time transformation also allows you to normalize logs when they're ingested into built-in or customer-normalized tables with [Advanced Security Information Model (ASIM)](normalization.md). Using ingest-time normalization improves the performance of normalized queries.

For more information, see [Ingest-time normalization](normalization-ingest-time.md).

### Enrichment and tagging

Ingestion-time transformation also lets you improve analytics by enriching your data with extra columns added to the configured KQL transformation. Extra columns might include parsed or calculated data from existing columns, or data taken from data structures created on-the-fly.

For example, you could add extra information such as external HR data, an expanded event description, or classifications that depend on the user, location, or activity type.

### Masking

Ingestion-time transformations can also be used to mask or remove personal information. For example, you might use data transformation to mask all but the last digits of a social security number or credit card number, or you could replace other types of personal data with nonsense, standard text, or dummy data. Mask your personal information at ingestion time to increase security across your network.

## Data ingestion flow in Microsoft Sentinel

The following image shows where ingestion-time data transformation enters the data ingestion flow in Microsoft Sentinel.

Microsoft Sentinel collects data into the Log Analytics workspace from multiple sources. 
- Data from built-in data connectors is processed in Log Analytics using some combination of hardcoded workflows and ingestion-time transformations in the workspace DCR. This data can be stored in standard tables or in a specific set of custom tables.
- Data ingested directly into the Logs ingestion API endpoint is processed by a standard DCR that may include an ingestion-time transformation. This data can then be stored in either standard or custom tables of any kind.

:::image type="content" source="media/data-transformation/data-transformation-architecture.png" alt-text="Diagram of the Microsoft Sentinel data transformation architecture." border="false":::

## DCR support in Microsoft Sentinel

In Log Analytics, data collection rules (DCRs) determine the data flow for different input streams. A data flow includes: the data stream to be transformed (standard or custom), the destination workspace, the KQL transformation, and the output table. For standard input streams, the output table is the same as the input stream.

Support for DCRs in Microsoft Sentinel includes:

- *Standard DCRs*, currently supported only for AMA-based connectors and workflows using the [Logs ingestion API](/azure/azure-monitor/logs/logs-ingestion-api-overview).

    Each connector or log source workflow can have its own dedicated *standard DCR*, though multiple connectors or sources can share a common *standard DCR* as well.

- *Workspace transformation DCRs*, for workflows that don't currently support standard DCRs.

    A single *workspace transformation DCR* serves all the supported workflows in a workspace that aren't served by standard DCRs. A workspace can have only one *workspace transformation DCR*, but that DCR contains separate transformations for each input stream. Also, *workspace transformation DCR*s are supported only for a [specific set of tables](/azure/azure-monitor/logs/tables-feature-support).

Microsoft Sentinel's support for ingestion-time transformation depends on the type of data connector you're using. For more in-depth information on custom logs, ingestion-time transformation, and data collection rules, see the articles linked in the [Related content](#related-content) section at the end of this article.

### DCR support for Microsoft Sentinel data connectors

The following table describes DCR support for Microsoft Sentinel data connector types:

| Data connector type | DCR support |
| ------------------- | ----------- |
| **Direct ingestion via [Logs ingestion API](/azure/azure-monitor/logs/logs-ingestion-api-overview)** | Standard DCRs |
| [**AMA standard logs**](connect-services-windows-based.md), such as: <li>[Windows Security Events via AMA](./data-connectors/windows-security-events-via-ama.md)<li>[Windows Forwarded Events](./data-connectors/windows-forwarded-events.md)<li>[CEF data](connect-cef-ama.md)<li>[Syslog data](connect-cef-syslog.md) | Standard DCRs |
| [**Diagnostic settings-based connections**](connect-services-diagnostic-setting-based.md) | Workspace transformation DCRs, based on the [supported output tables](/azure/azure-monitor/logs/tables-feature-support) for specific data connectors |
| **Built-in, service-to-service data connectors**, such as:<li>[Microsoft Office 365](connect-services-api-based.md)<li>[Microsoft Entra ID](connect-azure-active-directory.md)<li>[Amazon S3](connect-aws.md) | Workspace transformation DCRs, based on the [supported output tables](/azure/azure-monitor/logs/tables-feature-support) for specific data connectors |
| **Built-in, API-based data connector**, such as: <li>[Codeless data connectors](create-codeless-connector.md) | Standard DCRs |
| **Built-in, API-based data connectors**, such as: <li>[Legacy codeless data connectors](create-codeless-connector-legacy.md)<li>[Azure Functions-based data connectors](connect-azure-functions-template.md) | Not currently supported |



## Limitations and considerations

- Transformations in Microsoft Sentinel have the same limitations as Azure Monitor. See [Limitations and considerations](/azure/azure-monitor/essentials/data-collection-transformations-create#limitations-and-considerations) for details.
- Log Analytic workspaces enabled for Microsoft Sentinel aren't subject to the [filtering ingestion charge](/azure/azure-monitor/essentials/data-collection-transformations#cost-for-transformations), regardless of how much data the transformation filters. 


## Related content

For more information, see:

- [Transform or customize data at ingestion time in Microsoft Sentinel (preview)](configure-data-transformation.md)
- [Microsoft Sentinel data connectors](connect-data-sources.md)
- [Find your Microsoft Sentinel data connector](data-connectors-reference.md)

For more in-depth information on ingestion-time transformation, the Custom Logs API, and data collection rules, see the following articles in the Azure Monitor documentation:

- [Data collection transformations in Azure Monitor Logs](/azure/azure-monitor/essentials/data-collection-transformations)
- [Logs ingestion API in Azure Monitor Logs](/azure/azure-monitor/logs/logs-ingestion-api-overview)
- [Data collection rules in Azure Monitor](/azure/azure-monitor/essentials/data-collection-rule-overview)
