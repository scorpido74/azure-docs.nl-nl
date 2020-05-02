---
title: 'Snelstartgids: Azure Blob-opslag bibliotheek V12-java script in een browser'
description: In deze Quick Start leert u hoe u de Azure Blob Storage-client bibliotheek versie 12 voor Java script in een browser kunt gebruiken. U maakt een container en een object in Blob Storage. Vervolgens leert u hoe u alle blobs in een container kunt weer geven. Tot slot leert u hoe u blobs verwijdert en een container verwijdert.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/18/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 3b23eac4ab50401e68e17064d0964dacc3c17d32
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82120504"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-a-browser"></a>Quick Start: blobs beheren met Java script V12 SDK in een browser

Azure Blob-opslag is geoptimaliseerd voor het opslaan van grote hoeveel heden ongestructureerde gegevens. Blobs zijn objecten die tekst of binaire gegevens kunnen bevatten, zoals afbeeldingen, documenten, streaming media en gegevens archivering. In deze Quick Start leert u hoe u blobs beheert met behulp van Java script in een browser. U kunt blobs uploaden en weer geven en u kunt containers maken en verwijderen.

[API reference documentation](/javascript/api/@azure/storage-blob) | Voor[beelden](https://docs.microsoft.com/azure/storage/common/storage-samples-javascript?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#blob-samples) van de API-referentie[bibliotheek broncode](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [pakket (NPM)](https://www.npmjs.com/package/@azure/storage-blob) | 

> [!NOTE]
> Zie [Quick Start: blobs beheren met Java script V10 toevoegen SDK in node. js](storage-quickstart-blobs-nodejs-legacy.md)om aan de slag te gaan met de vorige SDK-versie.

## <a name="prerequisites"></a>Vereisten

* [Een Azure-account met een actief abonnement](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* [Een Azure Storage-account](../common/storage-account-create.md)
* [Node.js](https://nodejs.org)
* [Micro soft Visual Studio code](https://code.visualstudio.com)
* Een Visual Studio code-extensie voor fout opsporing in een browser, zoals:
    * [Fout opsporing voor micro soft Edge](vscode:extension/msjsdiag.debugger-for-edge)
    * [Fout opsporing voor Chrome](vscode:extension/msjsdiag.debugger-for-chrome)
    * [Fout opsporing voor Firefox](vscode:extension/firefox-devtools.vscode-firefox-debug)


[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="object-model"></a>Object model

Er zijn drie typen resources voor blobopslag:

* Het opslag account
* Een container in het opslagaccount
* Een BLOB in de container

Het volgende diagram geeft de relatie tussen deze resources weer.

![Diagram van de blobopslagarchitectuur](./media/storage-blobs-introduction/blob1.png)

In deze Quick Start gebruikt u de volgende Java script-klassen om te communiceren met deze resources:

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): met `BlobServiceClient` de klasse kunt u Azure storage resources en BLOB-containers bewerken.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): met `ContainerClient` de klasse kunt u Azure Storage containers en de bijbehorende blobs bewerken.
* [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient): met `BlockBlobClient` de klasse kunt u Azure Storage blobs bewerken.

## <a name="setting-up"></a>Instellen

In deze sectie wordt uitgelegd hoe u een project voorbereidt voor gebruik met de Azure Blob Storage-client bibliotheek V12 voor Java script.

### <a name="create-a-cors-rule"></a>Een CORS-regel maken

Voordat uw webtoepassing toegang kan krijgen tot de Blob-opslag van de client, moet u uw account configureren om [Cross-Origin-resource delen](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services)of CORS in te scha kelen.

Selecteer uw opslag account in de Azure Portal. Als u een nieuwe CORS-regel wilt definiëren, gaat u naar de sectie **instellingen** en selecteert u **CORS**. Voor deze snelstart maakt u een open CORS-regel:

![CORS-instellingen in Azure Blob Storage-account](media/quickstart-blobs-javascript-browser/azure-blob-storage-cors-settings.png)

In de volgende tabel worden alle CORS-instellingen beschreven en de waarden voor het definiëren van de regel uitgelegd.

|Instelling  |Waarde  | Beschrijving |
|---------|---------|---------|
| **TOEGESTANE OORSPRONGEN** | **\*** | Accepteert een door komma's gescheiden lijst met domeinen die als acceptabele oorsprongen zijn ingesteld. Als de waarde wordt ingesteld op `*`, hebben alle domeinen toegang tot het opslagaccount. |
| **TOEGESTANE METHODEN** | **Verwijderen**, **ophalen**, **hoofd**, **samen voegen**, **plaatsen**, **Opties**en **plaatsen** | Vermeldt de HTTP-bewerkingen die zijn toegestaan om voor het opslagaccount te worden uitgevoerd. Voor deze snelstart selecteert u alle beschikbare opties. |
| **TOEGESTANE HEADERS** | **\*** | Definieert een lijst met aanvraagheaders (inclusief headers met een voorvoegsel) die door het opslagaccount zijn toegestaan. Als de waarde wordt ingesteld op `*`, hebben alle headers toegang. |
| **WEER GEGEVEN KOPTEKSTEN** | **\*** | Vermeldt de door het account toegestane antwoordheaders. Als de waarde wordt ingesteld op `*`, kan het account elke header verzenden. |
| **MAXIMALE LEEFTIJD** | **86400** | De maximale tijd dat de browser de Preflight-OPTIONS-aanvraag in een paar seconden in de cache opslaat. De waarde *86400* betekent dat de cache een hele dag in stand blijft. |

Nadat u de velden met de waarden uit deze tabel hebt ingevuld, klikt u op de knop **Opslaan** .

> [!IMPORTANT]
> Zorg ervoor dat alle instellingen die u in productie gebruikt, de minimale benodigde toegangs rechten voor uw opslag account beschikbaar maakt om beveiligde toegang te behouden. De hier beschreven CORS-instellingen zijn geschikt voor een snelstart, omdat ze een minder streng beveiligingsbeleid definiëren. Deze instellingen worden echter niet aangeraden in reële situaties.

### <a name="create-a-shared-access-signature"></a>Een Shared Access Signature maken

De Shared Access Signature (SAS) wordt gebruikt door code die in de browser wordt uitgevoerd om aanvragen voor Azure Blob-opslag te autoriseren. Als u een SAS gebruikt, kan de client toegang tot de opslagresources autoriseren zonder de toegangssleutel of verbindingsreeks van het account. Zie [Handtekeningen voor gedeelde toegang gebruiken](../common/storage-sas-overview.md) voor meer informatie over SAS.

Volg deze stappen om de Blob service SAS-URL op te halen:

1. Selecteer uw opslag account in de Azure Portal.
2. Navigeer naar het gedeelte **instellingen** en selecteer **hand tekening voor gedeelde toegang**.
3. Schuif omlaag en klik op de knop **SAS-en Connection String genereren** .
4. Schuif verder omlaag en zoek het veld **BLOB service SAS-URL** op.
5. Klik op de knop **kopiëren naar klem bord** aan de rechter kant van het veld **BLOB service SAS-URL** .
6. Sla de gekopieerde URL ergens op voor gebruik in een volgende stap.

### <a name="add-the-azure-blob-storage-client-library"></a>De client bibliotheek voor Azure Blob Storage toevoegen

Maak op uw lokale computer een nieuwe map met de naam *Azure-blobs-js-browser* en open deze in Visual Studio code.

Selecteer **> Terminal weer geven** om een console venster te openen in Visual Studio code. Voer de volgende node. js pakket Manager (NPM) opdracht uit in het Terminal venster om een [package. json](https://docs.npmjs.com/files/package.json) -bestand te maken.

```console
npm init -y
```

De Azure SDK bestaat uit een groot aantal afzonderlijke pakketten. U kunt kiezen welke pakketten u nodig hebt op basis van de services die u wilt gebruiken. Voer de `npm` volgende opdracht uit in het Terminal venster om `@azure/storage-blob` het pakket te installeren.

```console
npm install --save @azure/storage-blob
```

#### <a name="bundle-the-azure-blob-storage-client-library"></a>De Azure Blob Storage-client bibliotheek bundelen

Als u Azure SDK-bibliotheken op een website wilt gebruiken, zet u de code om in de browser te werken. U kunt dit doen met een hulp programma dat een bundel wordt genoemd. Bundeling maakt Java script-code geschreven met behulp van [node. js](https://nodejs.org) -conventies en converteert deze naar een indeling die door browsers wordt begrepen. In dit Quick Start-artikel wordt gebruikgemaakt van de [pakket](https://parceljs.org/) bundel.

Installeer Parcel door de volgende `npm` opdracht uit te voeren in het Terminal venster:

```console
npm install -g parcel-bundler
```

Open in Visual Studio code het bestand *package. json* en voeg een `browserlist` tussen de `license` en `dependencies` toe. Dit `browserlist` streeft naar de nieuwste versie van drie populaire browsers. Het volledige bestand *package. json* ziet er nu als volgt uit:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/package.json" highlight="12-16":::

Sla het bestand *package. json* op.

### <a name="import-the-azure-blob-storage-client-library"></a>De Azure Blob Storage-client bibliotheek importeren

Als u Azure SDK-bibliotheken wilt gebruiken in Java `@azure/storage-blob` script, importeert u het pakket. Maak een nieuw bestand in Visual Studio code met de volgende Java script-code.

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ImportLibrary":::

Sla het bestand op als *index. js* in de map *Azure-blobs-js-browser* .

### <a name="implement-the-html-page"></a>HTML-pagina implementeren

Maak een nieuw bestand in Visual Studio code en voeg de volgende HTML-code toe.

:::code language="html" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.html":::

Sla het bestand op als *index. html* in de map *Azure-blobs-js-browser* .

## <a name="code-examples"></a>Codevoorbeelden

De voorbeeld code laat zien hoe u de volgende taken kunt uitvoeren met de Azure Blob Storage-client bibliotheek voor Java script:

* [Velden declareren voor UI-elementen](#declare-fields-for-ui-elements)
* [De gegevens van uw opslag account toevoegen](#add-your-storage-account-info)
* [Clientobjecten maken](#create-client-objects)
* [Een opslag container maken en verwijderen](#create-and-delete-a-storage-container)
* [Blobs vermelden](#list-blobs)
* [Blobs uploaden](#upload-blobs)
* [Blobs verwijderen](#delete-blobs)

U voert de code uit nadat u alle fragmenten aan het bestand *index. js* hebt toegevoegd.

### <a name="declare-fields-for-ui-elements"></a>Velden declareren voor UI-elementen

Voeg de volgende code toe aan het einde van het bestand *index. js* .

:::code language="JavaScript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeclareVariables":::

Sla het bestand *index. js* op.

Deze code declareert velden voor elk HTML-element en implementeert `reportStatus` een functie voor het weer geven van uitvoer.

Voeg in de volgende secties elk nieuw blok van Java script-code toe na het vorige blok.

### <a name="add-your-storage-account-info"></a>De gegevens van uw opslag account toevoegen

Voeg code toe om toegang te krijgen tot uw opslag account. Vervang de tijdelijke aanduiding door uw Blob service SAS-URL die u eerder hebt gegenereerd. Voeg de volgende code toe aan het einde van het bestand *index. js* .

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_StorageAcctInfo":::

Sla het bestand *index. js* op.

### <a name="create-client-objects"></a>Clientobjecten maken

Maak [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) -en [ContainerClient](/javascript/api/@azure/storage-blob/containerclient) -objecten voor interactie met de Azure Blob Storage-service. Voeg de volgende code toe aan het einde van het bestand *index. js* .

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateClientObjects":::

Sla het bestand *index. js* op.

### <a name="create-and-delete-a-storage-container"></a>Een opslag container maken en verwijderen

Maak en verwijder de opslag container wanneer u op de bijbehorende knop op de webpagina klikt. Voeg de volgende code toe aan het einde van het bestand *index. js* .

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_CreateDeleteContainer":::

Sla het bestand *index. js* op.

### <a name="list-blobs"></a>Blobs vermelden

Geef de inhoud van de opslag container weer wanneer u op de knop **bestanden weer geven** klikt. Voeg de volgende code toe aan het einde van het bestand *index. js* .

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_ListBlobs":::

Sla het bestand *index. js* op.

Met deze code wordt de functie [ContainerClient. listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) aangeroepen, waarna een iterator wordt gebruikt om de naam op te halen van elke geretourneerde [BlobItem](/javascript/api/@azure/storage-blob/blobitem) . Voor elke `BlobItem`wordt de lijst met **bestanden** bijgewerkt met de waarde van de eigenschap [name](/javascript/api/@azure/storage-blob/blobitem#name) .

### <a name="upload-blobs"></a>Blobs uploaden

Upload bestanden naar de opslag container wanneer u op de knop **bestanden selecteren en uploaden** klikt. Voeg de volgende code toe aan het einde van het bestand *index. js* .

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_UploadBlobs":::

Sla het bestand *index. js* op.

Met deze code wordt de knop **bestanden selecteren en uploaden** verbonden met `file-input` het verborgen element. Met de `click` knop gebeurtenis wordt de gebeurtenis `click` bestands invoer geactiveerd en wordt de bestands kiezer weer gegeven. Nadat u bestanden hebt geselecteerd en het dialoog venster hebt gesloten `input` , treedt de gebeurtenis `uploadFiles` op en wordt de functie aangeroepen. Deze functie maakt een [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) -object en roept vervolgens de [uploadBrowserData](/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) -functie aan voor elk bestand dat u hebt geselecteerd. Elke aanroep retourneert een `Promise`. Elk `Promise` wordt toegevoegd aan een lijst, zodat ze allemaal tegelijk kunnen worden gewacht, waardoor de bestanden parallel worden geüpload.

### <a name="delete-blobs"></a>Blobs verwijderen

Verwijder bestanden uit de opslag container wanneer u klikt op de knop **geselecteerde bestanden verwijderen** . Voeg de volgende code toe aan het einde van het bestand *index. js* .

:::code language="javascript" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/index.js" id="snippet_DeleteBlobs":::

Sla het bestand *index. js* op.

Met deze code wordt de functie [ContainerClient. deleteBlob](/javascript/api/@azure/storage-blob/containerclient#deleteblob-string--blobdeleteoptions-) aangeroepen om elk bestand te verwijderen dat in de lijst is geselecteerd. Vervolgens wordt de `listFiles` functie aangeroepen die eerder wordt weer gegeven om de inhoud van de **bestanden** lijst te vernieuwen.

## <a name="run-the-code"></a>De code uitvoeren

Als u de code wilt uitvoeren in de Visual Studio code debugger, configureert u het bestand *Launch. json* voor uw browser.

### <a name="configure-the-debugger"></a>Het fout opsporingsprogramma configureren

De uitbrei ding van fout opsporing in Visual Studio code instellen:

1. Selecteer **uitvoeren > configuratie toevoegen**
2. Selecteer **Edge**, **Chrome**of **Firefox**, afhankelijk van de extensie die u in de sectie [vereisten](#prerequisites) hebt geïnstalleerd.

Bij het toevoegen van een nieuwe configuratie wordt een bestand *Launch. json* gemaakt en geopend in de editor. Wijzig het bestand *Launch. json* zodat de `url` waarde is `http://localhost:1234/index.html`, zoals hier wordt weer gegeven:

:::code language="json" source="~/azure-storage-snippets/blobs/quickstarts/JavaScript/V12/azure-blobs-js-browser/.vscode/launch.json" highlight="11":::

Sla het bestand *Launch. json* na het bijwerken op. Deze configuratie vertelt Visual Studio code die browser moet openen en welke URL moet worden geladen.

### <a name="launch-the-web-server"></a>De webserver starten

Als u de webserver voor lokale ontwikkeling wilt starten, selecteert u **> Terminal weer geven** om een console venster te openen in Visual Studio code en voert u de volgende opdracht in.

```console
parcel index.html
```

Parcel bundelt uw code en start een lokale ontwikkel server voor uw pagina op `http://localhost:1234/index.html`. Wijzigingen die u aanbrengt in *index. js* worden automatisch op de ontwikkelings server gebouwd en gereflecteerd wanneer u het bestand opslaat.

Als u een bericht ontvangt met de melding dat de **geconfigureerde poort 1234 niet kan worden gebruikt**, kunt u de poort wijzigen `parcel -p <port#> index.html`door de opdracht uit te voeren. Werk in het bestand *Launch. json* de poort in het URL-pad bij naar overeenkomst.

### <a name="start-debugging"></a>Fout opsporing starten

Voer de pagina uit in het fout opsporingsprogramma en krijg een idee van de werking van Blob Storage. Als er fouten optreden, wordt het fout bericht weer gegeven in het **status** venster op de webpagina.

Als u *index. html* in de browser wilt openen terwijl het Visual Studio code-fout opsporingsprogramma is gekoppeld, selecteert u **uitvoeren > fout opsporing starten** of drukt u op F5 in Visual Studio code.

### <a name="use-the-web-app"></a>De web-app gebruiken

In de [Azure Portal](https://portal.azure.com)kunt u de resultaten van de API-aanroepen controleren wanneer u de volgende stappen uitvoert.

#### <a name="step-1---create-a-container"></a>Stap 1: een container maken

1. Selecteer in de Web-App de optie **container maken**. De status geeft aan dat een container is gemaakt.
2. Als u de Azure Portal wilt controleren, selecteert u uw opslag account. Selecteer onder **BLOB service** **containers**. Controleer of de nieuwe container wordt weer gegeven. (Mogelijk moet u **vernieuwen**selecteren.)

#### <a name="step-2---upload-a-blob-to-the-container"></a>Stap 2: een BLOB uploaden naar de container

1. Maak een test bestand, zoals *test. txt*, op uw lokale computer en sla het op.
2. Klik in de web-app op **bestanden selecteren en uploaden**.
3. Blader naar het test bestand en selecteer **openen**. De status geeft aan dat het bestand is geüpload en dat de lijst met bestanden is opgehaald.
4. Selecteer in de Azure Portal de naam van de nieuwe container die u eerder hebt gemaakt. Controleer of het test bestand wordt weer gegeven.

#### <a name="step-3---delete-the-blob"></a>Stap 3: de BLOB verwijderen

1. Selecteer in de web-app, onder **bestanden**, het test bestand.
2. Selecteer **geselecteerde bestanden verwijderen**. De status geeft aan dat het bestand is verwijderd en dat de container geen bestanden bevat.
3. Selecteer **vernieuwen**In het Azure Portal. Controleer of **er geen blobs zijn gevonden**.

#### <a name="step-4---delete-the-container"></a>Stap 4: de container verwijderen

1. Selecteer **container verwijderen**in de web-app. De status geeft aan dat de container is verwijderd.
2. Selecteer in de Azure Portal de ** \<account naam\> | **De koppeling containers in de linkerbovenhoek van het deel venster Portal.
3. Selecteer **vernieuwen**. De nieuwe container verdwijnt.
4. Sluit de web-app.

### <a name="clean-up-resources"></a>Resources opschonen

Klik in Visual Studio code op de **Terminal** -console en druk op CTRL + C om de webserver te stoppen.

Als u de resources die tijdens deze Quick start zijn gemaakt, wilt opschonen, gaat u naar de [Azure Portal](https://portal.azure.com) en verwijdert u de resource groep die u hebt gemaakt in de sectie [vereisten](#prerequisites) .

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u blobs kunt uploaden, weer geven en verwijderen met behulp van Java script. U hebt ook geleerd hoe u een BLOB storage-container maakt en verwijdert.

Voor zelf studies, voor beelden, Quick starts en andere documentatie gaat u naar:

> [!div class="nextstepaction"]
> [Documentatie voor Azure voor Java script](/azure/javascript/)

* Zie de [Azure Blob Storage-client bibliotheek voor Java script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob)voor meer informatie.
* Als u voor beeld-apps voor Blob-opslag wilt zien, gaat u naar [Azure Blob Storage-client bibliotheek V12 java script](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)-voor beelden.
