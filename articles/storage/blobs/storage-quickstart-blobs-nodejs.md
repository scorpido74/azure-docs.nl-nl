---
title: 'Snelstartgids: Azure Blob-opslag bibliotheek V12-java script'
description: In deze Quick Start leert u hoe u de Azure Blob Storage-client bibliotheek versie 12 voor Java script kunt gebruiken om een container en een BLOB in Blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: eabfefbf28b54e4a0a025698f8da48518e7df9bf
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906455"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>Quick Start: blobs beheren met Java script V12 SDK in node. js

In deze Quick Start leert u hoe u blobs beheert met behulp van node. js. Blobs zijn objecten die grote hoeveel heden tekst of binaire gegevens kunnen bevatten, zoals afbeeldingen, documenten, streaming media en gegevens archivering. U kunt blobs uploaden, downloaden en vermelden en u kunt containers maken en verwijderen.

[API-referentie documentatie](/javascript/api/@azure/storage-blob) | - [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [pakket (knooppunt pakket beheer)](https://www.npmjs.com/package/@azure/storage-blob) | voor [beelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Een Azure Storage-account. [Een opslagaccount maken](../common/storage-account-create.md).
- [Node.js](https://nodejs.org/en/download/).

> [!NOTE]
> Zie [Quick Start: blobs beheren met Java script V10 toevoegen SDK in node. js](storage-quickstart-blobs-nodejs-legacy.md)om aan de slag te gaan met de vorige SDK-versie.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Blob Storage-client bibliotheek V12 voor Java script.

### <a name="create-the-project"></a>Het project maken

Maak een Java script-toepassing met de naam *BLOB-Quick Start-V12*.

1. Maak in een console venster (zoals cmd, Power shell of bash) een nieuwe map voor het project.

    ```console
    mkdir blob-quickstart-v12
    ```

1. Schakel over naar de zojuist gemaakte map *BLOB-Quick Start-V12* .

    ```console
    cd blob-quickstart-v12
    ```

1. Maak een nieuw tekst bestand met de naam *package. json*. Dit bestand definieert het node. js-project. Sla dit bestand op in de map *BLOB-Quick Start-V12* . Dit is de inhoud van het bestand:

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
    
    Als u wilt, kunt u uw eigen naam in het veld `author` opnemen.
   
### <a name="install-the-package"></a>Het pakket installeren

Installeer de Azure Blob Storage-client bibliotheek voor Java script-pakket in de map *BLOB-Quick Start-V12* door de opdracht `npm install` te gebruiken. Met deze opdracht wordt het bestand *package. json* gelezen en wordt de Azure Blob Storage-client bibliotheek V12 voor Java script-pakket en alle bibliotheken waarvan het afhankelijk is, geïnstalleerd.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>Het app-Framework instellen

Vanuit de projectmap:

1. Een ander nieuw tekst bestand openen in de code-editor
1. `require`-aanroepen toevoegen om Azure-en node. js-modules te laden
1. De structuur voor het programma maken, met inbegrip van basis afhandeling van uitzonde ringen

    Hier volgt de code:

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. Sla het nieuwe bestand op als *BLOB-QuickStart-V12. js* in de map *BLOB-Quick Start-V12* .

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Object model

Azure Blob-opslag is geoptimaliseerd voor het opslaan van enorme hoeveel heden ongestructureerde gegevens. Ongestructureerde gegevens zijn gegevens die niet voldoen aan een bepaald gegevensmodel of bepaalde definitie, zoals tekst of binaire gegevens. Er zijn drie typen resources voor blobopslag:

* Het opslag account
* Een container in het opslag account
* Een BLOB in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-blob-introduction/blob1.png)

Gebruik de volgende Java script-klassen om te communiceren met deze resources:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): met de klasse `BlobServiceClient` kunt u Azure storage resources en BLOB-containers bewerken.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): met de klasse `ContainerClient` kunt u Azure Storage containers en de bijbehorende blobs bewerken.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): met de klasse `BlobClient` kunt u Azure Storage blobs bewerken.

