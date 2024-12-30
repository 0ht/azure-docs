---
title: Store external metadata in the DICOM service in Azure Health Data Services
description: Learn how to store, retrieve and query external metadata that are not part of the DICOM files in the DICOM service
author: kabalas
ms.service: azure-health-data-services
ms.subservice: dicom-service
ms.topic: how-to
ms.date: 12/30/2024
ms.author: kabalas
---

# External metadata

The external metadata feature allows to store the metadata or additional information about the DICOM files that is not part of the DICOM file. For e.g, using the same (STOW-RS), you can store the metadata by passing the additional information in the header.

## Limiations

- External metadata is only supported in latest api version. i.e Version 2 or above
- Only private dicom tags are supported to be as external metadata.
- Only study level tags are supported.
- External metadata tags cannot be updated or deleted. 
- The following VR types are supported:

    | VR   | Description           | Single Value Matching | Range Matching | Fuzzy Matching |
    | ---- | --------------------- | --------------------- | -------------- | -------------- |
    | AE   | Application Entity    | X                     |                |                |
    | AS   | Age String            | X                     |                |                |
    | CS   | Code String           | X                     |                |                |
    | DA   | Date                  | X                     | X              |                |
    | DT   | Date Time             | X                     | X              |                |
    | FD   | Floating Point Double | X                     |                |                |
    | FL   | Floating Point Single | X                     |                |                |
    | IS   | Integer String        | X                     |                |                |
    | LO   | Long String           | X                     |                |                |
    | PN   | Person Name           | X                     |                | X              |
    | SH   | Short String          | X                     |                |                |
    | SL   | Signed Long           | X                     |                |                |
    | SS   | Signed Short          | X                     |                |                |
    | TM   | Time                  | X                     | X              |                |
    | UI   | Unique Identifier     | X                     |                |                |
    | UL   | Unsigned Long         | X                     |                |                |
    | US   | Unsigned Short        | X                     |                |                |

    > [!NOTE]
    > Sequential tags, which are tags under a tag of type Sequence of Items (SQ), are currently not supported.
    > We do not index external metadata tags if the value is null or empty.
    > There is no ability to add private creator as part of external metadata tags.

### Store (STOW-RS)

The [Store (STOW-RS)](dicom-services-conformance-statement-v2.md#store-stow-rs) transaction allows storing external metadata as customer headers.
In order to store external metadata as part of the store transaction, specify the tag key in the format `msdicom-meta-{VR}-{TagPath}`. An example request follows.

```http 
POST {dicom-service-url}/{version}/studies
Accept: multipart/related; type="application/dicom+json";
msdicom-meta-CS-001912FF: CS
msdicom-meta-PN-00191100: PersonName
msdicom-meta-DA-001900FF: 20241231

Body:
{DICOM FILE}
```

> [!NOTE]
> Only study level tags are supported.
> If the tag already exists, it will update the value of the existing tag for the study.
> If the tag already exists with different VR, then it will results in an error.
> All the tag values must follow the standard for their respective VR. If there is an issue with one of the tags, then it will fail the whole transaction.

### Retrieve metadata (for study, series, or instance)

The [Retrieve metadata (for study, series, or instance)](dicom-services-conformance-statement-v2.md#retrieve-metadata-for-study-series-or-instance) transaction allows to retrieve the external metadata along with other metadata. The external metadata is retrieved along with other tags by setting the `msdicom-request-meta` header to `true`. An example request follows. 

```http 
GET {dicom-service-url}/{version}/studies/{study}/series/{series}/instances/{instance}/metadata
Accept: multipart/related; type="application/dicom"; transfer-syntax=*
msdicom-request-meta: true
Content-Type: application/dicom
 ```

> [!NOTE]
> The `json` response will include a standard private creator tag that follows the [DICOM standard](https://dicom.nema.org/dicom/2013/output/chtml/part05/sect_7.8.html).
> The default value of the private creator tag will be `Microsoft`.
> For e.g if the private tag is `001910FF` then the private creator tag will be `00190010`.

### Search (QIDO-RS)

The [Search (QIDO-RS)](dicom-services-conformance-statement-v2.md#search-qido-rs) transaction allows to query external metadata similar to other tags. The external metadata can be queried by setting the `msdicom-request-meta` header to `true`. An example request follows. 

```http 
GET {dicom-service-url}/{version}/studies?00191100=PersonName
msdicom-request-meta: true
Content-Type: application/dicom
 ```

> [!NOTE]
> Only study level tags are supported.
> If there is an extended query tag with the same tagPath, but if the header `msdicom-request-meta` is set to `true`, then value from the external metadata is used to filter or to included as part of response data.

## Other Transaction

There are no changes to other transactions like WADO-RS, Delete, ChangeFeed and Update.

[!INCLUDE [DICOM trademark statements](../includes/healthcare-apis-dicom-trademark.md)]
