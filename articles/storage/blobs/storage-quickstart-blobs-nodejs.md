---
title: 'Snelstartgids: Azure Blob-opslag bibliotheek V12-java script'
description: In deze Quick Start leert u hoe u de Azure Blob Storage-client bibliotheek versie 12 voor Java script kunt gebruiken om een container en een BLOB in Blob-opslag (object) te maken. Hierna leert u hoe u de blob naar uw lokale computer downloadt en hoe u alle blobs in een container kunt weergeven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/19/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 23cd292162a4c16c4b2b5b1f297d23eb2682013f
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74168281"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-javascript"></a>Snelstartgids: Azure Blob Storage-client bibliotheek V12 voor Java script

Ga aan de slag met de Azure Blob Storage-client bibliotheek V12 voor Java script. Azure Blob Storage is Microsoft's oplossing voor opslag van objecten in de cloud. Volg de stappen om het pakket te installeren en voorbeeld code voor basis taken uit te proberen. Blob Storage is geoptimaliseerd voor het opslaan van grote hoeveelheden ongestructureerde gegevens.

> [!NOTE]
> Zie [Snelstartgids: Azure Blob Storage-client bibliotheek voor Java script](storage-quickstart-blobs-nodejs-v10.md)om aan de slag te gaan met de vorige SDK-versie.

Gebruik de Azure Blob Storage-client bibliotheek V12 voor Java script voor het volgende:

* Een container maken
* Een BLOB uploaden naar Azure Storage
* Alle blobs in een container weer geven
* De BLOB downloaden naar uw lokale computer
* Een container verwijderen

[API-referentie documentatie](/javascript/api/@azure/storage-blob) | - [bibliotheek bron code](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [pakket (knooppunt pakket beheer)](https://www.npmjs.com/package/@azure/storage-blob/v/12.0.0) | voor [beelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Maak er gratis een](https://azure.microsoft.com/free/)
* Azure Storage-account: [een opslag account maken](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Huidig [knoop punt. js](https://nodejs.org/en/download/) voor uw besturings systeem.

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
1. De structuur voor het programma maken, inclusief zeer eenvoudige uitzonde ringen verwerken

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

### <a name="copy-your-credentials-from-the-azure-portal"></a>Kopieer uw referenties van de Azure Portal

Wanneer de voorbeeld toepassing een aanvraag indient om Azure Storage, moet deze worden geautoriseerd. Als u een aanvraag wilt autoriseren, voegt u de referenties van uw opslag account toe aan de toepassing als een connection string. U kunt de referenties van het opslagaccount weergeven door de volgende stappen te volgen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Zoek uw opslagaccount.
3. In de sectie **Instellingen** van het overzicht met opslagaccounts selecteert u **Toegangssleutels**. Hier worden de toegangssleutels van uw account weergegeven, evenals de volledige verbindingsreeks voor elke sleutel.
4. Zoek de waarde van de **Verbindingsreeks** onder **key1** en selecteer de knop **Kopiëren** om de verbindingsreeks te kopiëren. U gaat in de volgende stap de waarde voor de verbinding toevoegen aan een omgevingsvariabele.

    ![Schermopname waarin een verbindingsreeks vanuit de Azure-portal wordt gekopieerd](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

Nadat u de verbindingsreeks hebt gekopieerd, schrijft u deze naar een nieuwe omgevingsvariabele op de lokale computer waarop de toepassing wordt uitgevoerd. Als u de omgevingsvariabele wilt instellen, opent u een consolevenster en volgt u de aanwijzingen voor uw besturingssysteem. Vervang `<yourconnectionstring>` door de daad werkelijke connection string.

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Nadat u de omgevings variabele in Windows hebt toegevoegd, moet u een nieuw exemplaar van het opdracht venster starten.

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>Program ma's opnieuw starten

Nadat u de omgevings variabele hebt toegevoegd, start u alle actieve Program ma's die moeten worden gelezen van de omgevings variabele opnieuw. Start uw ontwikkel omgeving of editor bijvoorbeeld opnieuw op voordat u doorgaat.

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
// running the application called CONNECT_STR. If the environment variable is
// created after the application is launched in a console or with Visual Studio,
// the shell or application needs to be closed and reloaded to take the
// environment variable into account.
const CONNECT_STR = process.env.CONNECT_STR;
```

### <a name="create-a-container"></a>Een container maken

Kies een naam voor de nieuwe container. De onderstaande code voegt een UUID-waarde toe aan de container naam om ervoor te zorgen dat deze uniek is.

> [!IMPORTANT]
> Containernamen moeten uit kleine letters bestaan. Zie [Containers, blobs en metagegevens een naam geven en hiernaar verwijderen](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) voor meer informatie over de naamgeving van containers en blobs.

Maak een instantie van de klasse [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) door de methode [fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) aan te roepen. Vervolgens roept u de methode [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) aan om een verwijzing naar een container op te halen. Roep tot slot aan dat u [maakt](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) om de container in uw opslag account te maken.

Voeg deze code toe aan het einde van de `main` functie:

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await BlobServiceClient.fromConnectionString(CONNECT_STR);

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

Down load de eerder gemaakte BLOB door de [Download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) methode aan te roepen. De voorbeeld code bevat een hulp functie met de naam `streamToString` die wordt gebruikt om een door node. js Lees bare stroom te lezen in een teken reeks.

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

Door loop de code in uw debugger en controleer de Azure Portal gedurende het proces. Controleer of de container wordt gemaakt. U kunt de BLOB in de container openen en de inhoud weer geven.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u blobs kunt uploaden, downloaden en vermelden met behulp van Java script.

Als u voor beeld-apps voor Blob-opslag wilt zien, gaat u door naar:

> [!div class="nextstepaction"]
> [Azure Blob Storage SDK V12 java script-voor beelden](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

* Zie de [Azure SDK voor Java script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob/README.md)voor meer informatie.
* Voor zelf studies, voor beelden, snel starten en andere documentatie gaat u naar de [Azure SDK voor Java script-documentatie](/azure/javascript/).
