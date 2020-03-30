---
title: Reageren op Azure Blob-opslaggebeurtenissen | Microsoft Documenten
description: Gebruik Azure Event Grid om u te abonneren op gebeurtenissen van Blob Storage.
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: 5281dab8fd42326d88964614fd20a81621b5e9dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268494"
---
# <a name="reacting-to-blob-storage-events"></a>Reageren op gebeurtenissen van Blob Storage

Azure Storage-gebeurtenissen stellen toepassingen in staat om te reageren op gebeurtenissen, zoals het maken en verwijderen van blobs. Het doet dit zonder de noodzaak van ingewikkelde code of dure en inefficiënte polling diensten.

Gebeurtenissen worden met [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) naar abonnees zoals Azure-functies, Azure Logic Apps of zelfs naar uw eigen http-listener gepusht. Het beste deel is dat je alleen betaalt voor wat je gebruikt.

Blob-opslag stuurt gebeurtenissen naar Event Grid, wat betrouwbare gebeurtenislevering biedt aan uw toepassingen via een uitgebreid beleid voor nieuwe apparaten en dode letters.

Veelvoorkomende scenario's voor blob-opslaggebeurtenissen omvatten beeld- of videoverwerking, zoekindexering of een bestandsgeoriënteerde werkstroom. Asynchrone bestandsuploads passen goed bij gebeurtenissen. Wanneer wijzigingen zeldzaam zijn, maar uw scenario onmiddellijke responsiviteit vereist, kan architectuur op basis van gebeurtenissen bijzonder efficiënt zijn.

Als u dit nu wilt uitproberen, raadpleegt u een van deze quickstart-artikelen:

