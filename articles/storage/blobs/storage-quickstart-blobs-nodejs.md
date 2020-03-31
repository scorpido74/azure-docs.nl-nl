---
title: 'Snelstart: Azure Blob-opslagbibliotheek v12 - JavaScript'
description: In deze quickstart leert u hoe u de Azure Blob-opslagclientbibliotheekversie 12 voor JavaScript gebruiken om een container en een blob in Blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: e68e91d90846ab77b994b53be7a84a9dd8bc5a25
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241036"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>Snelstart: blobs beheren met JavaScript v12 SDK in Node.js

In deze quickstart leer je blobs te beheren met Behulp van Node.js. Blobs zijn objecten die grote hoeveelheden tekst of binaire gegevens kunnen bevatten, waaronder afbeeldingen, documenten, streamingmedia en archiefgegevens. Je uploadt, downloadt en lijstblobs en je maakt en verwijdert containers.

[API-naslagdocumentatie](/javascript/api/@azure/storage-blob) | [Bibliotheekbroncodepakket](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [(Node Package Manager)](https://www.npmjs.com/package/@azure/storage-blob) | [Voorbeelden](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een Azure Storage-account. [Een opslagaccount maken](../common/storage-account-create.md).
- [Knooppunt.js.](https://nodejs.org/en/download/)

> [!NOTE]
> Zie [Snelstart: Blobs beheren met JavaScript v10 SDK in Node.js](storage-quickstart-blobs-nodejs-legacy.md)om aan de slag te gaan met de vorige SDK-versie.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Instellen

In deze sectie u een project voorbereiden om te werken met de Azure Blob-opslagclientbibliotheek v12 voor JavaScript.

### <a name="create-the-project"></a>Het project maken

Maak een JavaScript-toepassing met de naam *blob-quickstart-v12*.

1. Maak in een consolevenster (zoals cmd, PowerShell of Bash) een nieuwe map voor het project.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Schakel over naar de nieuw gemaakte *blob-quickstart-v12-map.*

    ```console
    cd blob-quickstart-v12
    ```

1. Een nieuw tekstbestand maken met de naam *package.json*. In dit bestand wordt het node.js-project gedefinieerd. Sla dit bestand op in de map *blob-quickstart-v12.* Hier is de inhoud van het bestand:

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```
    
    Je je eigen naam `author` in het veld zetten, als je wilt.
   
### <a name="install-the-package"></a>Het pakket installeren

Terwijl u zich nog steeds in de map *blob-quickstart-v12* bevindt, `npm install` installeert u de Azure Blob-opslagclientbibliotheek voor JavaScript-pakket met behulp van de opdracht. Met deze opdracht wordt het *bestand package.json* gelezen en wordt het Azure Blob-opslagclientbibliotheek v12 voor JavaScript-pakket en alle bibliotheken geïnstalleerd waarvan het afhankelijk is.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Het app-framework instellen

In de projectmap:

1. Een ander nieuw tekstbestand openen in uw codeeditor
1. Oproepen `require` toevoegen om Azure- en Node.js-modules te laden
1. De structuur voor het programma maken, inclusief basisafhandeling voor uitzonderingen

    Hier is de code:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Sla het nieuwe bestand op als *blob-quickstart-v12.js* in de *map blob-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objectmodel

Azure Blob-opslag is geoptimaliseerd voor het opslaan van enorme hoeveelheden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslagaccount
* Een container in het opslagaccount
* Een blob in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-blobs-introduction/blob1.png)

Gebruik de volgende JavaScript-klassen om met deze bronnen te communiceren:

* [BlobServiceClient:](/javascript/api/@azure/storage-blob/blobserviceclient) `BlobServiceClient` met de klasse u Azure Storage-bronnen en blob-containers manipuleren.
* [ContainerClient:](/javascript/api/@azure/storage-blob/containerclient) `ContainerClient` met de klasse u Azure Storage-containers en hun blobs manipuleren.
* [BlobClient:](/javascript/api/@azure/storage-blob/blobclient) `BlobClient` met de klasse u Azure Storage-blobs manipuleren.

## <a name="code-examples"></a>Codevoorbeelden

In deze voorbeeldcodefragmenten ziet u hoe u het volgende uitvoeren met de Azure Blob-opslagclientbibliotheek voor JavaScript:

* [De verbindingsreeks ophalen](#get-the-connection-string)
* [Een container maken](#create-a-container)
* [Blobs uploaden naar een container](#upload-blobs-to-a-container)
* [De blobs in een container in een lijst weergeven](#list-the-blobs-in-a-container)
* [Blobs downloaden](#download-blobs)
* [Een container verwijderen](#delete-a-container)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de verbindingstekenreeks voor het opslagaccount op uit de omgevingsvariabele die is gemaakt in de sectie [Uw opslagverbindingstekenreeks configureren.](#configure-your-storage-connection-string)

Voeg deze code `main` toe in de functie:

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>Een container maken

Bepaal een naam voor de nieuwe container. De onderstaande code voegt een UUID-waarde toe aan de containernaam om er zeker van te zijn dat deze uniek is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Maak een instantie van de klasse [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) door de methode [fromConnectionString aan te](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) roepen. Roep vervolgens de [getContainerClient-methode](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) aan om een verwijzing naar een container te krijgen. Tot slot u [bellen om](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) de container daadwerkelijk te maken in uw opslagaccount.

Voeg deze code toe `main` aan het einde van de functie:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>Blobs uploaden naar een container

Het volgende codefragment:

1. Hiermee maakt u een teksttekenreeks die u wilt uploaden naar een blob.
1. Hier wordt een verwijzing naar een [BlockBlobClient-object](/javascript/api/@azure/storage-blob/blockblobclient) opgehaald door de [methode getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) op de [containerclient](/javascript/api/@azure/storage-blob/containerclient) aan te roepen vanuit de sectie [Een container](#create-a-container) maken.
1. Uploadt de teksttekenreeksgegevens naar de blob door de [uploadmethode](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) aan te roepen.

Voeg deze code toe `main` aan het einde van de functie:

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Lijst van de blobs in de container door de [methode listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) aan te roepen. In dit geval is er slechts één blob aan de container toegevoegd, zodat de lijstbewerking alleen die ene blob retourneert.

Voeg deze code toe `main` aan het einde van de functie:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Blobs downloaden

Download de eerder gemaakte blob door de [downloadmethode](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) aan te roepen. De voorbeeldcode bevat een `streamToString`helperfunctie genaamd , die wordt gebruikt om een leesbare stream van Node.js in een tekenreeks te lezen.

Voeg deze code toe `main` aan het einde van de functie:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Voeg deze helperfunctie `main` toe *na* de functie:

```javascript
// A helper function used to read a Node.js readable stream into a string
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
```

### <a name="delete-a-container"></a>Een container verwijderen

Met de volgende code worden de resources die de app heeft gemaakt, opgeschoond door de hele container te verwijderen met behulp van de [verwijdermethode.](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) U ook de lokale bestanden verwijderen, als u dat wilt.

Voeg deze code toe `main` aan het einde van de functie:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>De code uitvoeren

Deze app maakt een teksttekenreeks en uploadt deze naar Blob-opslag. In het voorbeeld worden vervolgens de blob(s) in de container weergegeven, wordt de blob gedownload en worden de gedownloade gegevens weergegeven.

Navigeer vanuit een consoleprompt naar de map met het `node` *blob-quickstart-v12.py-bestand* en voer vervolgens de volgende opdracht uit om de app uit te voeren.

```console
node blob-quickstart-v12.js
```

De uitvoer van de app is vergelijkbaar met het volgende voorbeeld:

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

Stap door de code in uw foutopsporing en controleer uw [Azure-portal](https://portal.azure.com) gedurende het hele proces. Controleer of de container wordt gemaakt. U de blob in de container openen en de inhoud bekijken.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart heb je geleerd hoe je blobs uploadt, downloadt en weermaakt met JavaScript.

Ga voor zelfstudies, voorbeelden, quickstarts en andere documentatie naar:

> [!div class="nextstepaction"]
> [Azure voor JavaScript-documentatie](/azure/javascript/)

* Zie de Azure [Blob-opslagclientbibliotheek voor JavaScript voor](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob)meer informatie.
* Ga verder naar [Azure Blob-opslagclientbibliotheek v12 JavaScript-voorbeelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)voor voorbeeld-apps voor blob-opslag .
