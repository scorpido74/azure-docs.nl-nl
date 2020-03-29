---
title: Azure Data Lake Storage Gen2 .NET SDK voor bestanden & ACL's
description: Gebruik de Azure Storage-clientbibliotheek om mappen en lijsten met bestands- en adresmaptoegangsbeheer (ACL) te beheren in opslagaccounts waarop hiërarchische naamruimte (HNS) is ingeschakeld.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: article
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: b83d0d2d765b60585832f1a3e7c610f05eac075c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061570"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>.NET gebruiken om mappen, bestanden en ACL's te beheren in Azure Data Lake Storage Gen2

In dit artikel ziet u hoe u .NET gebruiken voor het maken en beheren van mappen, bestanden en machtigingen in opslagaccounts waarop hiërarchische naamruimte (HNS) is ingeschakeld. 

[Package (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake) | [Samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake) | [API referentie](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake) | [Gen1 naar Gen2 mapping](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md) | [Feedback geven](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslagaccount met hiërarchische naamruimte (HNS) ingeschakeld. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.

## <a name="set-up-your-project"></a>Uw project instellen

Installeer om aan de slag te gaan het [Azure.Storage.Files.DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet-pakket.

Zie Pakketten installeren en beheren in [Visual Studio met nuGet Package Manager](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio)voor meer informatie over het installeren van NuGet-pakketten.

Voeg deze vervolgens toe met behulp van instructies aan de bovenkant van uw codebestand.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>Verbinding maken met het account

Als u de fragmenten in dit artikel wilt gebruiken, moet u een [Instantie DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) maken die het opslagaccount vertegenwoordigt. 

### <a name="connect-by-using-an-account-key"></a>Verbinding maken met een accountsleutel

Dit is de eenvoudigste manier om verbinding te maken met een account. 

In dit voorbeeld wordt een [Instantie DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) gemaakt met behulp van een accountsleutel.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Verbinding maken met Azure Active Directory (AD)

U de [Azure-identiteitsclientbibliotheek voor .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) gebruiken om uw toepassing te verifiëren met Azure AD.

In dit voorbeeld wordt een [GegevensLakeServiceClient-exemplaar](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) gemaakt met behulp van een client-id, een clientgeheim en een tenant-id.  Zie Een token [van Azure AD ophalen van Azure AD voor het toestaan van aanvragen van een clienttoepassing](../common/storage-auth-aad-app.md)om deze waarden te verkrijgen.

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
> Zie voor meer voorbeelden de [Azure-identiteitsclientbibliotheek voor .NET-documentatie..](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity)

## <a name="create-a-file-system"></a>Een bestandssysteem maken

Een bestandssysteem fungeert als een container voor uw bestanden. U er een maken door de [methode DataLakeServiceClient.CreateFileSystem](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) aan te roepen.

In dit voorbeeld wordt `my-file-system`een bestandssysteem met de naam . 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>Een map maken

Maak een adreslijstverwijzing door de [methode DataLakeFileSystemClient.CreateDirectoryAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) aan te roepen.

In dit voorbeeld `my-directory` wordt een map toegevoegd met de naam `my-subdirectory`aan een bestandssysteem en wordt een submap met de naam toegevoegd. 

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

## <a name="rename-or-move-a-directory"></a>De naam van een map wijzigen of verplaatsen

Wijzig de naam of verplaats een map door de [methode DataLakeDirectoryClient.RenameAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) aan te roepen. Geef het pad van de gewenste map een parameter. 

In dit voorbeeld wordt de naam `my-subdirectory-renamed`van een submap gewijzigd in de naam .

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

In dit voorbeeld `my-subdirectory-renamed` wordt een map met de `my-directory-2`naam verplaatst naar een submap van een map met de naam . 

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

Een map verwijderen door de methode [DataLakeDirectoryClient.Delete aan](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) te roepen.

In dit voorbeeld wordt `my-directory`een map met de naam .  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>Een map ACL beheren

Download de lijst met toegangsbeheer (ACL) van een map door de [Methode GegevensLakeDirectoryClient aan te roepen.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) en stel de ACL in door de methode [DataLakeDirectoryClient.SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) aan te roepen.

> [!NOTE]
> Als uw toepassing de toegang autoriseert met Azure Active Directory (Azure AD), controleert u of de beveiligingsprincipal die uw toepassing gebruikt om toegang te autoriseren, is toegewezen aan de [rol Opslagblob-gegevenseigenaar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)voor meer informatie over hoe ACL-machtigingen worden toegepast en de effecten van het wijzigen ervan. 

In dit voorbeeld wordt de ACL `my-directory`van een map met de naam . De `user::rwx,group::r-x,other::rw-` tekenreeks geeft de eigenaar van de gebruiker machtigingen lezen, schrijven en uitvoeren, geeft de eigenaargroep alleen lees- en uitvoermachtigingen en geeft alle anderen lees- en schrijftoestemming.

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

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Maak eerst een bestandsverwijzing in de doelmap door een instantie van de klasse [DataLakeFileClient te](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) maken. Upload een bestand door de [methode DataLakeFileClient.AppendAsync aan te](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) roepen. Zorg ervoor dat u de upload voltooit door de [methode DataLakeFileClient.FlushAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) aan te roepen.

In dit voorbeeld wordt een tekstbestand geüpload naar een map met de naam `my-directory`.    

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
> Als uw bestandsgrootte groot is, moet uw code meerdere oproepen doen naar de [DataLakeFileClient.AppendAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync). Overweeg in plaats daarvan de [methode DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) te gebruiken. Op die manier u het hele bestand in één gesprek uploaden. 
>
> Zie de volgende sectie voor een voorbeeld.

## <a name="upload-a-large-file-to-a-directory"></a>Een groot bestand uploaden naar een map

Gebruik de [methode DataLakeFileClient.UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) om grote bestanden te uploaden zonder meerdere gesprekken te hoeven voeren naar de [methode DataLakeFileClient.AppendAsync.](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)

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

## <a name="manage-a-file-acl"></a>Een bestand ACL beheren

Download de lijst met toegangsbeheer (ACL) van een bestand door de [methode DataLakeFileClient aan te roepen.GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) en stel de ACL in door de methode [DataLakeFileClient.SetAccessControlList](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) aan te roepen.

> [!NOTE]
> Als uw toepassing de toegang autoriseert met Azure Active Directory (Azure AD), controleert u of de beveiligingsprincipal die uw toepassing gebruikt om toegang te autoriseren, is toegewezen aan de [rol Opslagblob-gegevenseigenaar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)voor meer informatie over hoe ACL-machtigingen worden toegepast en de effecten van het wijzigen ervan. 

In dit voorbeeld wordt de ACL `my-file.txt`van een bestand met de naam . De `user::rwx,group::r-x,other::rw-` tekenreeks geeft de eigenaar van de gebruiker machtigingen lezen, schrijven en uitvoeren, geeft de eigenaargroep alleen lees- en uitvoermachtigingen en geeft alle anderen lees- en schrijftoestemming.

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

## <a name="download-from-a-directory"></a>Downloaden uit een map 

Maak eerst een [Instantie DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) die het bestand vertegenwoordigt dat u wilt downloaden. Gebruik de methode [DataLakeFileClient.ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) en ontlijn de retourwaarde om een [streamobject](https://docs.microsoft.com/dotnet/api/system.io.stream) te verkrijgen. Gebruik een .NET-api voor bestandsverwerking om bytes van de stream in een bestand op te slaan. 

In dit voorbeeld worden een [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) en een [FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream) gebruikt om bytes in een bestand op te slaan. 

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

Lijstinhoud door de methode [FileSystemClient.GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) aan te roepen en vervolgens de resultaten op te sommen.

In dit voorbeeld worden de namen van elk `my-directory`bestand met de naam .

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
* [Monsters](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Gen1 naar Gen2 mapping](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Feedback geven](https://github.com/Azure/azure-sdk-for-net/issues)

