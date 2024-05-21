---
title: "Uploading Files to Sharepoint using Microsoft Graph API - C#"
date: 2024-05-20
---
In this blog post, we will explore how to upload files to a SharePoint folder using the Microsoft Graph API. If you haven't already, make sure to refer to my [previous article on authentication to set up your authentication with Microsoft Graph](https://mitchellhein25.github.io/CSharp-and-React-Insights/2024/05/20/Microsoft-Graph-API-Access-Token-Client-Secret.html).

Once you have your authentication in place, follow the steps below to upload files to SharePoint.

## Step 1: Get the Drive ID
The first step in uploading a file is to get the Drive ID associated with your SharePoint site and list.

``` csharp
private async Task<string> GetDriveId(string siteId, string listId)
{
    Drive listDrive = await _graphClient.Sites[siteId]
                                        .Lists[listId]
                                        .Drive
                                        .GetAsync();
    if (listDrive == null)
    {
        _logger($"Unable to get drive with siteId: {siteId} and listId: {listId}", EventLogEntryType.Error);
        return string.Empty;
    }
    return listDrive.Id;
}
```
## Step 2: Get or Create the Folder by Name
Next, you need to get the folder by name or create it if it doesn't exist.

``` csharp
private async Task<DriveItem> GetFolderByFolderName(string driveId, string folderName)
{
    DriveItemCollectionResponse folderSearch = null;
    folderSearch = await _graphClient.Drives[driveId]
                                     .Items
                                     .GetAsync(requestConfig => requestConfig.QueryParameters.Filter = $"Name eq '{folderName}'");

    bool folderIsNull = folderSearch == null || folderSearch.Value == null || folderSearch.Value.Count == 0;
    if (!folderIsNull)
        folder = folderSearch.Value[0];

    if (folderIsNull)
    {
        DriveItem newFolderDriveItemPostRequest = new DriveItem
        {
            Name = folderName,
            Folder = new Folder { },
            AdditionalData = new Dictionary<string, object>
            {
                {
                    "@microsoft.graph.conflictBehavior" , "fail"
                },
            },
        };
        DriveItem newFolderDriveItem = null;
        newFolderDriveItem = await _graphClient.Drives[driveId]
                                               .Items
                                               .PostAsync(newFolderDriveItemPostRequest);
        if (newFolderDriveItem != null)
        {
            folder = newFolderDriveItem;
            _logger($"Successful folder creation. folderName: {folderName}, folderId: {newFolderDriveItem.Id}", EventLogEntryType.Information);
        }
    }

    return folder;
}
```
## Step 3: Upload the File to the Folder
Finally, upload your file to the specified folder. This method handles both small and large files using an upload session.
``` csharp
private async Task UploadFileToFolder(string filePath, string driveId, string folderId)
{
    using (Stream fileStream = File.OpenRead(filePath))
    {
        CreateUploadSessionPostRequestBody uploadSessionRequestBody = new CreateUploadSessionPostRequestBody
        {
            Item = new DriveItemUploadableProperties
            {
                AdditionalData = new Dictionary<string, object>
                {
                    { "@microsoft.graph.conflictBehavior", "rename" }, // fail, replace, or rename
                },
            },
        };
        UploadSession uploadSession = await _graphClient.Drives[driveId]
                                                        .Items[folderId]
                                                        .ItemWithPath(Path.GetFileName(filePath))
                                                        .CreateUploadSession
                                                        .PostAsync(uploadSessionRequestBody);

        int maxSliceSize = 320 * 1024;
        LargeFileUploadTask<DriveItem> fileUploadTask = new LargeFileUploadTask<DriveItem>(uploadSession, fileStream, maxSliceSize, _graphClient.RequestAdapter);

        long totalLength = fileStream.Length;
        IProgress<long> progress = new Progress<long>(prog => Console.WriteLine($"Uploaded {prog} bytes of {totalLength} bytes"));

        try
        {
            UploadResult<DriveItem> uploadResult = await fileUploadTask.UploadAsync(progress);
            _logger($"Successful file upload. filePath: {filePath}. folderId: {folderId}", EventLogEntryType.Information);
        }
        catch (ODataError ex)
        {
            _logger($"Error uploading. filePath: {filePath}. folderId: {folderId}:\n{ex.Error?.Message}", EventLogEntryType.Error);
        }
    }
}
```

By following these steps, you can upload files to a SharePoint folder using the Microsoft Graph API. This method ensures that your files are uploaded efficiently, regardless of their size.