## <a name="code-examples"></a>Code voorbeelden

In deze voorbeeld code fragmenten ziet u hoe u het volgende kunt uitvoeren met de Azure Blob Storage-client bibliotheek voor Java script:

* [De connection string ophalen](#get-the-connection-string)
* [Een container maken](#create-a-container)
* [Blobs uploaden naar een container](#upload-blobs-to-a-container)
* [De blobs in een container weer geven](#list-the-blobs-in-a-container)
* [Blobs downloaden](#download-blobs)
* [Container verwijderen](#delete-a-container)

### <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De onderstaande code haalt de connection string voor het opslag account op uit de omgevings variabele die in de sectie [uw opslag Connection String configureren](#configure-your-storage-connection-string) is gemaakt.

Voeg deze code toe in de `main` functie:

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

Kies een naam voor de nieuwe container. De onderstaande code voegt een UUID-waarde toe aan de container naam om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Maak een instantie van de klasse [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) door de methode [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) aan te roepen. Vervolgens roept u de methode [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) aan om een verwijzing naar een container op te halen. Roep tot slot aan dat u [maakt](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) om de container in uw opslag account te maken.

Voeg deze code toe aan het einde van de `main` functie:

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

Het volgende code fragment:

1. Hiermee maakt u een teken reeks die wordt geüpload naar een blob.
1. Hiermee wordt een verwijzing naar een [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) -object opgehaald door de methode [GetBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) op de [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) aan te roepen vanuit de sectie [een container maken](#create-a-container) .
1. Uploadt de tekst teken reeks gegevens naar de BLOB door de [Upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) methode aan te roepen.

Voeg deze code toe aan het einde van de `main` functie:

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

Vermeld de blobs in de container door de methode [listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) aan te roepen. In dit geval is er slechts één BLOB aan de container toegevoegd, zodat de vermelding in de lijst alleen die ene BLOB retourneert.

Voeg deze code toe aan het einde van de `main` functie:

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Blobs downloaden

Down load de eerder gemaakte BLOB door de [Download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) methode aan te roepen. De voorbeeld code bevat een hulp functie met de naam `streamToString`, die wordt gebruikt voor het lezen van een door node. js Lees bare stroom in een teken reeks.

Voeg deze code toe aan het einde van de `main` functie:

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

Voeg deze hulp functie toe *na* de functie `main`:

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

Met de volgende code wordt de resources opgeschoond die de app heeft gemaakt door de volledige container te verwijderen met de methode [Delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) . U kunt ook de lokale bestanden verwijderen, indien gewenst.

Voeg deze code toe aan het einde van de `main` functie:

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>De code uitvoeren

Met deze app wordt een tekst teken reeks gemaakt en geüpload naar Blob-opslag. In het voor beeld worden vervolgens de BLOB (s) in de container weer gegeven, wordt de BLOB gedownload en worden de gedownloade gegevens weer gegeven.

Navigeer vanuit een console prompt naar de map met het *BLOB-QuickStart-V12.py* -bestand en voer de volgende `node` opdracht uit om de app uit te voeren.

```console
node blob-quickstart-v12.js
```

De uitvoer van de app is vergelijkbaar met het volgende voor beeld:

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

Door loop de code in uw debugger en controleer de [Azure Portal](https://portal.azure.com) gedurende het proces. Controleer of de container wordt gemaakt. U kunt de BLOB in de container openen en de inhoud weer geven.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u blobs kunt uploaden, downloaden en vermelden met behulp van Java script.

Voor zelf studies, voor beelden, Quick starts en andere documentatie gaat u naar:

> [!div class="nextstepaction"]
> [Documentatie voor Azure voor Java script](/azure/javascript/)

* Zie de [Azure Blob Storage-client bibliotheek voor Java script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob)voor meer informatie.
* Als u voor beeld-apps voor Blob-opslag wilt zien, gaat u naar [Azure Blob Storage-client bibliotheek V12 java script](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)-voor beelden.
