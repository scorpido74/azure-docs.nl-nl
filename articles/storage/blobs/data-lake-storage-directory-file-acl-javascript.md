---
title: Java script gebruiken voor bestanden & Acl's in Azure Data Lake Storage Gen2 (preview-versie)
description: Gebruik Azure Storage Data Lake-client bibliotheek voor Java script om directory's en ACL'S (toegangs beheer lijsten) in opslag accounts te beheren waarvoor een hiërarchische naam ruimte (HNS) is ingeschakeld.
author: normesta
ms.service: storage
ms.date: 12/18/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 8fd63adc76422b7fd9978e626208aa90593f8604
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154866"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Java script gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2 (preview-versie)

In dit artikel leest u hoe u Java script gebruikt voor het maken en beheren van mappen, bestanden en machtigingen in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld. 

> [!IMPORTANT]
> De Java script-bibliotheek die in dit artikel wordt aanbevolen, is momenteel beschikbaar als open bare preview.

[Pakket (node Package Manager) | -](https://www.npmjs.com/package/@azure/storage-file-datalake) voor [beelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [feedback geven](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.
> * Als u dit pakket in een node. js-toepassing gebruikt, hebt u node. js 8.0.0 of hoger nodig.

## <a name="set-up-your-project"></a>Uw project instellen

Installeer Data Lake-client bibliotheek voor Java script door een Terminal venster te openen en de volgende opdracht te typen.

```javascript
npm install @azure/storage-file-datalake
```

Importeer het `storage-file-datalake`-pakket door deze instructie boven aan het code bestand te plaatsen. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Verbinding maken met het account 

Als u de fragmenten in dit artikel wilt gebruiken, moet u een **DataLakeServiceClient** -exemplaar maken dat het opslag account vertegenwoordigt. De eenvoudigste manier om een account sleutel te gebruiken is. 

In dit voor beeld wordt een exemplaar van de **DataLakeServiceClient** gemaakt met behulp van een account sleutel.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> Deze autorisatie methode werkt alleen voor node. js-toepassingen. Als u van plan bent om uw code uit te voeren in een browser, kunt u autoriseren met behulp van Azure Active Directory (AD). Zie het [Azure Storage bestand data Lake-client bibliotheek voor Java script](https://www.npmjs.com/package/@azure/storage-file-datalake) Leesmij-bestand voor meer informatie over hoe u dit doet. 

## <a name="create-a-file-system"></a>Een bestandssysteem maken

Een bestands systeem fungeert als een container voor uw bestanden. U kunt er een maken door een **FileSystemClient** -exemplaar op te halen en vervolgens de methode **FileSystemClient. Create** aan te roepen.

In dit voor beeld wordt een bestands systeem gemaakt met de naam `my-file-system`. 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Een map maken

Maak een directory verwijzing door een **DirectoryClient** -exemplaar op te halen en vervolgens de methode **DirectoryClient. Create** aan te roepen.

In dit voor beeld wordt een map met de naam `my-directory` toegevoegd aan een bestands systeem. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>Een map een andere naam geven of verplaatsen

Wijzig de naam of verplaats een map door de methode **DirectoryClient. rename** aan te roepen. Geef een para meter door aan het pad van de gewenste map. 

In dit voor beeld wordt de naam van een submap gewijzigd in `my-directory-renamed`.

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

In dit voor beeld wordt een map met de naam `my-directory-renamed` verplaatst naar een submap van de map met de naam `my-directory-2`. 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Een map verwijderen

Verwijder een directory door de methode **DirectoryClient. Delete** aan te roepen.

In dit voor beeld wordt een map met de naam `my-directory`verwijderd.   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Een directory-ACL beheren

In dit voor beeld wordt de ACL van een directory met de naam `my-directory`opgehaald en ingesteld. In dit voor beeld worden de machtigingen lezen, schrijven en uitvoeren voor de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgt alle andere Lees toegang.

> [!NOTE]
> Als uw toepassing toegang autoriseert met behulp van Azure Active Directory (Azure AD), moet u ervoor zorgen dat de beveiligings-principal die door uw toepassing wordt gebruikt om toegang te verlenen, is toegewezen aan de [rol Storage BLOB data owner](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van [toegangs beheer in azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Lees eerst een bestand. In dit voor beeld wordt de module node. js `fs` gebruikt. Maak vervolgens een bestands verwijzing in de doel directory door een **FileClient** -exemplaar te maken en vervolgens de methode **FileClient. Create** aan te roepen. Upload een bestand door de methode **FileClient. Append aan** te roepen. Zorg ervoor dat u de upload voltooit door de methode **FileClient. Flush** aan te roepen.

In dit voor beeld wordt een tekst bestand geüpload naar een map met de naam `my-directory`.

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>Een bestands-ACL beheren

In dit voor beeld wordt de ACL van een bestand met de naam `upload-file.txt`opgehaald en ingesteld. In dit voor beeld worden de machtigingen lezen, schrijven en uitvoeren voor de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgt alle andere Lees toegang.

> [!NOTE]
> Als uw toepassing toegang autoriseert met behulp van Azure Active Directory (Azure AD), moet u ervoor zorgen dat de beveiligings-principal die door uw toepassing wordt gebruikt om toegang te verlenen, is toegewezen aan de [rol Storage BLOB data owner](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van [toegangs beheer in azure data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="download-from-a-directory"></a>Downloaden uit een directory

Maak eerst een **FileSystemClient** -exemplaar dat het bestand vertegenwoordigt dat u wilt downloaden. Gebruik de methode **FileSystemClient. Read** om het bestand te lezen. Schrijf vervolgens het bestand. In dit voor beeld wordt de module node. js `fs` gebruikt. 

> [!NOTE]
> Deze methode voor het downloaden van een bestand werkt alleen voor node. js-toepassingen. Als u van plan bent om uw code uit te voeren in een browser, raadpleegt u het bestand [Azure Storage bestand data Lake client bibliotheek voor Java script](https://www.npmjs.com/package/@azure/storage-file-datalake) voor een voor beeld van hoe u dit in een browser kunt doen. 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>Mapinhoud weergeven

In dit voor beeld worden de namen afgedrukt van elke map en elk bestand dat zich bevindt in een map met de naam `my-directory`.

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>Zie ook

* [Pakket (node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Feedback geven](https://github.com/Azure/azure-sdk-for-java/issues)