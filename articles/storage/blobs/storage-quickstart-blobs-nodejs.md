---
title: Een BLOB maken in Azure Storage voor node. js
description: Maak een opslagaccount en een container in object(Blob)-opslag. Gebruik vervolgens de Azure Storage-client bibliotheek voor node. js v2 om een BLOB te uploaden naar Azure Storage, een BLOB te downloaden en de blobs in een container weer te geven.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/04/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.custom: seo-javascript-september2019
ms.openlocfilehash: 8fada27f82fea557d7d078f06b5c05e7d1db906d
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861234"
---
# <a name="how-to-upload-download-and-list-blobs-using-the-client-library-for-nodejs-v2"></a>Blobs uploaden, downloaden en vermelden met behulp van de client bibliotheek voor node. js v2

In deze hand leiding vindt u informatie over het gebruik van de client bibliotheek voor node. js v2 om blobs te uploaden, te downloaden en weer te geven met Azure Blob Storage.

> [!TIP]
> De meest recente versie van de Azure Storage-client bibliotheek voor node. js is V10 toevoegen. U wordt aangeraden de nieuwste versie van de client bibliotheek te gebruiken wanneer dat mogelijk is. Om aan de slag te gaan met [V10 toevoegen, raadpleegt u Quick Start: U kunt blobs uploaden, downloaden, weer geven en verwijderen met Azure Storage-client bibliotheek voor Java script](storage-quickstart-blobs-nodejs-v10.md)V10 toevoegen (preview).

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Maak een Azure-opslag account in de [Azure Portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM). Zie voor meer informatie over het maken van het account [Een opslagaccount maken](../common/storage-quickstart-create-account.md).

## <a name="download-the-sample-application"></a>De voorbeeldtoepassing downloaden

De [voorbeeld toepassing](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git) is een eenvoudige node. js-console toepassing. Kloon om te beginnen de opslagplaats naar uw computer met de volgende opdracht:

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

Als u de toepassing wilt openen, gaat u naar de map *storage-blobs-node-quickstart* en opent u deze in uw favoriete omgeving voor het bewerken van code.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>De opslagverbindingsreeks configureren

U moet eerst de verbindingsreeks voor uw opslagaccount opgeven om de toepassing te kunnen uitvoeren. Het voorbeeld van de opslagplaats bevat een bestand met de naam *.env.example*. U kunt de naam van dit bestand wijzigen door de *.example*-extensie te verwijderen, wat resulteert in een bestand met de naam *.env*. In het bestand *.env* voegt u de waarde van de verbindingsreeks toe na de *AZURE_STORAGE_CONNECTION_STRING*-sleutel.

## <a name="install-required-packages"></a>De vereiste pakketten installeren

Voer in de toepassingsmap de opdracht *npm install* uit om de vereiste pakketten voor de toepassing te installeren.

```bash
npm install
```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren
Nu de afhankelijkheden zijn geïnstalleerd, kunt u het voorbeeld uitvoeren door de volgende opdracht te geven:

```bash
npm start
```

De uitvoer van het script ziet er ongeveer als volgt uit:

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme.md
Blob downloaded blob content: "hello Blob SDK"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

Als u een nieuw opslag account voor dit voor beeld gebruikt, ziet u mogelijk geen container namen die worden vermeld onder het label "*containers*".

