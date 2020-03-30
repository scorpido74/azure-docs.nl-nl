---
title: JavaScript gebruiken voor bestanden & ACL's in Azure Data Lake Storage Gen2
description: Gebruik Azure Storage Data Lake-clientbibliotheek voor JavaScript om mappen en bestands- en adreslijstlijsten (ACL) te beheren in opslagaccounts waarvoor hiërarchische naamruimte (HNS) is ingeschakeld.
author: normesta
ms.service: storage
ms.date: 03/20/2020
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 04d0d23bdbdaeda6a4823c900badb3133ba9eeae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061543"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>JavaScript gebruiken om mappen, bestanden en ACL's te beheren in Azure Data Lake Storage Gen2

In dit artikel ziet u hoe u JavaScript gebruiken om mappen, bestanden en machtigingen te maken en te beheren in opslagaccounts waarop hiërarchische naamruimte (HNS) is ingeschakeld. 

[Pakket (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [Monsters](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [Geven Feedback](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslagaccount met hiërarchische naamruimte (HNS) ingeschakeld. Volg [deze](data-lake-storage-quickstart-create-account.md) instructies om er een te maken.
> * Als u dit pakket gebruikt in een Node.js-toepassing, hebt u Node.js 8.0.0 of hoger nodig.

## <a name="set-up-your-project"></a>Uw project instellen

Installeer de Data Lake-clientbibliotheek voor JavaScript door een terminalvenster te openen en vervolgens de volgende opdracht te typen.

```javascript
npm install @azure/storage-file-datalake
```

Importeer `storage-file-datalake` het pakket door deze instructie boven aan uw codebestand te plaatsen. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>Verbinding maken met het account 

Als u de fragmenten in dit artikel wilt gebruiken, moet u een **Instantie DataLakeServiceClient** maken die het opslagaccount vertegenwoordigt. 

### <a name="connect-by-using-an-account-key"></a>Verbinding maken met een accountsleutel

Dit is de eenvoudigste manier om verbinding te maken met een account. 

In dit voorbeeld wordt een **Instantie DataLakeServiceClient** gemaakt met behulp van een accountsleutel.

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
> Deze autorisatiemethode werkt alleen voor Node.js-toepassingen. Als u van plan bent uw code in een browser uit te voeren, u deze autoriseren met Azure Active Directory (AD). 

### <a name="connect-by-using-azure-active-directory-ad"></a>Verbinding maken met Azure Active Directory (AD)

U de [Azure-identiteitsclientbibliotheek voor JS](https://www.npmjs.com/package/@azure/identity) gebruiken om uw toepassing te verifiëren met Azure AD.

In dit voorbeeld wordt een **GegevensLakeServiceClient-exemplaar** gemaakt met behulp van een client-id, een clientgeheim en een tenant-id.  Zie Een token [van Azure AD ophalen van Azure AD voor het toestaan van aanvragen van een clienttoepassing](../common/storage-auth-aad-app.md)om deze waarden te verkrijgen.

```javascript
function GetDataLakeServiceClientAD(accountName, clientID, clientSecret, tenantID) {

  const credential = new ClientSecretCredential(tenantID, clientID, clientSecret);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, credential);

  return datalakeServiceClient;             
}
```

> [!NOTE]
> Zie voor meer voorbeelden de [Azure-identiteitsclientbibliotheek voor JS-documentatie.](https://www.npmjs.com/package/@azure/identity)

## <a name="create-a-file-system"></a>Een bestandssysteem maken

Een bestandssysteem fungeert als een container voor uw bestanden. U er een maken door een **FileSystemClient-exemplaar** te downloaden en vervolgens de methode **FileSystemClient.Create aan** te roepen.

In dit voorbeeld wordt `my-file-system`een bestandssysteem met de naam . 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>Een map maken

Maak een adreslijstverwijzing door een **directoryclient-exemplaar** op te halen en vervolgens de methode **DirectoryClient.create aan** te roepen.

In dit voorbeeld `my-directory` wordt een map toegevoegd met de naam aan een bestandssysteem. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>De naam van een map wijzigen of verplaatsen

Wijzig de naam of verplaats een map door de **methode DirectoryClient.rename aan te** roepen. Geef het pad van de gewenste map een parameter. 

In dit voorbeeld wordt de naam `my-directory-renamed`van een submap gewijzigd in de naam .

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

In dit voorbeeld `my-directory-renamed` wordt een map met de `my-directory-2`naam verplaatst naar een submap van een map met de naam . 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>Een map verwijderen

Een map verwijderen door de methode **DirectoryClient.delete aan** te roepen.

In dit voorbeeld wordt `my-directory`een map met de naam .   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>Een map ACL beheren

In dit voorbeeld wordt de ACL `my-directory`van een map met de naam . In dit voorbeeld leest, schrijft en voert u machtigingen uit, geeft de eigenaarsgroep alleen lees- en uitvoermachtigingen en geeft alle anderen leestoegang.

> [!NOTE]
> Als uw toepassing de toegang autoriseert met Azure Active Directory (Azure AD), controleert u of de beveiligingsprincipal die uw toepassing gebruikt om toegang te autoriseren, is toegewezen aan de [rol Opslagblob-gegevenseigenaar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)voor meer informatie over hoe ACL-machtigingen worden toegepast en de effecten van het wijzigen ervan.

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

Lees eerst een bestand. In dit voorbeeld wordt `fs` de module Node.js gebruikt. Maak vervolgens een bestandsverwijzing in de doelmap door een **FileClient-exemplaar te** maken en vervolgens de methode **FileClient.create aan** te roepen. Upload een bestand door de methode **FileClient.append aan te** roepen. Zorg ervoor dat u de upload voltooit door de methode **FileClient.flush aan** te roepen.

In dit voorbeeld wordt een tekstbestand geüpload naar een map met de naam `my-directory`.'

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

## <a name="manage-a-file-acl"></a>Een bestand ACL beheren

In dit voorbeeld wordt de ACL `upload-file.txt`van een bestand met de naam . In dit voorbeeld leest, schrijft en voert u machtigingen uit, geeft de eigenaarsgroep alleen lees- en uitvoermachtigingen en geeft alle anderen leestoegang.

> [!NOTE]
> Als uw toepassing de toegang autoriseert met Azure Active Directory (Azure AD), controleert u of de beveiligingsprincipal die uw toepassing gebruikt om toegang te autoriseren, is toegewezen aan de [rol Opslagblob-gegevenseigenaar](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Zie [Toegangsbeheer in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)voor meer informatie over hoe ACL-machtigingen worden toegepast en de effecten van het wijzigen ervan.

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

## <a name="download-from-a-directory"></a>Downloaden uit een map

Maak eerst een **FileSystemClient-exemplaar** dat het bestand vertegenwoordigt dat u wilt downloaden. Gebruik de **methode FileSystemClient.read** om het bestand te lezen. Schrijf vervolgens het bestand. In dit voorbeeld wordt `fs` de module Node.js gebruikt om dat te doen. 

> [!NOTE]
> Deze methode voor het downloaden van een bestand werkt alleen voor Node.js-toepassingen. Als u van plan bent uw code in een browser uit te voeren, raadpleegt u de [Azure Storage File Data Lake-clientbibliotheek voor JavaScript-leesmijbestand](https://www.npmjs.com/package/@azure/storage-file-datalake) voor een voorbeeld van hoe u dit in een browser doen. 

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

In dit voorbeeld worden de namen van elke map `my-directory`en bestand met de naam .

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

* [Package (Node Package Manager)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [Monsters](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [Feedback geven](https://github.com/Azure/azure-sdk-for-java/issues)