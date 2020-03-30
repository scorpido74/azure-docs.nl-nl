---
title: Azure Data Lake Storage Gen2 Java SDK voor bestanden & ACL's
description: Gebruik Azure Storage-bibliotheken voor Java om mappen en lijsten met bestands- en adresmaptoegangsbeheer (ACL) te beheren in opslagaccounts waarvoor hiërarchische naamruimte (HNS) is ingeschakeld.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 45870dd7d3035b6b49340fd6e8016794088e775a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061561"
---
# <a name="use-java-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Java gebruiken om mappen, bestanden en ACL's te beheren in Azure Data Lake Storage Gen2

In dit artikel ziet u hoe u Java gebruiken om mappen, bestanden en machtigingen te maken en te beheren in opslagaccounts waarop hiërarchische naamruimte (HNS) is ingeschakeld. 

[Pakket (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) | [Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake) | [API referentie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html) | [Gen1 naar Gen2 mapping](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md) | [Feedback geven](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslagaccount met hiërarchische naamruimte (HNS) ingeschakeld. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.

## <a name="set-up-your-project"></a>Uw project instellen

Open [deze pagina](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) om aan de slag te gaan en de nieuwste versie van de Java-bibliotheek te vinden. Open vervolgens het *pom.xml-bestand* in uw teksteditor. Voeg een afhankelijkheidselement toe dat naar die versie verwijst.

Als u van plan bent uw clienttoepassing te verifiëren met Behulp van Azure Active Directory (AD), voegt u een afhankelijkheid toe aan de Azure Secret Client Library. Zie [Het pakket Geheime clientbibliotheek toevoegen aan uw project](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Voeg vervolgens deze importinstructies toe aan uw codebestand.

```java
import com.azure.core.credential.TokenCredential;
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

Als u de fragmenten in dit artikel wilt gebruiken, moet u een **Instantie DataLakeServiceClient** maken die het opslagaccount vertegenwoordigt. 

### <a name="connect-by-using-an-account-key"></a>Verbinding maken met een accountsleutel

Dit is de eenvoudigste manier om verbinding te maken met een account. 

In dit voorbeeld wordt een **Instantie DataLakeServiceClient** gemaakt met behulp van een accountsleutel.

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

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Verbinding maken met Azure Active Directory (Azure AD)

U de [Azure-identiteitsclientbibliotheek voor Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) gebruiken om uw toepassing te verifiëren met Azure AD.

In dit voorbeeld wordt een **GegevensLakeServiceClient-exemplaar** gemaakt met behulp van een client-id, een clientgeheim en een tenant-id.  Zie Een token [van Azure AD ophalen van Azure AD voor het toestaan van aanvragen van een clienttoepassing](../common/storage-auth-aad-app.md)om deze waarden te verkrijgen.

```java
static public DataLakeServiceClient GetDataLakeServiceClient
    (String accountName, String clientId, String ClientSecret, String tenantID){

    String endpoint = "https://" + accountName + ".dfs.core.windows.net";
        
    ClientSecretCredential clientSecretCredential = new ClientSecretCredentialBuilder()
    .clientId(clientId)
    .clientSecret(ClientSecret)
    .tenantId(tenantID)
    .build();
           
    DataLakeServiceClientBuilder builder = new DataLakeServiceClientBuilder();
    return builder.credential(clientSecretCredential).endpoint(endpoint).buildClient();
 } 
```

> [!NOTE]
> Zie voor meer voorbeelden de [Azure-identiteitsclientbibliotheek voor Java-documentatie.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)


## <a name="create-a-file-system"></a>Een bestandssysteem maken

Een bestandssysteem fungeert als een container voor uw bestanden. U er een maken door de **methode DataLakeServiceClient.createFileSystem aan** te roepen.

In dit voorbeeld wordt `my-file-system`een bestandssysteem met de naam . 

```java
static public DataLakeFileSystemClient CreateFileSystem
(DataLakeServiceClient serviceClient){

    return serviceClient.createFileSystem("my-file-system");
}
```

## <a name="create-a-directory"></a>Een map maken

Maak een adreslijstverwijzing door de methode **DataLakeFileSystemClient.createDirectory aan** te roepen.

In dit voorbeeld `my-directory` wordt een map toegevoegd met de naam `my-subdirectory`aan een bestandssysteem en wordt een submap met de naam toegevoegd. 

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

## <a name="rename-or-move-a-directory"></a>De naam van een map wijzigen of verplaatsen

Wijzig de naam of verplaats een map door de **methode DataLakeDirectoryClient.rename** aan te roepen. Geef het pad van de gewenste map een parameter. 

In dit voorbeeld wordt de naam `my-subdirectory-renamed`van een submap gewijzigd in de naam .

```java
static public DataLakeDirectoryClient
    RenameDirectory(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-subdirectory-renamed");
}
```

In dit voorbeeld `my-subdirectory-renamed` wordt een map met de `my-directory-2`naam verplaatst naar een submap van een map met de naam . 

```java
static public DataLakeDirectoryClient MoveDirectory
(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory/my-subdirectory-renamed");

    return directoryClient.rename(
        fileSystemClient.getFileSystemName(),"my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>Een map verwijderen

Een map verwijderen door de methode **DataLakeDirectoryClient.deleteWithte aan** te roepen.

In dit voorbeeld wordt `my-directory`een map met de naam .   

```java
static public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient){
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    directoryClient.deleteWithResponse(true, null, null, null);
}
```

## <a name="manage-a-directory-acl"></a>Een map ACL beheren

In dit voorbeeld wordt de ACL `my-directory`van een map met de naam . In dit voorbeeld leest, schrijft en voert u machtigingen uit, geeft de eigenaarsgroep alleen lees- en uitvoermachtigingen en geeft alle anderen leestoegang.

> [!NOTE]
> Als uw toepassing de toegang autoriseert met Azure Active Directory (Azure AD), controleert u of de beveiligingsprincipal die uw toepassing gebruikt om toegang te autoriseren, is toegewezen aan de [rol Opslagblob-gegevenseigenaar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)voor meer informatie over hoe ACL-machtigingen worden toegepast en de effecten van het wijzigen ervan.

```java
static public void ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient){

    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        directoryClient.getAccessControl();

    List<PathAccessControlEntry> pathPermissions = directoryAccessControl.getAccessControlList();
       
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));
             
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);
  
    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);
  
    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);
  
    PathPermissions permissions = new PathPermissions();
  
    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    directoryClient.setPermissions(permissions, null, null);

    pathPermissions = directoryClient.getAccessControl().getAccessControlList();
     
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}

```

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Maak eerst een bestandsverwijzing in de doelmap door een instantie van de klasse **DataLakeFileClient te** maken. Upload een bestand door de methode **DataLakeFileClient.append aan te** roepen. Zorg ervoor dat u de upload voltooit door de **methode DataLakeFileClient.FlushAsync** aan te roepen.

In dit voorbeeld wordt een tekstbestand geüpload naar een map met de naam `my-directory`.'

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

> [!TIP]
> Als uw bestandsgrootte groot is, moet uw code meerdere oproepen doen naar de methode **DataLakeFileClient.append.** Overweeg in plaats daarvan de methode **DataLakeFileClient.uploadFromFile** te gebruiken. Op die manier u het hele bestand in één gesprek uploaden. 
>
> Zie de volgende sectie voor een voorbeeld.

## <a name="upload-a-large-file-to-a-directory"></a>Een groot bestand uploaden naar een map

Gebruik de **methode DataLakeFileClient.uploadFromFile** om grote bestanden te uploaden zonder meerdere oproepen te hoeven voeren naar de **methode DataLakeFileClient.append.**

```java
static public void UploadFileBulk(DataLakeFileSystemClient fileSystemClient) 
    throws FileNotFoundException{
        
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.getDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.getFileClient("uploaded-file.txt");

    fileClient.uploadFromFile("C:\\mytestfile.txt");

    }

```


## <a name="manage-a-file-acl"></a>Een bestand ACL beheren

In dit voorbeeld wordt de ACL `upload-file.txt`van een bestand met de naam . In dit voorbeeld leest, schrijft en voert u machtigingen uit, geeft de eigenaarsgroep alleen lees- en uitvoermachtigingen en geeft alle anderen leestoegang.

> [!NOTE]
> Als uw toepassing de toegang autoriseert met Azure Active Directory (Azure AD), controleert u of de beveiligingsprincipal die uw toepassing gebruikt om toegang te autoriseren, is toegewezen aan de [rol Opslagblob-gegevenseigenaar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)voor meer informatie over hoe ACL-machtigingen worden toegepast en de effecten van het wijzigen ervan.

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
           
    RolePermissions groupPermission = new RolePermissions();
    groupPermission.setExecutePermission(true).setReadPermission(true);

    RolePermissions ownerPermission = new RolePermissions();
    ownerPermission.setExecutePermission(true).setReadPermission(true).setWritePermission(true);

    RolePermissions otherPermission = new RolePermissions();
    otherPermission.setReadPermission(true);

    PathPermissions permissions = new PathPermissions();

    permissions.setGroup(groupPermission);
    permissions.setOwner(ownerPermission);
    permissions.setOther(otherPermission);

    fileClient.setPermissions(permissions, null, null);

    pathPermissions = fileClient.getAccessControl().getAccessControlList();
   
    System.out.println(PathAccessControlEntry.serializeList(pathPermissions));

}
```

## <a name="download-from-a-directory"></a>Downloaden uit een map

Maak eerst een **Instantie DataLakeFileClient** die het bestand vertegenwoordigt dat u wilt downloaden. Gebruik de **methode DataLakeFileClient.read** om het bestand te lezen. Gebruik een .NET-api voor bestandsverwerking om bytes van de stream in een bestand op te slaan. 

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

In dit voorbeeld worden de namen van elk `my-directory`bestand met de naam .

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

* [API-referentiedocumentatie](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.0.1/index.html)
* [Pakket (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
* [Monsters](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
* [Gen1 naar Gen2 mapping](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Feedback geven](https://github.com/Azure/azure-sdk-for-java/issues)