## <a name="understanding-the-code"></a>De code begrijpen
De eerste expressie wordt gebruikt om waarden in de omgevingsvariabelen te laden.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
```

De module *dotenv* laadt omgevingsvariabelen wanneer de app lokaal wordt uitgevoerd voor foutopsporing. De waarden worden gedefinieerd in een bestand met de naam *.env* en geladen in de huidige uitvoeringscontext. In een productiecontext worden deze waarden geleverd door de serverconfiguratie, daarom wordt deze code alleen uitgevoerd als het script niet wordt uitgevoerd in een ‘productie’context.

```javascript
const path = require('path');
const storage = require('azure-storage');
```

Dit is het doel van de modules: 

bestand met naam *.env* naar de huidige uitvoeringscontext
- *path* is vereist om het absolute pad van het bestand te bepalen voor uploaden naar blob-opslag
- *Azure-Storage* is de [Azure Storage-client bibliotheek](https://docs.microsoft.com/javascript/api/azure-storage) module voor node. js

Vervolgens wordt de variabele **blobService** geïnitialiseerd als nieuw exemplaar van de Azure Blobservice.

```javascript
const blobService = storage.createBlobService();
```

In de volgende implementatie wordt elk van de *blobService*-functies verpakt in een *Promise* voor toegang tot de JavaScript-functie *async* en de *await*-operator voor het stroomlijnen van de callback van de [Azure Storage API](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). Wanneer een geslaagde reactie voor elke functie wordt gegenereerd, wordt de promise omgezet met relevante gegevens, samen met een bericht dat specifiek bij die actie hoort.

### <a name="list-containers"></a>Containers weergeven

De *listContainers*-functie roept [listContainersSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) aan die een verzameling van containers in groepen retourneert.

```javascript
const listContainers = async () => {
    return new Promise((resolve, reject) => {
        blobService.listContainersSegmented(null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} containers`, containers: data.entries });
            }
        });
    });
};
```

De grootte van de groepen kan worden geconfigureerd via [ListContainersOptions](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.listcontaineroptions?view=azure-node-latest). Het aanroepen van *listContainersSegmented* retourneert blobmetagegevens als een matrix met [ContainerResult](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.containerresult?view=azure-node-latest)-instanties. Resultaten worden geretourneerd in batches van 5000 blobs (segmenten). Als er in een container meer dan 5000 blobs zijn, bevatten de resultaten een waarde voor *continuationToken*. Als u volgende segmenten uit de blobcontainer wilt weergeven, kunt u het vervolgtoken als tweede argument doorgeven aan *listContainersSegmented*.

### <a name="create-a-container"></a>Een container maken

De *createContainer*-functie roept [createContainerIfNotExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) aan en stelt het juiste toegangsniveau voor de blob in.

```javascript
const createContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

De tweede parameter (*options*) voor **createContainerIfNotExists** accepteert een waarde voor [publicAccessLevel](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest). De waarde *blob* voor *publicAccessLevel* geeft aan dat specifieke blob-gegevens worden weergegeven aan het publiek. Deze instelling is het tegenovergestelde van toegang op *container*niveau, waarmee de inhoud van de container kan worden weergegeven.

Door **createContainerIfNotExists** kan de toepassing de opdracht *createContainer* meerdere keren uitvoeren zonder dat er fouten worden geretourneerd wanneer de container al bestaat. In een productieomgeving hoeft u **createContainerIfNotExists** vaak maar eenmaal aan te roepen wanneer dezelfde container overal in de toepassing wordt gebruikt. In dergelijke gevallen kunt u de container vooraf via de portal of via de Azure CLI maken.

### <a name="upload-text"></a>Tekst uploaden

De *uploadString*-functie roept [createBlockBlobFromText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) aan om een willekeurige tekenreeks naar de blobcontainer te schrijven of die te overschrijven.

```javascript
const uploadString = async (containerName, blobName, text) => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromText(containerName, blobName, text, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Text "${text}" is written to blob storage` });
            }
        });
    });
};
```
### <a name="upload-a-local-file"></a>Een lokaal bestand uploaden

De *uploadLocalFile*-functie maakt gebruik van de [createBlockBlobFromLocalFile](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromlocalfile-string--string--string--errororresult-blobresult--)-functie om een bestand te uploaden en te schrijven of overschrijven vanuit het bestandssysteem naar de blobopslag. 

```javascript
const uploadLocalFile = async (containerName, filePath) => {
    return new Promise((resolve, reject) => {
        const fullPath = path.resolve(filePath);
        const blobName = path.basename(filePath);
        blobService.createBlockBlobFromLocalFile(containerName, blobName, fullPath, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Local file "${filePath}" is uploaded` });
            }
        });
    });
};
```
Andere methoden voor het uploaden van inhoud naar blobs zijn werken met [tekst](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest-string--string--string---buffer--errororresult-blobresult--) en [streams](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromstream-string--string--stream-readable--number--errororresult-blobresult--). Om te controleren of het bestand is geüpload naar de blob-opslag, kunt u met de [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) de gegevens in uw account bekijken.

### <a name="list-the-blobs"></a>Blobs weergeven

De *listBlobs*-functie roept de [listBlobsSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#listblobssegmented-string--continuationtoken--errororresult-listblobsresult--)-methode aan om een lijst met blobmetagegevens in een container te retourneren. 

```javascript
const listBlobs = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} blobs in '${containerName}'`, blobs: data.entries });
            }
        });
    });
};
```

Het aanroepen van *listBlobsSegmented* retourneert blob-metagegevens als een matrix met [BlobResult](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.blobresult?view=azure-node-latest)-instanties. Resultaten worden geretourneerd in batches van 5000 blobs (segmenten). Als er in een container meer dan 5000 blobs zijn, bevatten de resultaten een waarde voor **continuationToken**. Als u volgende segmenten uit de blob-container wilt weergeven, kunt u het vervolgtoken als tweede argument doorgeven aan **listBlobSegmented**.

### <a name="download-a-blob"></a>Een blob downloaden

De *downloadBlob*-functie maakt gebruik van [getBlobToText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) om de inhoud van de blob te downloaden naar het opgegeven absolute bestandspad.

