---
title: Performance tuning for uploads and downloads with Azure Storage client library for Go
titleSuffix: Azure Storage
description: Learn how to tune your uploads and downloads for better performance with Azure Storage client library for Go. 
services: storage
author: pauljewellmsft
ms.author: pauljewell
ms.service: azure-blob-storage
ms.topic: how-to
ms.date: 09/10/2024
ms.devlang: golang
ms.custom: devx-track-go, devguide-go, devx-track-go
---

# Performance tuning for uploads and downloads with Go

When an application transfers data using the Azure Storage client library for Go, there are several factors that can affect speed, memory usage, and even the success or failure of the request. To maximize performance and reliability for data transfers, it's important to be proactive in configuring client library transfer options based on the environment your app runs in.

This article walks through several considerations for tuning data transfer options. When properly tuned, the client library can efficiently distribute data across multiple requests, which can result in improved operation speed, memory usage, and network stability.

## Performance tuning for uploads

Properly tuning data transfer options is key to reliable performance for uploads. Storage transfers are partitioned into several subtransfers based on the values of these arguments. The maximum supported transfer size varies by operation and service version, so be sure to check the documentation to determine the limits. For more information on transfer size limits for Blob storage, see [Scale targets for Blob storage](scalability-targets.md#scale-targets-for-blob-storage).

### Set transfer options for uploads

If the total blob size is less than or equal to 256 MiB, the data is uploaded with a single `Put Blob` request. If the blob size is greater than 256 MiB, or if the blob size is unknown, the blob is uploaded in chunks using a series of [Put Block](/rest/api/storageservices/put-block) calls followed by [Put Block List](/rest/api/storageservices/put-block-list).

The following arguments can be tuned based on the needs of your app:

- `BlockSize`: The maximum length of a transfer in bytes when uploading a block blob in chunks. Defaults to 1 MiB. Minimum value is 1 MiB.
- `Concurrency`: The maximum number of subtransfers that may be used in parallel. Defaults to 1.

These options are available when uploading using the following methods: `UploadBuffer`, `UploadStream`, and `UploadFile`. The `Upload` method doesn't support these options, and uploads data in a single request.

> [!NOTE]
> The client libraries use defaults for each data transfer option, if not provided. These defaults are typically performant in a data center environment, but not likely to be suitable for home consumer environments. Poorly tuned data transfer options can result in excessively long operations and even request timeouts. It's best to be proactive in testing these values, and tuning them based on the needs of your application and environment.

#### BlockSize

The `BlockSize` argument is the maximum length of a transfer in bytes when uploading a block blob in chunks.

To keep data moving efficiently, the client libraries may not always reach the `max_block_size` value for every transfer. Depending on the operation, the maximum supported value for transfer size can vary. For more information on transfer size limits for Blob storage, see the chart in [Scale targets for Blob storage](scalability-targets.md#scale-targets-for-blob-storage).

#### Code example

The following code example shows how to specify data transfer options when creating a `BlobClient` object, and how to upload data using that client object. The values provided in this sample aren't intended to be a recommendation. To properly tune these values, you need to consider the specific needs of your app.

```go
func uploadBlobWithTransferOptions(client *azblob.Client, containerName string, blobName string) {
    // Open the file for reading
    file, err := os.OpenFile("path/to/sample/file", os.O_RDONLY, 0)
    handleError(err)

    defer file.Close()

    // Upload the data to a block blob with transfer options
    _, err = client.UploadFile(context.TODO(), containerName, blobName, file,
        &azblob.UploadFileOptions{
            BlockSize:   int64(4 * 1024 * 1024), // 4 MiB
            Concurrency: uint16(2),
        })
    handleError(err)
}
```

In this example, we set the number of parallel transfer workers to 2, using the `max_concurrency` argument on the method call. This configuration opens up to two connections simultaneously, allowing the upload to happen in parallel. During client instantiation, we set the `max_single_put_size` argument to 8 MiB. If the blob size is smaller than 8 MiB, only a single request is necessary to complete the upload operation. If the blob size is larger than 8 MiB, the blob is uploaded in chunks with a maximum chunk size of 4 MiB, as set by the `max_block_size` argument.

### Performance considerations for uploads

During an upload, the Storage client libraries split a given upload stream into multiple subuploads based on the configuration options defined during client construction. Each subupload has its own dedicated call to the REST operation. For a `BlobClient` object, this operation is [Put Block](/rest/api/storageservices/put-block). The Storage client library manages these REST operations in parallel (depending on transfer options) to complete the full upload.

You can learn how the client library handles buffering in the following sections.

> [!NOTE]
> Block blobs have a maximum block count of 50,000 blocks. The maximum size of your block blob, then, is 50,000 times `max_block_size`.

#### Buffering during uploads

The Storage REST layer doesn’t support picking up a REST upload operation where you left off; individual transfers are either completed or lost. To ensure resiliency for stream uploads, the Storage client libraries buffer data for each individual REST call before starting the upload. In addition to network speed limitations, this buffering behavior is a reason to consider a smaller value for `BlockSize`, even when uploading in sequence. Decreasing the value of `BlockSize` decreases the maximum amount of data that is buffered on each request and each retry of a failed request. If you're experiencing frequent timeouts during data transfers of a certain size, reducing the value of `BlockSize` reduces the buffering time, and may result in better performance.

## Performance tuning for downloads

Properly tuning data transfer options is key to reliable performance for downloads. Storage transfers are partitioned into several subtransfers based on the values of these arguments.

### Set transfer options for downloads

The following arguments can be tuned based on the needs of your app:

- `BlockSize`: The maximum chunk size used for downloading a blob. Defaults to 4 MB.
- `Concurrency`: The maximum number of subtransfers that may be used in parallel. Defaults to 5.

These options are available when downloading using the following methods: `DownloadBuffer` and `DownloadFile`. The `DownloadStream` method doesn't support these options, and downloads data in a single request.

#### Code example

```go
def download_blob_transfer_options(self, account_url: str, container_name: str, blob_name: str):
    # Create a BlobClient object with data transfer options for download
    blob_client = BlobClient(
        account_url=account_url, 
        container_name=container_name, 
        blob_name=blob_name,
        credential=DefaultAzureCredential(),
        max_single_get_size=1024*1024*32, # 32 MiB
        max_chunk_get_size=1024*1024*4 # 4 MiB
    )

    with open(file=os.path.join(r'file_path', 'file_name'), mode="wb") as sample_blob:
        download_stream = blob_client.download_blob(max_concurrency=2)
        sample_blob.write(download_stream.readall())
```

### Performance considerations for downloads

During a download, the Storage client libraries split a given download request into multiple subdownloads based on the configuration options defined during client construction. Each subdownload has its own dedicated call to the REST operation. Depending on transfer options, the client libraries manage these REST operations in parallel to complete the full download.

## Related content

- This article is part of the Blob Storage developer guide for Go. See the full list of developer guide articles at [Build your app](storage-blob-go-get-started.md#build-your-app).
- To understand more about factors that can influence performance for Azure Storage operations, see [Latency in Blob storage](storage-blobs-latency.md).
- To see a list of design considerations to optimize performance for apps using Blob storage, see [Performance and scalability checklist for Blob storage](storage-performance-checklist.md).
