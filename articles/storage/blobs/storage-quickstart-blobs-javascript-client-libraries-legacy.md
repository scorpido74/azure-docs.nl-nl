---
title: 'Quick Start: Azure Blob-opslag voor Java script-V10 toevoegen in de browser'
description: Meer informatie over het uploaden, vermelden en verwijderen van blobs met behulp van Java script V10 toevoegen SDK in een HTML-pagina.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.author: mhopkins
ms.date: 01/24/2020
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: 257af309ebdb9080c3cd60b8b89a2c992ecf5145
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906569"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-browser"></a>Quick Start: blobs beheren met Java script V10 toevoegen SDK in browser

In deze Quick Start leert u hoe u blobs beheert met behulp van Java script-code die volledig in de browser wordt uitgevoerd. Blobs zijn objecten die grote hoeveel heden tekst of binaire gegevens kunnen bevatten, zoals afbeeldingen, documenten, streaming media en gegevens archivering. U gebruikt vereiste beveiligings maatregelen om beveiligde toegang tot uw Blob Storage-account te garanderen.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Een Azure Storage-account. [Een opslagaccount maken](../common/storage-account-create.md).
- Een lokale webserver. In dit artikel wordt [node. js](https://nodejs.org) gebruikt voor het openen van een basis server.
- [Visual Studio Code](https://code.visualstudio.com).
- Een VS-code-extensie voor fout opsporing in een browser, zoals [fout opsporing voor Chrome](vscode:extension/msjsdiag.debugger-for-chrome) of [fout opsporing voor micro soft Edge](vscode:extension/msjsdiag.debugger-for-edge).

## <a name="setting-up-storage-account-cors-rules"></a>CORS-regels voor het opslagaccount instellen

Voordat uw webtoepassing toegang kan krijgen tot een Blob-opslag van de client, moet u uw account configureren om [Cross-Origin-resource delen](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)of CORS in te scha kelen.

Ga terug naar Azure Portal en selecteer uw opslagaccount. Als u een nieuwe CORS-regel wilt definiëren, gaat u naar de sectie **instellingen** en klikt u op de koppeling **CORS** . Klik vervolgens op de knop **Toevoegen** om het venster **CORS-regel toevoegen** te openen. Voor deze snelstart maakt u een open CORS-regel:

![CORS-instellingen in Azure Blob Storage-account](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

In de volgende tabel worden alle CORS-instellingen beschreven en de waarden voor het definiëren van de regel uitgelegd.

|Instelling  |Waarde  | Beschrijving |
|---------|---------|---------|
| Toegestane oorsprongen | * | Accepteert een door komma's gescheiden lijst met domeinen die als acceptabele oorsprongen zijn ingesteld. Als de waarde wordt ingesteld op `*`, hebben alle domeinen toegang tot het opslagaccount. |
| Toegestane bewerkingen     | delete, get, head, merge, post, options en put | Vermeldt de HTTP-bewerkingen die zijn toegestaan om voor het opslagaccount te worden uitgevoerd. Voor deze snelstart selecteert u alle beschikbare opties. |
| Toegestane headers | * | Definieert een lijst met aanvraagheaders (inclusief headers met een voorvoegsel) die door het opslagaccount zijn toegestaan. Als de waarde wordt ingesteld op `*`, hebben alle headers toegang. |
| Weergegeven headers | * | Vermeldt de door het account toegestane antwoordheaders. Als de waarde wordt ingesteld op `*`, kan het account elke header verzenden.  |
| Maximumleeftijd (seconden) | 86400 | De maximale periode dat de browser de voorbereidende aanvraag OPTIONS in de cache opslaat. De waarde *86400* betekent dat de cache een hele dag in stand blijft. |

> [!IMPORTANT]
> Zorg ervoor dat alle instellingen die u in productie gebruikt, de minimale benodigde toegangs rechten voor uw opslag account beschikbaar maakt om beveiligde toegang te behouden. De hier beschreven CORS-instellingen zijn geschikt voor een snelstart, omdat ze een minder streng beveiligingsbeleid definiëren. Deze instellingen worden echter niet aangeraden in reële situaties.

Vervolgens gebruikt u Azure Cloud Shell om een beveiligingstoken te maken.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Een Shared Access Signature maken

Shared Access Signature (handtekening voor gedeelde toegang, SAS) wordt gebruikt door de code die wordt uitgevoerd in de browser om aanvragen bij de blob-opslag te verifiëren. Als u een SAS gebruikt, kan de client toegang tot de opslagresources autoriseren zonder de toegangssleutel of verbindingsreeks van het account. Zie [Handtekeningen voor gedeelde toegang gebruiken](../common/storage-sas-overview.md) voor meer informatie over SAS.

U kunt een SAS maken met behulp van de Azure CLI via de Azure Cloud shell of met de Azure Portal of Azure Storage Explorer. In de volgende tabel worden de para meters beschreven die u moet opgeven om een SAS te genereren met de CLI.

| Parameter      |Beschrijving  | Tijdelijke aanduiding |
|----------------|-------------|-------------|
| *expiry*       | De vervaldatum van het toegangstoken in de notatie JJJJ-MM-DD. Voer voor deze snelstart de datum van morgen in. | *FUTURE_DATE* |
| *account-name* | De naam van het opslagaccount. Gebruik de naam die in een vorige stap is bewaard. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | De opslagaccountsleutel. Gebruik de sleutel die in een vorige stap is bewaard. | *YOUR_STORAGE_ACCOUNT_KEY* |

Gebruik de volgende CLI-opdracht, met de werkelijke waarden voor elke tijdelijke aanduiding, om een SAS te genereren die u in uw Java script-code kunt gebruiken.

```azurecli-interactive
az storage account generate-sas \
  --permissions racwdl \
  --resource-types sco \
  --services b \
  --expiry FUTURE_DATE \
  --account-name YOUR_STORAGE_ACCOUNT_NAME \
  --account-key YOUR_STORAGE_ACCOUNT_KEY
```

Mogelijk vindt u de reeks waarden na elke parameter wat cryptisch. Deze parameterwaarden zijn afkomstig van de eerste letter van hun respectieve machtiging. In de volgende tabel wordt uitgelegd waar de waarden vandaan komen:

| Parameter        | Waarde   | Beschrijving  |
|------------------|---------|---------|
| *permissions*    | racwdl  | Deze SAS staat de mogelijkheden *read*, *append*, *create*, *write*, *delete* en *list* toe. |
| *resource-types* | sco     | De resources die waar de SAS invloed op heeft, zijn *service*, *container* en *object*. |
| *services*       | b       | De service waar de SAS invloed op heeft is de *blob*-service. |

Nu de SAS is gegenereerd, kopieert u de retour waarde en slaat u deze ergens op voor gebruik in een toekomstige stap. Als u uw SAS hebt gegenereerd met een andere methode dan de Azure CLI, moet u de eerste `?` verwijderen als deze aanwezig is. Dit teken is een URL-scheidings teken dat al is opgenomen in de URL-sjabloon, verderop in dit onderwerp waarin de SA'S worden gebruikt.

> [!IMPORTANT]
> Geef in productie SAS-tokens altijd door met behulp van SSL. SAS-tokens moeten op de server worden gegenereerd en naar de HTML-pagina verzonden om ze opnieuw door te geven aan Azure Blob Storage. U kunt eventueel een serverloze functie gebruiken om SAS-tokens te genereren. Azure Portal bevat functiesjablonen waarmee een SAS kan worden gemaakt met behulp van een JavaScript-functie.

## <a name="implement-the-html-page"></a>HTML-pagina implementeren

In deze sectie maakt u een eenvoudige webpagina en configureert u VS code om de pagina te starten en fouten op te sporen. Voordat u kunt starten, moet u echter node. js gebruiken om een lokale webserver te starten en de pagina te bedienen wanneer uw browser deze aanvraagt. Vervolgens voegt u Java script-code toe om verschillende Blob Storage-Api's aan te roepen en de resultaten op de pagina weer te geven. U kunt de resultaten van deze aanroepen ook weer geven in de [Azure Portal](https://portal.azure.com), [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer)en de [Azure Storage extensie](vscode:extension/ms-azuretools.vscode-azurestorage) voor VS code.

### <a name="set-up-the-web-application"></a>Webtoepassing instellen

Maak eerst een nieuwe map met de naam *Azure-blobs-java script* en open deze in VS code. Maak vervolgens een nieuw bestand in VS code, voeg de volgende HTML toe en sla deze op als *index. html* in de map *Azure-blobs-java script* .

```html
<!DOCTYPE html>
<html>

<body>
    <button id="create-container-button">Create container</button>
    <button id="delete-container-button">Delete container</button>
    <button id="select-button">Select and upload files</button>
    <input type="file" id="file-input" multiple style="display: none;" />
    <button id="list-button">List files</button>
    <button id="delete-button">Delete selected files</button>
    <p><b>Status:</b></p>
    <p id="status" style="height:160px; width: 593px; overflow: scroll;" />
    <p><b>Files:</b></p>
    <select id="file-list" multiple style="height:222px; width: 593px; overflow: scroll;" />
</body>

<!-- You'll add code here later in this quickstart. -->

</html>
```

### <a name="configure-the-debugger"></a>Het fout opsporingsprogramma configureren

Als u de uitbrei ding van de fout opsporing in VS code wilt instellen, selecteert u **fout opsporing > configuratie toevoegen...** en selecteert u vervolgens **Chrome** of **Edge**, afhankelijk van de extensie die u eerder hebt geïnstalleerd in de sectie vereisten. Met deze actie wordt een bestand *Launch. json* gemaakt en geopend in de editor.

Wijzig vervolgens het bestand *Launch. json* zodat de `url` waarde `/index.html` bevat zoals wordt weer gegeven:

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:8080/index.html",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```

Deze configuratie vertelt versus code welke browser moet worden gestart en welke URL moet worden geladen.

### <a name="launch-the-web-server"></a>De webserver starten

Als u de lokale node. js-webserver wilt starten, selecteert u **> Terminal weer geven** om een console venster in VS code te openen en voert u de volgende opdracht in.

```console
npx http-server
```

Met deze opdracht wordt het *http-server* pakket geïnstalleerd en wordt de server gestart, waardoor de huidige map beschikbaar is via standaard-url's, met inbegrip van de naam die in de vorige stap is aangegeven.

### <a name="start-debugging"></a>Fout opsporing starten

Als u *index. html* in de browser wilt starten terwijl het fout opsporingsprogramma VS code is gekoppeld, selecteert u **fout opsporing > fout opsporing starten** of drukt u op F5 in VS code.

De weer gegeven gebruikers interface doet nog niets, maar u voegt java script-code toe in de volgende sectie om elke functie te implementeren die wordt weer gegeven. U kunt vervolgens onderbrekings punten instellen en met het fout opsporingsprogramma communiceren wanneer het is onderbroken voor uw code.

Wanneer u wijzigingen aanbrengt in *index. html*, moet u de pagina opnieuw laden om de wijzigingen in de browser weer te geven. In VS code kunt u ook **fout opsporing selecteren > fout opsporing opnieuw opstarten** of druk op CTRL + SHIFT + F5.

### <a name="add-the-blob-storage-client-library"></a>De client bibliotheek voor de Blob-opslag toevoegen

Als u aanroepen naar de Blob Storage API wilt inschakelen, downloadt u eerst [de Azure Storage SDK voor Java script-BLOB-client bibliotheek](https://aka.ms/downloadazurestoragejsblob), extraheert u de inhoud van de zip en plaatst u het bestand *Azure-Storage-blob. js* in de map *Azure-blobs-java script* .

Plak vervolgens de volgende HTML in *index. html* na de afsluit code van `</body>`, waarbij u de tijdelijke Opmerking vervangt.

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Deze code voegt een verwijzing toe naar het script bestand en biedt een plaats voor uw eigen Java script-code. Voor de toepassing van deze Snelstartgids gebruiken we het script bestand *Azure-Storage-blob. js* , zodat u het kunt openen in VS code, de inhoud ervan leest en onderbrekings punten instelt. In productie moet u het bestand compact *Azure-Storage. blob. min. js* gebruiken dat ook in het zip-bestand is opgenomen.

U vindt meer informatie over elke Blob Storage-functie in de [referentie documentatie](https://docs.microsoft.com/javascript/api/%40azure/storage-blob/index). Sommige van de functies in de SDK zijn alleen beschikbaar in node. js of alleen beschikbaar in de browser.

De code in *Azure-Storage-blob. js* exporteert een globale variabele met de naam `azblob`, die u in uw Java script-code gebruikt om toegang te krijgen tot de Blob Storage-api's.

### <a name="add-the-initial-javascript-code"></a>De eerste Java script-code toevoegen

Plak vervolgens de volgende code in het `<script>`-element dat in het vorige code blok wordt weer gegeven en vervang de tijdelijke aanduiding voor de opmerking.

```javascript
const createContainerButton = document.getElementById("create-container-button");
const deleteContainerButton = document.getElementById("delete-container-button");
const selectButton = document.getElementById("select-button");
const fileInput = document.getElementById("file-input");
const listButton = document.getElementById("list-button");
const deleteButton = document.getElementById("delete-button");
const status = document.getElementById("status");
const fileList = document.getElementById("file-list");

const reportStatus = message => {
    status.innerHTML += `${message}<br/>`;
    status.scrollTop = status.scrollHeight;
}
```

Deze code maakt velden voor elk HTML-element dat door de volgende code wordt gebruikt en implementeert een `reportStatus`-functie om uitvoer weer te geven.

Voeg in de volgende secties elk nieuw blok van Java script-code toe na het vorige blok.

### <a name="add-your-storage-account-info"></a>De gegevens van uw opslag account toevoegen

Voeg vervolgens code toe om toegang te krijgen tot uw opslag account, waarbij u de tijdelijke aanduidingen vervangt door de naam van uw account en de SAS die u in een vorige stap hebt gegenereerd.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Deze code maakt gebruik van uw account gegevens en SAS om een [ContainerURL](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL) -exemplaar te maken. Dit is handig voor het maken en bewerken van een opslag container.

### <a name="create-and-delete-a-storage-container"></a>Een opslag container maken en verwijderen

Voeg vervolgens code toe om de opslag container te maken en te verwijderen wanneer u op de bijbehorende knop klikt.

```javascript
const createContainer = async () => {
    try {
        reportStatus(`Creating container "${containerName}"...`);
        await containerURL.create(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

const deleteContainer = async () => {
    try {
        reportStatus(`Deleting container "${containerName}"...`);
        await containerURL.delete(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

createContainerButton.addEventListener("click", createContainer);
deleteContainerButton.addEventListener("click", deleteContainer);
```

Deze code roept de ContainerURL [maken](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) en [verwijderen](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) aan zonder een [Afbrekings](https://docs.microsoft.com/javascript/api/@azure/storage-blob/aborter) exemplaar te gebruiken. Voor deze Quick Start wordt ervan uitgegaan dat uw opslag account is gemaakt en is ingeschakeld. Gebruik in productie code een Afbrekings instantie om een time-outfunctie toe te voegen.

### <a name="list-blobs"></a>Blobs vermelden

Voeg vervolgens code toe om de inhoud van de opslag container weer te geven wanneer u op de knop **bestanden weer geven** klikt.

```javascript
const listFiles = async () => {
    fileList.size = 0;
    fileList.innerHTML = "";
    try {
        reportStatus("Retrieving file list...");
        let marker = undefined;
        do {
            const listBlobsResponse = await containerURL.listBlobFlatSegment(
                azblob.Aborter.none, marker);
            marker = listBlobsResponse.nextMarker;
            const items = listBlobsResponse.segment.blobItems;
            for (const blob of items) {
                fileList.size += 1;
                fileList.innerHTML += `<option>${blob.name}</option>`;
            }
        } while (marker);
        if (fileList.size > 0) {
            reportStatus("Done.");
        } else {
            reportStatus("The container does not contain any files.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

listButton.addEventListener("click", listFiles);
```

Met deze code wordt de functie [ContainerURL. listBlobFlatSegment](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) in een lus aangeroepen om ervoor te zorgen dat alle segmenten worden opgehaald. Voor elk segment wordt een lus op de lijst met Blob-items weer geven en wordt de lijst met **bestanden** bijgewerkt.

### <a name="upload-blobs"></a>Blobs uploaden

Voeg vervolgens code toe om bestanden te uploaden naar de opslag container wanneer u op de knop **bestanden selecteren en uploaden** klikt.

```javascript
const uploadFiles = async () => {
    try {
        reportStatus("Uploading files...");
        const promises = [];
        for (const file of fileInput.files) {
            const blockBlobURL = azblob.BlockBlobURL.fromContainerURL(containerURL, file.name);
            promises.push(azblob.uploadBrowserDataToBlockBlob(
                azblob.Aborter.none, file, blockBlobURL));
        }
        await Promise.all(promises);
        reportStatus("Done.");
        listFiles();
    } catch (error) {
        reportStatus(error.body.message);
    }
}

selectButton.addEventListener("click", () => fileInput.click());
fileInput.addEventListener("change", uploadFiles);
```

Met deze code wordt de knop **bestanden selecteren en uploaden** verbonden met het verborgen `file-input` element. Op deze manier wordt met de gebeurtenis knop `click` de gebeurtenis file input `click` geactiveerd en wordt de bestands kiezer weer gegeven. Nadat u bestanden hebt geselecteerd en het dialoog venster hebt gesloten, treedt de gebeurtenis `input` op en wordt de functie `uploadFiles` aangeroepen. Deze functie roept de alleen- [uploadBrowserDataToBlockBlob](https://docs.microsoft.com/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) functie voor elk bestand dat u hebt geselecteerd. Elke aanroep retourneert een Promise, die wordt toegevoegd aan een lijst, zodat ze allemaal tegelijk kunnen worden gewacht, waardoor de bestanden parallel worden geüpload.

### <a name="delete-blobs"></a>Blobs verwijderen

Voeg vervolgens code toe om bestanden uit de opslag container te verwijderen wanneer u op de knop **geselecteerde bestanden verwijderen** klikt.

```javascript
const deleteFiles = async () => {
    try {
        if (fileList.selectedOptions.length > 0) {
            reportStatus("Deleting files...");
            for (const option of fileList.selectedOptions) {
                const blobURL = azblob.BlobURL.fromContainerURL(containerURL, option.text);
                await blobURL.delete(azblob.Aborter.none);
            }
            reportStatus("Done.");
            listFiles();
        } else {
            reportStatus("No files selected.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

deleteButton.addEventListener("click", deleteFiles);
```

Met deze code wordt de functie [BlobURL. Delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) aangeroepen om elk bestand te verwijderen dat in de lijst is geselecteerd. Vervolgens wordt de functie `listFiles` aangeroepen die eerder is weer gegeven om de inhoud van de **bestanden** lijst te vernieuwen.

### <a name="run-and-test-the-web-application"></a>De webtoepassing uitvoeren en testen

Op dit moment kunt u de pagina starten en experimenteren om te zien hoe Blob Storage werkt. Als er fouten optreden (bijvoorbeeld wanneer u bestanden probeert weer te geven voordat u de container hebt gemaakt), wordt in het deel venster **status** het fout bericht weer gegeven dat is ontvangen. U kunt ook onderbrekings punten instellen in de Java script-code om de waarden te controleren die worden geretourneerd door de opslag-Api's.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die tijdens deze Quick start zijn gemaakt, wilt opschonen, gaat u naar de [Azure Portal](https://portal.azure.com) en verwijdert u de resource groep die u hebt gemaakt in de sectie vereisten.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een eenvoudige website gemaakt die toegang heeft tot Blob-opslag vanuit een browser-gebaseerde java script. Ga verder met de volgende zelf studie als u wilt weten hoe u een website zelf kunt hosten in Blob Storage:

> [!div class="nextstepaction"]
> [Een statische website op Blob Storage hosten](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)
