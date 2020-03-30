---
title: 'Snelstart: Azure Blob-opslag voor JavaScript v10 in de browser'
description: Lees blobs uploaden, aanbieden en verwijderen met JavaScript v10 SDK op een HTML-pagina.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.author: mhopkins
ms.date: 01/24/2020
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: da5db7d956b1ba8aa1ac245b77fe0d4cb31909c1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80061452"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-browser"></a>Snelstart: blobs beheren met JavaScript v10 SDK in browser

In deze quickstart leert u blobs beheren door JavaScript-code te gebruiken die volledig in de browser wordt uitgevoerd. Blobs zijn objecten die grote hoeveelheden tekst of binaire gegevens kunnen bevatten, waaronder afbeeldingen, documenten, streamingmedia en archiefgegevens. U gebruikt de vereiste beveiligingsmaatregelen om de beveiligde toegang tot uw blob-opslagaccount te garanderen.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- Een Azure Storage-account. [Een opslagaccount maken](../common/storage-account-create.md).
- Een lokale webserver. In dit artikel wordt [Node.js](https://nodejs.org) gebruikt om een basisserver te openen.
- [Visual Studio Code](https://code.visualstudio.com).
- Een VS-code-extensie voor foutopsporing in de browser, zoals [Foutopsporing voor Chrome](vscode:extension/msjsdiag.debugger-for-chrome) of [Foutopsporing voor Microsoft Edge.](vscode:extension/msjsdiag.debugger-for-edge)

## <a name="setting-up-storage-account-cors-rules"></a>CORS-regels voor het opslagaccount instellen

Voordat uw webtoepassing toegang heeft tot een blob-opslag van de client, moet u uw account configureren om het delen van bronnen van [cross-origine](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)of CORS in te schakelen.

Ga terug naar Azure Portal en selecteer uw opslagaccount. Als u een nieuwe CORS-regel wilt definiëren, navigeert u naar de sectie **Instellingen** en klikt u op de **CORS-koppeling.** Klik vervolgens op de knop **Toevoegen** om het venster **CORS-regel toevoegen** te openen. Voor deze snelstart maakt u een open CORS-regel:

![CORS-instellingen in Azure Blob Storage-account](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

In de volgende tabel worden alle CORS-instellingen beschreven en de waarden voor het definiëren van de regel uitgelegd.

|Instelling  |Waarde  | Beschrijving |
|---------|---------|---------|
| Toegestane oorsprongen | * | Accepteert een door komma's gescheiden lijst met domeinen die als acceptabele oorsprongen zijn ingesteld. Als de waarde wordt ingesteld op `*`, hebben alle domeinen toegang tot het opslagaccount. |
| Toegestane methoden     | delete, get, head, merge, post, options en put | Vermeldt de HTTP-bewerkingen die zijn toegestaan om voor het opslagaccount te worden uitgevoerd. Voor deze snelstart selecteert u alle beschikbare opties. |
| Toegestane headers | * | Definieert een lijst met aanvraagheaders (inclusief headers met een voorvoegsel) die door het opslagaccount zijn toegestaan. Als de waarde wordt ingesteld op `*`, hebben alle headers toegang. |
| Weergegeven headers | * | Vermeldt de door het account toegestane antwoordheaders. Als de waarde wordt ingesteld op `*`, kan het account elke header verzenden.  |
| Maximale leeftijd (seconden) | 86400 | De maximale periode dat de browser de voorbereidende aanvraag OPTIONS in de cache opslaat. De waarde *86400* betekent dat de cache een hele dag in stand blijft. |

> [!IMPORTANT]
> Zorg ervoor dat instellingen die u in de productie gebruikt, de minimale hoeveelheid toegang die nodig is voor uw opslagaccount blootstellen om veilige toegang te behouden. De hier beschreven CORS-instellingen zijn geschikt voor een snelstart, omdat ze een minder streng beveiligingsbeleid definiëren. Deze instellingen worden echter niet aangeraden in reële situaties.

Vervolgens gebruikt u Azure Cloud Shell om een beveiligingstoken te maken.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>Een handtekening voor gedeelde toegang maken

Shared Access Signature (handtekening voor gedeelde toegang, SAS) wordt gebruikt door de code die wordt uitgevoerd in de browser om aanvragen bij de blob-opslag te verifiëren. Als u een SAS gebruikt, kan de client toegang tot de opslagresources autoriseren zonder de toegangssleutel of verbindingsreeks van het account. Zie [Handtekeningen voor gedeelde toegang gebruiken](../common/storage-sas-overview.md) voor meer informatie over SAS.

U een SAS maken met de Azure CLI via de Azure-cloudshell of met de Azure-portal of Azure Storage Explorer. In de volgende tabel worden de parameters beschreven waarvoor u waarden moet opgeven om een SAS met de CLI te genereren.

| Parameter      |Beschrijving  | Tijdelijke aanduiding |
|----------------|-------------|-------------|
| *Verstrijken*       | De vervaldatum van het toegangstoken in de notatie JJJJ-MM-DD. Voer voor deze snelstart de datum van morgen in. | *FUTURE_DATE* |
| *accountnaam* | De naam van het opslagaccount. Gebruik de naam die in een vorige stap is bewaard. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | De opslagaccountsleutel. Gebruik de sleutel die in een vorige stap is bewaard. | *YOUR_STORAGE_ACCOUNT_KEY* |

Gebruik de volgende opdracht CLI, met werkelijke waarden voor elke tijdelijke aanduiding, om een SAS te genereren die u gebruiken in uw JavaScript-code.

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
| *Machtigingen*    | racwdl  | Deze SAS staat de mogelijkheden *read*, *append*, *create*, *write*, *delete* en *list* toe. |
| *resource-types* | sco     | De resources die waar de SAS invloed op heeft, zijn *service*, *container* en *object*. |
| *Diensten*       | b       | De service waar de SAS invloed op heeft is de *blob*-service. |

Nu de SAS is gegenereerd, kopieer t u de retourwaarde en slaat u deze ergens op voor gebruik in een komende stap. Als u uw SAS hebt gegenereerd met een andere methode dan `?` de Azure CLI, moet u de initiaal verwijderen als deze aanwezig is. Dit teken is een URL-scheidingsteken die later in dit onderwerp al in de URL-sjabloon staat waarin de SAS wordt gebruikt.

> [!IMPORTANT]
> Geef in productie SAS-tokens altijd door met behulp van SSL. SAS-tokens moeten op de server worden gegenereerd en naar de HTML-pagina verzonden om ze opnieuw door te geven aan Azure Blob Storage. U kunt eventueel een serverloze functie gebruiken om SAS-tokens te genereren. Azure Portal bevat functiesjablonen waarmee een SAS kan worden gemaakt met behulp van een JavaScript-functie.

## <a name="implement-the-html-page"></a>HTML-pagina implementeren

In deze sectie maakt u een basiswebpagina en configureert u VS-code om de pagina te starten en te debuggen. Voordat u echter starten, moet u Node.js gebruiken om een lokale webserver te starten en de pagina te serveren wanneer uw browser daarom vraagt. Vervolgens voegt u JavaScript-code toe om verschillende BLOB-opslag-API's aan te roepen en de resultaten op de pagina weer te geven. U ook de resultaten van deze oproepen bekijken in de [Azure-portal,](https://portal.azure.com) [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer)en de Azure [Storage-extensie](vscode:extension/ms-azuretools.vscode-azurestorage) voor VS-code.

### <a name="set-up-the-web-application"></a>Webtoepassing instellen

Maak eerst een nieuwe map met de naam *azure-blobs-javascript* en open deze in VS-code. Maak vervolgens een nieuw bestand in VS-code, voeg de volgende HTML toe en sla het op als *index.html* in de map *azure-blobs-javascript.*

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

### <a name="configure-the-debugger"></a>De foutopsporing configureren

Als u de foutopsporingsextensie wilt instellen in VS-code, selecteert u **Foutopsporing > Configuratie toevoegen...** en selecteert u **vervolgens Chrome** of **Edge,** afhankelijk van de extensie die u eerder in de sectie Vereisten hebt geïnstalleerd. Met deze actie wordt een *launch.json-bestand* aangenomen en wordt het in de editor geopend.

Wijzig vervolgens het *bestand launch.json,* zodat de `url` waarde wordt weergegeven `/index.html` zoals weergegeven:

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

Deze configuratie vertelt VS Code welke browser te starten en welke URL te laden.

### <a name="launch-the-web-server"></a>De webserver starten

Als u de lokale Node.js-webserver wilt starten, selecteert u **> Terminal weergeven** om een consolevenster in VS-code te openen en voert u de volgende opdracht in.

```console
npx http-server
```

Met deze opdracht wordt het *http-serverpakket* geïnstalleerd en wordt de server gestart, waardoor de huidige map beschikbaar is via standaard-URL's, inclusief de map die in de vorige stap is aangegeven.

### <a name="start-debugging"></a>Foutopsporing starten

Als u *index.html* in de browser wilt starten met de FOUTopsporing vs-code eraan gekoppeld, selecteert u **Foutopsporing > Foutopsporing starten** of drukt u op F5 in VS-code.

De weergegeven gebruikersinterface doet nog niets, maar u voegt JavaScript-code toe in de volgende sectie om elke weergegeven functie te implementeren. U vervolgens breekpunten instellen en communiceren met de foutopsporing wanneer deze wordt onderbroken op uw code.

Wanneer u wijzigingen aanbrengt in *index.html,* moet u de pagina opnieuw laden om de wijzigingen in de browser te zien. In VS-code u ook **Foutopsporing > Debugging opnieuw starten** of op Ctrl + Shift + F5 drukken.

### <a name="add-the-blob-storage-client-library"></a>De clientbibliotheek voor blobopslag toevoegen

Als u oproepen naar de blob-opslag-API wilt inschakelen, downloadt u eerst [de Azure Storage SDK voor JavaScript - Blob-clientbibliotheek,](https://aka.ms/downloadazurestoragejsblob)haalt u de inhoud van de zip en plaatst u het bestand *azure-storage-blob.js* in de map *azure-blobs-javascript.*

Plak vervolgens de volgende HTML in `</body>` *index.html* na de afsluitende tag, ter vervanging van de opmerking van de tijdelijke aanduiding.

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

Deze code voegt een verwijzing naar het scriptbestand toe en biedt een plaats voor uw eigen JavaScript-code. Voor deze quickstart gebruiken we het scriptbestand *azure-storage-blob.js,* zodat u het openen in VS-code, de inhoud ervan lezen en breekpunten instellen. In de productie moet u het compactere *azure-storage.blob.min.js-bestand* gebruiken dat ook in het zip-bestand wordt geleverd.

U meer informatie vinden over elke blob-opslagfunctie in de [referentiedocumentatie.](https://docs.microsoft.com/javascript/api/%40azure/storage-blob/index) Houd er rekening mee dat sommige functies in de SDK alleen beschikbaar zijn in Node.js of alleen beschikbaar zijn in de browser.

De code in *azure-storage-blob.js* exporteert een globale variabele genaamd `azblob`, die u in uw JavaScript-code gebruikt om toegang te krijgen tot de BLOB-opslag-API's.

### <a name="add-the-initial-javascript-code"></a>De eerste JavaScript-code toevoegen

Plak vervolgens de volgende `<script>` code in het element dat in het vorige codeblok wordt weergegeven, ter vervanging van de opmerking van de tijdelijke aanduiding.

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

Met deze code worden velden gemaakt voor elk HTML-element `reportStatus` dat de volgende code zal gebruiken en wordt een functie geïmplementeerd om de uitvoer weer te geven.

Voeg in de volgende secties elk nieuw blok JavaScript-code toe na het vorige blok.

### <a name="add-your-storage-account-info"></a>Uw opslagaccountgegevens toevoegen

Voeg vervolgens code toe om toegang te krijgen tot uw opslagaccount, waarbij u de tijdelijke aanduidingen vervangt door uw accountnaam en de SAS die u in een vorige stap hebt gegenereerd.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

Deze code gebruikt uw accountgegevens en SAS om een [ContainerURL-exemplaar](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL) te maken, wat handig is voor het maken en manipuleren van een opslagcontainer.

### <a name="create-and-delete-a-storage-container"></a>Een opslagcontainer maken en verwijderen

Voeg vervolgens code toe om de opslagcontainer te maken en te verwijderen wanneer u op de bijbehorende knop drukt.

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

Met deze code worden de functies van ContainerURL [gemaakt](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-) en [verwijderd](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) zonder een instantie [Afbreken te](https://docs.microsoft.com/javascript/api/@azure/storage-blob/aborter) gebruiken. Om het eenvoudig te houden voor deze quickstart, gaat deze code ervan uit dat uw opslagaccount is gemaakt en is ingeschakeld. Gebruik in productiecode een instantie Aborter om time-outfunctionaliteit toe te voegen.

### <a name="list-blobs"></a>Blobs vermelden

Voeg vervolgens code toe om de inhoud van de opslagcontainer weer te geven wanneer u op de knop **Lijstbestanden** drukt.

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

Met deze code wordt de functie [ContainerURL.listBlobFlatSegment](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) in een lus aangesproken om ervoor te zorgen dat alle segmenten worden opgehaald. Voor elk segment wordt de lijst met blob-items herhaald en wordt de lijst **met bestanden** bijgewerkt.

### <a name="upload-blobs"></a>Blobs uploaden

Voeg vervolgens code toe om bestanden naar de opslagcontainer te uploaden wanneer u op de knop **Bestanden selecteren en uploaden.**

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

Deze code verbindt de knop Bestanden `file-input` selecteren en **uploaden** met het verborgen element. Op deze manier `click` activeert de `click` knopgebeurtenis de bestandsinvoergebeurtenis en wordt de bestandenkiezer weergegeven. Nadat u bestanden hebt geselecteerd en `input` het dialoogvenster `uploadFiles` hebt gesloten, vindt de gebeurtenis plaats en wordt de functie aangeroepen. Met deze functie wordt de functie browser-only [uploadBrowserDataToBlockBlob](https://docs.microsoft.com/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) aangebeld voor elk bestand dat u hebt geselecteerd. Elke oproep retourneert een Belofte, die wordt toegevoegd aan een lijst, zodat ze allemaal in één keer kunnen worden afgewacht, waardoor de bestanden parallel worden geüpload.

### <a name="delete-blobs"></a>Blobs verwijderen

Voeg vervolgens code toe om bestanden uit de opslagcontainer te verwijderen wanneer u op de knop **Geselecteerde bestanden verwijderen** drukt.

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

Met deze code wordt de functie [BlobURL.delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) aanroept om elk bestand dat in de lijst is geselecteerd, te verwijderen. Vervolgens wordt `listFiles` de eerder getoonde functie aangesproken om de inhoud van de lijst **Bestanden** te vernieuwen.

### <a name="run-and-test-the-web-application"></a>De webtoepassing uitvoeren en testen

Op dit punt u de pagina starten en experimenteren om een gevoel te krijgen voor hoe blobopslag werkt. Als er fouten optreden (bijvoorbeeld wanneer u bestanden probeert weer te geven voordat u de container hebt gemaakt), wordt in het deelvenster **Status** het ontvangen foutbericht weergegeven. U ook breekpunten instellen in de JavaScript-code om de waarden te onderzoeken die door de opslag-API's zijn geretourneerd.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de bronnen wilt opschonen die tijdens deze quickstart zijn gemaakt, gaat u naar de [Azure-portal](https://portal.azure.com) en verwijdert u de brongroep die u hebt gemaakt in de sectie Voorwaarden.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een eenvoudige website gemaakt die toegang heeft tot blob-opslag vanuit javascript in de browser. Ga naar de volgende zelfstudie voor meer informatie over hoe u een website zelf hosten in blob-opslag:

> [!div class="nextstepaction"]
> [Een statische website hosten op Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)