|Als u deze tool wilt gebruiken:    |Zie dit artikel: |
|--|-|
|Azure Portal    |[Snelstart: Blob-opslaggebeurtenissen routeren naar webeindpunt met de Azure-portal](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Snelstart: opslaggebeurtenissen routeren naar webeindpunt met PowerShell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure-CLI    |[Snelstart: opslaggebeurtenissen routeren naar webeindpunt met Azure CLI](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Zie de volgende artikelen om diepgaande voorbeelden van reageren op Blob-opslaggebeurtenissen met Azure-functies weer te geven:

- [Zelfstudie: Gebruik Azure Data Lake Storage Gen2-gebeurtenissen om een Gegevensbricks Delta-tabel bij te werken.](data-lake-storage-events.md)
- [Zelfstudie: Het formaat van geüploade afbeeldingen wijzigen met gebeurtenisraster](https://docs.microsoft.com/azure/event-grid/resize-images-on-storage-blob-upload-event?tabs=dotnet)

>[!NOTE]
> Alleen opslagaccounts van soort **StorageV2 (v2 voor algemeen gebruik)** en **BlobStorage** ondersteunen gebeurtenis-integratie. **Opslag (genral ei v1)** biedt *geen* ondersteuning voor integratie met Event Grid.

## <a name="the-event-model"></a>Het gebeurtenismodel

Event Grid gebruikt [gebeurtenisabonnementen](../../event-grid/concepts.md#event-subscriptions) om gebeurtenisberichten naar abonnees te leiden. Deze afbeelding illustreert de relatie tussen gebeurtenis-uitgevers, gebeurtenisabonnementen en gebeurtenishandlers.

![Gebeurtenisrastermodel](./media/storage-blob-event-overview/event-grid-functional-model.png)

Abonneer u eerst een eindpunt op een gebeurtenis. Wanneer een gebeurtenis wordt geactiveerd, stuurt de gebeurtenisrasterservice vervolgens gegevens over die gebeurtenis naar het eindpunt.

Zie het [schema voor opslaggebeurtenissen van Blob](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) om weer te geven:

> [!div class="checklist"]
> * Een volledige lijst met Blob-opslaggebeurtenissen en hoe elke gebeurtenis wordt geactiveerd.
> * Een voorbeeld van de gegevens die het gebeurtenisraster voor elk van deze gebeurtenissen zou verzenden.
> * Het doel van elk sleutelwaardepaar dat in de gegevens wordt weergegeven.

## <a name="filtering-events"></a>Gebeurtenissen filteren

[Blob-gebeurtenissen kunnen worden gefilterd](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) op het gebeurtenistype, de containernaam of de naam van het object dat is gemaakt/verwijderd. Filters in gebeurtenisraster komen overeen met het begin of einde van het onderwerp, zodat gebeurtenissen met een overeenkomend onderwerp naar de abonnee gaan.

Zie [Gebeurtenissen filteren voor gebeurtenisraster](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)voor meer informatie over het toepassen van filters.

Het onderwerp blob-opslaggebeurtenissen gebruikt de indeling:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Als u alle gebeurtenissen voor een opslagaccount wilt matchen, u de onderwerpfilters leeg laten.

Als u gebeurtenissen wilt matchen van blobs die zijn `subjectBeginsWith` gemaakt in een reeks containers die een voorvoegsel delen, gebruikt u een filter als:

```
/blobServices/default/containers/containerprefix
```

Als u gebeurtenissen wilt matchen met `subjectBeginsWith` blobs die in een specifieke container zijn gemaakt, gebruikt u een filter als:

```
/blobServices/default/containers/containername/
```

Als u gebeurtenissen wilt matchen van blobs die zijn `subjectBeginsWith` gemaakt in een specifiek containerdat een blobnaamvoorvoegsel deelt, gebruikt u een filter als:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Als u gebeurtenissen wilt matchen van blobs die `subjectEndsWith` zijn gemaakt in een specifieke container die een blob-achtervoegsel deelt, gebruikt u een filter als '.log' of '.jpg'. Zie [Gebeurtenisrasterconcepten](../../event-grid/concepts.md#event-subscriptions)voor meer informatie .

## <a name="practices-for-consuming-events"></a>Praktijken voor het consumeren van gebeurtenissen

Toepassingen die blob-opslaggebeurtenissen verwerken, moeten een aantal aanbevolen procedures volgen:
> [!div class="checklist"]
> * Aangezien meerdere abonnementen kunnen worden geconfigureerd om gebeurtenissen naar dezelfde gebeurtenishandler te leiden, is het belangrijk om niet aan te nemen dat gebeurtenissen van een bepaalde bron zijn, maar om het onderwerp van het bericht te controleren om ervoor te zorgen dat het afkomstig is van het opslagaccount dat u verwacht.
> * Controleer ook of het eventType een gebeurtenis is die u bereid bent te verwerken en ga er niet van uit dat alle gebeurtenissen die u ontvangt, de typen zijn die u verwacht.
> * Aangezien berichten na enige vertraging kunnen binnenkomen, gebruikt u de etag-velden om te begrijpen of uw informatie over objecten nog steeds up-to-date is. Zie Gelijktijdigheid beheren [in Blob-opslag](https://docs.microsoft.com/azure/storage/common/storage-concurrency?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#managing-concurrency-in-blob-storage)voor meer informatie over het gebruik van het veld etag. 
> * Aangezien berichten niet in orde kunnen komen, gebruikt u de sequencervelden om de volgorde van gebeurtenissen op een bepaald object te begrijpen. Het sequencerveld is een tekenreekswaarde die de logische volgorde van gebeurtenissen voor een bepaalde blobnaam vertegenwoordigt. U standaardtekenreeksvergelijking gebruiken om de relatieve volgorde van twee gebeurtenissen op dezelfde blobnaam te begrijpen.
> * Gebruik het veld blobType om te begrijpen welk type bewerkingen zijn toegestaan op de blob en welke clientbibliotheektypen u moet gebruiken om toegang te krijgen tot de blob. Geldige waarden `BlockBlob` zijn `PageBlob`of . 
> * Gebruik het url-veld met de `CloudBlockBlob` constructors om `CloudAppendBlob` toegang te krijgen tot de blob.
> * Velden negeren die u niet begrijpt. Deze praktijk zal u helpen om u bestand te houden tegen nieuwe functies die in de toekomst kunnen worden toegevoegd.
> * Als u ervoor wilt zorgen dat de gebeurtenis **Microsoft.Storage.BlobCreated** alleen wordt geactiveerd wanneer `CopyBlob` `PutBlob`een `PutBlockList` `FlushWithClose` Block Blob volledig is vastgelegd, filtert u de gebeurtenis voor de API-aanroepen of REST. Deze API-aanroepen activeren de gebeurtenis **Microsoft.Storage.BlobCreated** pas nadat gegevens volledig zijn toegewezen aan een Block Blob. Zie [Gebeurtenissen filteren voor gebeurtenisraster](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)voor meer informatie over het maken van een filter.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over gebeurtenisraster en geef Blob-opslaggebeurtenissen een kans:

- [Over Event Grid](../../event-grid/overview.md)
- [Blob-opslaggebeurtenissen routeren naar een aangepast webeindpunt](storage-blob-event-quickstart.md)
