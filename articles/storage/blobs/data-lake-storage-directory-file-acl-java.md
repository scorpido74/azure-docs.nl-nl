---
title: Azure Data Lake Storage Gen2 Java SDK voor bestanden & Acl's (preview-versie)
description: Gebruik Azure Storage bibliotheken voor Java voor het beheren van mappen en toegangs beheer lijsten (ACL'S) voor bestands-en Directory bestanden in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld.
author: normesta
ms.service: storage
ms.date: 11/24/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 0f9489cd702eab6038689f6ac710c32427665093
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031115"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Java gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2 (preview)

In dit artikel leest u hoe u Java gebruikt om directory's, bestanden en machtigingen te maken en te beheren in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld. 

> [!IMPORTANT]
> De Java-bibliotheek die in dit artikel wordt aanbevolen, is momenteel beschikbaar als open bare preview.

[Package (Maven) | -](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) voor [beelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API-verwijzing](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.0-preview.6/index.html) | [gen1 naar Gen2-toewijzing](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [feedback geven](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.

## <a name="set-up-your-project"></a>Uw project instellen

Open [Deze pagina](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) en zoek de meest recente versie van de Java-bibliotheek om aan de slag te gaan. Open vervolgens het bestand *pom. XML* in de tekst editor. Voeg een afhankelijkheids element toe dat verwijst naar die versie.

Voeg vervolgens deze Imports-instructies toe aan het code bestand.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.RolePermissions;
```

## <a name="connect-to-the-account"></a>Verbinding maken met het account 

Als u de fragmenten in dit artikel wilt gebruiken, moet u een **DataLakeServiceClient** -exemplaar maken dat het opslag account vertegenwoordigt. De eenvoudigste manier om een account sleutel te gebruiken is. 

In dit voor beeld wordt een exemplaar van de **DataLakeServiceClient** gemaakt met behulp van een account sleutel.

```java

static public DataLakeServiceClient GetDataLakeServiceClient
(String accountName, String accountKey){

    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();

    builder.credential(sharedKeyCredential);
    builder.endpoint("https://" + accountName + ".dfs.core.windows.net");

    return builder.buildClient();
}      

```
## <a name="create-a-file-system"></a>Een bestandssysteem maken

Een bestands systeem fungeert als een container voor uw bestanden. U kunt er een maken door de methode **DataLakeServiceClient. createFileSystem** aan te roepen.

In dit voor beeld wordt een bestands systeem gemaakt met de naam `my-file-system`. 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>Een map maken

Maak een verwijzing naar een directory door de methode **DataLakeFileSystemClient. createDirectory** aan te roepen.

In dit voor beeld wordt een map met de naam `my-directory` toegevoegd aan een bestands systeem en wordt vervolgens een submap met de naam `my-subdirectory`toegevoegd. 

```java
static public DataLakeDirectoryClient CreateDirectory
(DataLakeServiceClient serviceClient, String fileSystemName){
    
    DataLakeFileSystemClient fileSystemClient =
    serviceClient.getFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.createDirectory("my-directory");

    return directoryClient.createSubDirectory("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>Een map een andere naam geven of verplaatsen

Wijzig de naam of verplaats een map door de methode **DataLakeDirectoryClient. rename** aan te roepen. Geef een para meter door aan het pad van de gewenste map. 

In dit voor beeld wordt de naam van een submap gewijzigd in `my-subdirectory-renamed`.

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename("my-directory/my-subdirectory-renamed");
}
```

In dit voor beeld wordt een map met de naam `my-subdirectory-renamed` verplaatst naar een submap van de map met de naam `my-directory-2`. 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Een map verwijderen

Verwijder een directory door de methode **DataLakeDirectoryClient. deleteWithResponse** aan te roepen.

In dit voor beeld wordt een map met de naam `my-directory`verwijderd.   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>Een directory-ACL beheren

In dit voor beeld wordt de ACL van een directory met de naam `my-directory`opgehaald en ingesteld. In dit voor beeld worden de machtigingen lezen, schrijven en uitvoeren voor de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgt alle andere Lees toegang.

> [!NOTE]
> Als uw toepassing toegang autoriseert met behulp van Azure Active Directory (Azure AD), moet u ervoor zorgen dat de beveiligings-principal die door uw toepassing wordt gebruikt om toegang te verlenen, is toegewezen aan de [rol Storage BLOB data owner](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van [toegangs beheer in azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    PathPermissions permissions = new PathPermissions()

      .group(new RolePermissions().execute(true).read(true))
      .owner(new RolePermissions().execute(true).read(true).write(true))
      .other(new RolePermissions().read(true));

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Maak eerst een bestands verwijzing in de doel directory door een instantie van de klasse **DataLakeFileClient** te maken. Upload een bestand door de methode **DataLakeFileClient. Append aan** te roepen. Zorg ervoor dat u de upload voltooit door de methode **DataLakeFileClient. FlushAsync** aan te roepen.

In dit voor beeld wordt een tekst bestand geüpload naar een map met de naam `my-directory`.

```java
static public void UploadFile(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.createFile("uploaded-file.txt");

    File file = new File("C:\\mytestfile.txt");

    InputStream targetStream = new FileInputStream(file);

    long fileSize = file.length();

    fileClient.append(targetStream, 0, fileSize);

    fileClient.flush(fileSize);
}
```

## <a name="manage-a-file-acl"></a>Een bestands-ACL beheren

In dit voor beeld wordt de ACL van een bestand met de naam `upload-file.txt`opgehaald en ingesteld. In dit voor beeld worden de machtigingen lezen, schrijven en uitvoeren voor de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgt alle andere Lees toegang.

> [!NOTE]
> Als uw toepassing toegang autoriseert met behulp van Azure Active Directory (Azure AD), moet u ervoor zorgen dat de beveiligings-principal die door uw toepassing wordt gebruikt om toegang te verlenen, is toegewezen aan de [rol Storage BLOB data owner](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van [toegangs beheer in azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```java
static public void ManageFileACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    PathAccessControl fileAccessControl =
        fileClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = fileAccessControl.getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
           
    PathPermissions permissions = new PathPermissions()

        .group(new RolePermissions().execute(true).read(true))
        .owner(new RolePermissions().execute(true).read(true).write(true))
        .other(new RolePermissions().read(false));

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>Downloaden uit een directory

Maak eerst een **DataLakeFileClient** -exemplaar dat het bestand vertegenwoordigt dat u wilt downloaden. Gebruik de methode **DataLakeFileClient. Read** om het bestand te lezen. Gebruik een API voor het verwerken van .NET-bestanden om bytes van de stroom naar een bestand op te slaan. 

```java
static public void DownloadFile(DataLakeFileSystemClient fileSystemClient)
    throws FileNotFoundException, java.io.IOException{

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.getFileClient("uploaded-file.txt");

    File file = new File("C:\\downloadedFile.txt");

    OutputStream targetStream = new FileOutputStream(file);

    fileClient.read(targetStream);

    targetStream.close();
      
}

```

## <a name="list-directory-contents"></a>Mapinhoud weergeven

In dit voor beeld worden de namen afgedrukt van elk bestand dat zich bevindt in een map met de naam `my-directory`.

```java
static public void ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient){
        
    ListPathsOptions options = new ListPathsOptions();
    options.setPath("my-directory");
     
    PagedIterable<PathItem> pagedIterable = 
    fileSystemClient.listPaths(options, null);

    java.util.Iterator<PathItem> iterator = pagedIterable.iterator();
       
    PathItem item = iterator.next();

    while (item != null)
    {
        System.out.println(item.getName());


        if (!iterator.hasNext())
        {
            break;
        }
            
        item = iterator.next();
    }

}
```

## <a name="see-also"></a>Zie ook

* [API-referentie documentatie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.0-preview.6/index.html)
* [Pakket (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake/12.0.0-preview.6/jar)
* [Voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Toewijzing van gen1 naar Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Feedback geven](https://github.com/Azure/azure-sdk-for-java/issues)