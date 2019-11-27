---
title: Azure .NET gebruiken voor bestanden & Acl's in Azure Data Lake Storage Gen2 (preview-versie)
description: Gebruik de Azure Storage-client bibliotheek voor het beheren van mappen en ACL'S (toegangs beheer lijsten) in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld.
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: fb69e0b797243a3403e8899d3f4ef23a9be9451d
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534282"
---
# <a name="use-net-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>.NET gebruiken voor bestanden & Acl's in Azure Data Lake Storage Gen2 (preview-versie)

In dit artikel leest u hoe u .NET gebruikt voor het maken en beheren van mappen, bestanden en machtigingen in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld. 

> [!IMPORTANT]
> Het NuGet-pakket van [Azure. storage. files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) dat in dit artikel wordt aanbevolen, is momenteel beschikbaar als open bare preview.

[Package (NuGet) | -](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) voor [beelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [API-verwijzing](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Storage.Files.DataLake/12.0.0-preview.6/api/index.html) | [gen1 naar Gen2-toewijzing](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [feedback geven](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.

## <a name="set-up-your-project"></a>Uw project instellen

Om aan de slag te gaan, installeert u het [Azure. storage. files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6) NuGet-pakket.

Zie [pakketten installeren en beheren in Visual Studio met behulp van de NuGet-pakket beheer](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio)voor meer informatie over het installeren van NuGet-pakketten.

Voeg deze instructies vervolgens toe aan de bovenkant van het code bestand.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Verbinding maken met het account

Als u de fragmenten in dit artikel wilt gebruiken, moet u een **DataLakeServiceClient** -exemplaar maken dat het opslag account vertegenwoordigt. De eenvoudigste manier om een account sleutel te gebruiken is. 

In dit voor beeld wordt een exemplaar van de **DataLakeServiceClient** gemaakt met behulp van een account sleutel.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

## <a name="create-a-file-system"></a>Een bestandssysteem maken

Een bestands systeem fungeert als een container voor uw bestanden. U kunt er een maken door de methode **FileSystemClient. CreateFileSystemAsync** aan te roepen.

In dit voor beeld wordt een bestands systeem gemaakt met de naam `my-file-system`. 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Een map maken

Maak een verwijzing naar een directory door de methode **FileSystemClient. CreateDirectoryAsync** aan te roepen.

In dit voor beeld wordt een map met de naam `my-directory` toegevoegd aan een bestands systeem en wordt vervolgens een submap met de naam `my-subdirectory`toegevoegd. 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Een map een andere naam geven of verplaatsen

Wijzig de naam of verplaats een map door de methode **DirectoryClient. RenameAsync** aan te roepen. Geef een para meter door aan het pad van de gewenste map. 

In dit voor beeld wordt de naam van een submap gewijzigd in `my-subdirectory-renamed`.

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

In dit voor beeld wordt een map met de naam `my-subdirectory-renamed` verplaatst naar een submap van de map met de naam `my-directory-2`. 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Een map verwijderen

Verwijder een directory door de methode **DirectoryClient. Delete** aan te roepen.

In dit voor beeld wordt een map met de naam `my-directory`verwijderd.  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Een directory-ACL beheren

Haal de toegangs beheer lijst (ACL) van een directory op door de methode **directoryClient. GetAccessControlAsync** aan te roepen en de ACL in te stellen door de methode **directoryClient. SetAccessControl** aan te roepen.

In dit voor beeld wordt de toegangs beheer lijst van een map met de naam `my-directory`opgehaald en ingesteld. De teken reeks `user::rwx,group::r-x,other::rw-` de machtigingen lezen, schrijven en uitvoeren van de gebruiker die eigenaar is, geeft de groep die eigenaar is alleen lezen-en uitvoer machtigingen en geeft alle andere Lees-en schrijf rechten.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.Acl);

    directoryClient.SetAccessControl("user::rwx,group::r-x,other::rw-");

}

```

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Maak eerst een bestands verwijzing in de doel directory door een instantie van de klasse **DataLakeFileClient** te maken. Upload een bestand door de methode **DataLakeFileClient. AppendAsync** aan te roepen. Zorg ervoor dat u de upload voltooit door de methode **DataLakeFileClient. FlushAsync** aan te roepen.

In dit voor beeld wordt een tekst bestand geüpload naar een map met de naam `my-directory`.    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

## <a name="manage-a-file-acl"></a>Een bestands-ACL beheren

Haal de toegangs beheer lijst (ACL) van een bestand op door de methode **DataLakeFileClient. GetAccessControlAsync** aan te roepen en de ACL in te stellen door de methode **FileClient. SetAccessControl** aan te roepen.

In dit voor beeld wordt de ACL van een bestand met de naam `my-file.txt`opgehaald en ingesteld. De teken reeks `user::rwx,group::r-x,other::rw-` de machtigingen lezen, schrijven en uitvoeren van de gebruiker die eigenaar is, geeft de groep die eigenaar is alleen lezen-en uitvoer machtigingen en geeft alle andere Lees-en schrijf rechten.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.Acl);

    fileClient.SetAccessControl("user::rwx,group::r-x,other::rw-");
}
```

## <a name="download-from-a-directory"></a>Downloaden uit een directory 

Maak eerst een **DataLakeFileClient** -exemplaar dat het bestand vertegenwoordigt dat u wilt downloaden. Gebruik de methode **FileClient. ReadAsync** en parser de retour waarde voor het verkrijgen van een [Stream](https://docs.microsoft.com/dotnet/api/system.io.stream) -object. Gebruik een API voor het verwerken van .NET-bestanden om bytes van de stroom naar een bestand op te slaan. 

In dit voor beeld wordt een [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) en een [FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream) gebruikt om bytes op te slaan in een bestand. 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>Mapinhoud weergeven

Mapinhoud weer geven door de methode **FileSystemClient. ListPathsAsync** aan te roepen en vervolgens de resultaten te inventariseren.

In dit voor beeld worden de namen afgedrukt van elk bestand dat zich bevindt in een map met de naam `my-directory`.

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.ListPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="see-also"></a>Zie ook

* [API-referentie documentatie](https://azuresdkdocs.blob.core.windows.net/$web/dotnet/Azure.Storage.Files.DataLake/12.0.0-preview.6/api/index.html)
* [Pakket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/12.0.0-preview.6)
* [Voorbeelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Toewijzing van gen1 naar Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Feedback geven](https://github.com/Azure/azure-sdk-for-net/issues)