```javascript
const downloadBlob = async (containerName, blobName) => {
    const dowloadFilePath = path.resolve('./' + blobName.replace('.txt', '.downloaded.txt'));
    return new Promise((resolve, reject) => {
        blobService.getBlobToText(containerName, blobName, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Blob downloaded "${data}"`, text: data });
            }
        });
    });
};
```
De hier getoonde implementatie verandert de bron en retourneert de inhoud van de blob als tekenreeks. U kunt de blob ook als [stream](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) downloaden en ook rechtstreeks naar een [lokaal bestand](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest).

### <a name="delete-a-blob"></a>Een blob verwijderen

De *deleteBlob*-functie roept de [deleteBlobIfExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#deleteblobifexists-string--string--errororresult-boolean--)-functie aan. Zoals de naam al aangeeft, retourneert deze functie geen fout als de blob al is verwijderd.

```javascript
const deleteBlob = async (containerName, blobName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="delete-a-container"></a>Een container verwijderen

Containers worden verwijderd door de *deleteContainer*-methode aan te roepen in de blobservice en de containernaam door te geven.

```javascript
const deleteContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteContainer(containerName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' deleted` });
            }
        });
    });
};
```

### <a name="calling-code"></a>Code aanroepen

Om de *async/await*-syntaxis van JavaScript te ondersteunen, wordt alle aanroepcode verpakt in een functie met de naam *execute*. Vervolgens wordt execute-functie aangeroepen en behandeld als promise.

```javascript
async function execute() {
    // commands 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
Alle onderstaande code wordt uitgevoerd binnen de execute-functie waar de `// commands`-opmerking is geplaatst.

Eerst worden de relevante variabelen gedeclareerd om namen en voorbeeldinhoud toe te wijzen en om het lokale bestand aan te wijzen voor upload naar de blobopslag.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello Node SDK";
const localFilePath = "./readme.md";
let response;
```

Om de containers in de opslagaccount weer te geven, wordt de functie listContainers aangeroepen en de geretourneerde lijst met containers wordt in het uitvoervenster weergegeven.

```javascript
console.log("Containers:");
response = await listContainers();
response.containers.forEach((container) => console.log(` -  ${container.name}`));
```

Zodra de lijst met containers beschikbaar is, kunt u de matrixmethode *findIndex* gebruiken om te zien of de container die u wilt maken al bestaat. Als de container nog niet bestaat, wordt die aangemaakt.

```javascript
const containerDoesNotExist = response.containers.findIndex((container) => container.name === containerName) === -1;

if (containerDoesNotExist) {
    await createContainer(containerName);
    console.log(`Container "${containerName}" is created`);
}
```
Vervolgens worden een tekenreeks en een lokaal bestand geüpload naar Blobopslag.

```javascript
await uploadString(containerName, blobName, content);
console.log(`Blob "${blobName}" is uploaded`);

response = await uploadLocalFile(containerName, localFilePath);
console.log(response.message);
```
Het proces om de blobs weer te geven is hetzelfde als het weergeven van containers. De aanroep van *listBlobs* retourneert een matrix van blobs in de container die worden weergegeven in het uitvoervenster.

```javascript
console.log(`Blobs in "${containerName}" container:`);
response = await listBlobs(containerName);
response.blobs.forEach((blob) => console.log(` - ${blob.name}`));
```

Om een blob te downloaden, wordt het antwoord vastgelegd en gebruikt om bij de waarde van de blob te kunnen komen. Van het antwoord wordt readableStreamBody geconverteerd naar een tekenreeks en weergegeven in het uitvoervenster.

```javascript
response = await downloadBlob(containerName, blobName);
console.log(`Downloaded blob content: "${response.text}"`);
```

Ten slotte worden de blob en container verwijderd uit de opslagaccount.

```javascript
await deleteBlob(containerName, blobName);
console.log(`Blob "${blobName}" is deleted`);

await deleteContainer(containerName);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>Resources opschonen
Alle gegevens die zijn geschreven naar het opslagaccount worden automatisch verwijderd aan het einde van de voorbeeldcode. 

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>Resources voor het ontwikkelen van Node.js-toepassingen met blobs

Zie de volgende aanvullende bronnen voor Node.js-ontwikkeling met Blob-opslag:

### <a name="binaries-and-source-code"></a>Binaire bestanden en broncode

- Bekijk en installeer de [broncode voor de Node.js-clientbibliotheek](https://github.com/Azure/azure-storage-node) voor Azure Storage op GitHub.

### <a name="client-library-reference-and-samples"></a>Naslaginformatie en voorbeelden voor de .NET-clientbibliotheek

- Zie het [Node.js API-referentiemateriaal](https://docs.microsoft.com/javascript/api/overview/azure/storage) voor meer informatie over de Node.js-clientbibliotheek.
- Verken [Blob-opslagvoorbeelden](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob) die zijn geschreven met de Node.js-clientbibliotheek.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt beschreven hoe u een bestand kunt uploaden tussen een lokale schijf en Azure Blob-opslag met behulp van node. js. Voor meer informatie over het werken met Blobopslag, gaat u naar de GitHub-opslagplaats.

> [!div class="nextstepaction"]
> [Microsoft Azure Storage SDK voor node. js en Java script voor browsers](https://github.com/Azure/azure-storage-node)
