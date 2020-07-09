---
title: Azure Data Lake Storage Gen2 .NET SDK voor bestanden & Acl's
description: Gebruik de Azure Storage-client bibliotheek voor het beheren van mappen en ACL'S (toegangs beheer lijsten) in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 1bf21b8714554dcdc52ab6e34041c738ec2653f6
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105061"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>.NET gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2

In dit artikel leest u hoe u .NET gebruikt voor het maken en beheren van mappen, bestanden en machtigingen in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld. 

[Pakket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  Voor [beelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  [API-verwijzing](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)  |  Toewijzing van gen1 [naar Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Feedback geven](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.

## <a name="set-up-your-project"></a>Uw project instellen

Om aan de slag te gaan, installeert u het [Azure. storage. files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet-pakket.

Zie [pakketten installeren en beheren in Visual Studio met behulp van de NuGet-pakket beheer](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio)voor meer informatie over het installeren van NuGet-pakketten.

Voeg deze instructies vervolgens toe aan de bovenkant van het code bestand.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Verbinding maken met het account

Als u de fragmenten in dit artikel wilt gebruiken, moet u een [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -exemplaar maken dat het opslag account vertegenwoordigt. 

### <a name="connect-by-using-an-account-key"></a>Verbinding maken met behulp van een account sleutel

Dit is de eenvoudigste manier om verbinding te maken met een account. 

In dit voor beeld wordt een [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) -exemplaar gemaakt met behulp van een account sleutel.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Verbinding maken met behulp van Azure Active Directory (AD)

U kunt de [Azure Identity client-bibliotheek voor .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) gebruiken om uw toepassing te verifiëren met Azure AD.

In dit voor beeld wordt een [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) -exemplaar gemaakt met behulp van een client-id, een client geheim en een Tenant-id.  Zie [een Token ophalen uit Azure AD voor het machtigen van aanvragen van een client toepassing](../common/storage-auth-aad-app.md)om deze waarden op te halen.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

```

> [!NOTE]
> Zie de [Azure Identity client-bibliotheek voor .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) -documentatie voor meer voor beelden.

## <a name="create-a-file-system"></a>Een bestandssysteem maken

Een bestands systeem fungeert als een container voor uw bestanden. U kunt er een maken door de methode [DataLakeServiceClient. CreateFileSystem](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) aan te roepen.

In dit voor beeld wordt een bestands systeem gemaakt met de naam `my-file-system` . 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Een map maken

Maak een verwijzing naar een directory door de methode [DataLakeFileSystemClient. CreateDirectoryAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) aan te roepen.

In dit voor beeld wordt een map met de naam van `my-directory` een bestands systeem toegevoegd en wordt vervolgens een submap met de naam toegevoegd `my-subdirectory` . 

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

Wijzig de naam of verplaats een map door de methode [DataLakeDirectoryClient. RenameAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) aan te roepen. Geef een para meter door aan het pad van de gewenste map. 

In dit voor beeld wordt de naam van een submap gewijzigd in de naam `my-subdirectory-renamed` .

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

In dit voor beeld wordt een map verplaatst met de naam `my-subdirectory-renamed` naar een submap van een map met de naam `my-directory-2` . 

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

Verwijder een directory door de methode [DataLakeDirectoryClient. Delete](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) aan te roepen.

In dit voor beeld wordt een map met de naam verwijderd `my-directory` .  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Een directory-ACL beheren

Haal de toegangs beheer lijst (ACL) van een directory op door de methode [DataLakeDirectoryClient. GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) aan te roepen en de ACL in te stellen door de methode [DataLakeDirectoryClient. SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) aan te roepen.

> [!NOTE]
> Als uw toepassing toegang autoriseert met behulp van Azure Active Directory (Azure AD), moet u ervoor zorgen dat de beveiligings-principal die door uw toepassing wordt gebruikt om toegang te verlenen, is toegewezen aan de [rol Storage BLOB data owner](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van [toegangs beheer in azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

In dit voor beeld wordt de ACL van een directory met de naam opgehaald en ingesteld `my-directory` . De teken reeks `user::rwx,group::r-x,other::rw-` geeft de machtigingen lezen, schrijven en uitvoeren van de gebruiker, geeft de groep die eigenaar is alleen lees-en uitvoer machtigingen en geeft alle andere Lees-en schrijf rechten.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    Console.WriteLine(directoryAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

U kunt ook de ACL ophalen en instellen van de hoofdmap van een bestands systeem. Als u de hoofdmap wilt ophalen, geeft u een lege teken reeks () door aan `""` de methode [DataLakeFileSystemClient. GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) .

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Maak eerst een bestands verwijzing in de doel directory door een instantie van de klasse [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) te maken. Upload een bestand door de methode [DataLakeFileClient. AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) aan te roepen. Zorg ervoor dat u de upload voltooit door de methode [DataLakeFileClient. FlushAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) aan te roepen.

In dit voor beeld wordt een tekst bestand geüpload naar een map met de naam `my-directory` .    

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

> [!TIP]
> Als de bestands grootte groot is, moet uw code meerdere aanroepen naar de [DataLakeFileClient. AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). U kunt in plaats daarvan de methode [DataLakeFileClient. UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) gebruiken. Op die manier kunt u het volledige bestand in één aanroep uploaden. 
>
> Zie de volgende sectie voor een voor beeld.

## <a name="upload-a-large-file-to-a-directory"></a>Een groot bestand uploaden naar een map

Gebruik de methode [DataLakeFileClient. UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) om grote bestanden te uploaden zonder meerdere aanroepen naar de methode [DataLakeFileClient. AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) te hoeven maken.

```cs
public async Task UploadFileBulk(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.GetFileClient("uploaded-file.txt");

    FileStream fileStream =
        File.OpenRead("C:\\file-to-upload.txt");

    await fileClient.UploadAsync(fileStream);

}

```

## <a name="manage-a-file-acl"></a>Een bestands-ACL beheren

Haal de toegangs beheer lijst (ACL) van een bestand op door de methode [DataLakeFileClient. GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) aan te roepen en de ACL in te stellen door de methode [DataLakeFileClient. SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) aan te roepen.

> [!NOTE]
> Als uw toepassing toegang autoriseert met behulp van Azure Active Directory (Azure AD), moet u ervoor zorgen dat de beveiligings-principal die door uw toepassing wordt gebruikt om toegang te verlenen, is toegewezen aan de [rol Storage BLOB data owner](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van [toegangs beheer in azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control). 

In dit voor beeld wordt de ACL van een bestand met de naam opgehaald en ingesteld `my-file.txt` . De teken reeks `user::rwx,group::r-x,other::rw-` geeft de machtigingen lezen, schrijven en uitvoeren van de gebruiker, geeft de groep die eigenaar is alleen lees-en uitvoer machtigingen en geeft alle andere Lees-en schrijf rechten.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    Console.WriteLine(FileAccessControl.AccessControlList);

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

## <a name="download-from-a-directory"></a>Downloaden uit een directory 

Maak eerst een [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) -exemplaar dat het bestand vertegenwoordigt dat u wilt downloaden. Gebruik de methode [DataLakeFileClient. ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) en parser de retour waarde voor het verkrijgen van een [Stream](https://docs.microsoft.com/dotnet/api/system.io.stream) -object. Gebruik een API voor het verwerken van .NET-bestanden om bytes van de stroom naar een bestand op te slaan. 

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

Mapinhoud weer geven door de methode [FileSystemClient. GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) aan te roepen en vervolgens de resultaten te inventariseren.

In dit voor beeld worden de namen van elk bestand dat zich in een map met de naam bevindt `my-directory` .

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.GetPathsAsync("my-directory").GetAsyncEnumerator();

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

* [API-referentiedocumentatie](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [Pakket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [Voorbeelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Toewijzing van gen1 naar Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Feedback geven](https://github.com/Azure/azure-sdk-for-net/issues)

