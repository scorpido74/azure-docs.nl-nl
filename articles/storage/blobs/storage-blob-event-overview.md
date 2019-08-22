---
title: Reageren op Azure Blob Storage-gebeurtenissen | Microsoft Docs
description: Gebruik Azure Event Grid om u te abonneren op gebeurtenissen van Blob Storage.
author: normesta
ms.author: normesta
ms.date: 01/30/2018
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: cbrooks
ms.openlocfilehash: b813ef89bb1a55f769d0ea2391855ba5d671c140
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648796"
---
# <a name="reacting-to-blob-storage-events"></a>Reageren op Blob Storage-gebeurtenissen

Met Azure Storage gebeurtenissen kunnen toepassingen reageren op gebeurtenissen, zoals het maken en verwijderen van blobs met behulp van moderne serverloze architecturen. Dit gebeurt zonder dat er complexe code of dure en inefficiënte polling services nodig zijn.

In plaats daarvan worden gebeurtenissen gepusht via [Azure Event grid](https://azure.microsoft.com/services/event-grid/) naar abonnees zoals Azure Functions, Azure Logic apps of zelfs naar uw eigen aangepaste HTTP-listener en betaalt u alleen voor wat u gebruikt.

Blob Storage-gebeurtenissen worden betrouwbaar verzonden naar de Event Grid-service die betrouw bare leverings Services biedt aan uw toepassingen via een uitgebreid beleid voor opnieuw proberen en levering van onbestelbare berichten.

Veelvoorkomende gebeurtenissen in Blob Storage zijn onder andere afbeeldings-of video verwerking, zoek indexering of een op bestanden gebaseerde werk stroom. Het asynchroon uploaden van bestanden is een uitstekend geschikt voor gebeurtenissen. Als de wijzigingen niet worden doorgevoerd, maar voor uw scenario een onmiddellijke reactie nodig is, kan de architectuur op basis van gebeurtenissen bijzonder efficiënt zijn.

Als u dit nu wilt doen, raadpleegt u een van de volgende Quick Start-artikelen:

|Als u dit hulp programma wilt gebruiken:    |Zie dit artikel: |
|--|-|
|Azure Portal    |[Snelstart: Blob Storage-gebeurtenissen naar een webeindpunt door sturen met de Azure Portal](https://docs.microsoft.com/azure/event-grid/blob-event-quickstart-portal?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|PowerShell    |[Snelstart: Opslag gebeurtenissen naar een webeindpunt door sturen met Power shell](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart-powershell?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|
|Azure-CLI    |[Snelstart: Opslag gebeurtenissen naar een webeindpunt door sturen met Azure CLI](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

Als uw account een hiërarchische naam ruimte heeft, ziet u in deze zelf studie hoe u verbinding maakt met een Event Grid-abonnement, een Azure-functie en een [taak](https://docs.azuredatabricks.net/user-guide/jobs.html) in azure Databricks: [Vind Gebruik Azure Data Lake Storage Gen2 gebeurtenissen om een Databricks Delta tabel](data-lake-storage-events.md)bij te werken.

## <a name="the-event-model"></a>Het gebeurtenis model

Event Grid gebruikt [gebeurtenis abonnementen](../../event-grid/concepts.md#event-subscriptions) om gebeurtenis berichten te routeren naar abonnees. In deze afbeelding ziet u de relatie tussen gebeurtenis uitgevers, gebeurtenis abonnementen en gebeurtenis-handlers.

![Event Grid model](./media/storage-blob-event-overview/event-grid-functional-model.png)

Abonneer u eerst op een eind punt voor een gebeurtenis. Wanneer een gebeurtenis vervolgens wordt geactiveerd, verzendt de Event Grid-Service gegevens over die gebeurtenis naar het eind punt.

Zie het artikel over het [schema voor Blob Storage-gebeurtenissen](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor het weer geven van:

> [!div class="checklist"]
> * Een volledige lijst met Blob Storage-gebeurtenissen en hoe elke gebeurtenis wordt geactiveerd.
> * Een voor beeld van de gegevens die het Event Grid verzendt voor elk van deze gebeurtenissen.
> * Het doel van elk sleutel waarde-paar dat wordt weer gegeven in de gegevens.

## <a name="filtering-events"></a>Gebeurtenissen filteren

BLOB gebeurtenis abonnementen kunnen worden gefilterd op basis van het gebeurtenis type en door de naam van de container en de blobnaam van het object dat is gemaakt of verwijderd.  Filters kunnen worden toegepast op gebeurtenis abonnementen tijdens het [maken](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest) van het gebeurtenis abonnement of [op een later tijdstip](/cli/azure/eventgrid/event-subscription?view=azure-cli-latest). De subject filters in Event Grid werken op basis van de overeenkomsten ' begint met ' en ' eindigt op ', zodat gebeurtenissen met een overeenkomend onderwerp aan de abonnee worden geleverd.

Zie [gebeurtenissen filteren voor Event grid voor](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)meer informatie over het Toep assen van filters.

Het onderwerp van Blob Storage-gebeurtenissen maakt gebruik van de volgende indeling:

```
/blobServices/default/containers/<containername>/blobs/<blobname>
```

Als u alle gebeurtenissen voor een opslag account wilt vergelijken, kunt u de onderwerps filters leeg laten.

Als u gebeurtenissen wilt vergelijken van blobs die zijn gemaakt in een set containers die een voor `subjectBeginsWith` voegsel delen, gebruikt u een filter zoals:

```
/blobServices/default/containers/containerprefix
```

Als u gebeurtenissen wilt vergelijken van blobs die zijn gemaakt in een `subjectBeginsWith` specifieke container, gebruikt u een filter zoals:

```
/blobServices/default/containers/containername/
```

Als u gebeurtenissen wilt vergelijken van blobs die zijn gemaakt in een specifieke container voor het delen `subjectBeginsWith` van een BLOB-naam, gebruikt u een filter zoals:

```
/blobServices/default/containers/containername/blobs/blobprefix
```

Als u gebeurtenissen wilt vergelijken van blobs die zijn gemaakt in een specifieke container met een `subjectEndsWith` BLOB-achtervoegsel, gebruikt u een filter zoals ". log" of ". jpg". Zie [Event grid-concepten](../../event-grid/concepts.md#event-subscriptions)voor meer informatie.

## <a name="practices-for-consuming-events"></a>Procedures voor het gebruiken van gebeurtenissen

Toepassingen die Blob Storage-gebeurtenissen verwerken, moeten een aantal aanbevolen procedures volgen:
> [!div class="checklist"]
> * Omdat meerdere abonnementen kunnen worden geconfigureerd voor het routeren van gebeurtenissen naar dezelfde gebeurtenis-handler, is het belang rijk om te voor komen dat gebeurtenissen afkomstig zijn uit een bepaalde bron, maar om het onderwerp van het bericht te controleren om ervoor te zorgen dat het afkomstig is van het opslag account dat u verwacht.
> * Controleer ook of de Event type is ingesteld als een voor bereiding op het proces en ga er niet van uit dat alle gebeurtenissen die u ontvangt, de verwachte typen zijn.
> * Wanneer berichten in aflopende volg orde en na enige vertraging kunnen aankomen, gebruikt u de ETAG-velden om te begrijpen of uw informatie over objecten nog steeds actueel is.  Gebruik ook de sequencer-velden om de volg orde van gebeurtenissen op een bepaald object te begrijpen.
> * Gebruik het veld blobType om te begrijpen welk type bewerkingen op de BLOB zijn toegestaan en welke client bibliotheek typen u moet gebruiken voor toegang tot de blob. Geldige waarden zijn ofwel `BlockBlob` of `PageBlob`. 
> * Gebruik het URL-veld met `CloudBlockBlob` de `CloudAppendBlob` Constructors om toegang te krijgen tot de blob.
> * Velden negeren die u niet begrijpt. Met deze procedure kunt u de nieuwe functies die in de toekomst kunnen worden toegevoegd, flexibeler maken.
> * Als u ervoor wilt zorgen dat de **gebeurtenis micro soft. storage. BlobCreated** alleen wordt geactiveerd als een blok-BLOB volledig is doorgevoerd, filtert u `CopyBlob`de gebeurtenis `PutBlockList` voor `FlushWithClose` de-, `PutBlob`-of rest API-aanroepen. Met deze API-aanroepen wordt de gebeurtenis **micro soft. storage. BlobCreated** alleen geactiveerd wanneer gegevens volledig zijn doorgevoerd in een blok-blob. Zie [gebeurtenissen filteren voor Event grid voor](https://docs.microsoft.com/azure/event-grid/how-to-filter-events)meer informatie over het maken van een filter.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over Event Grid en het geven van Blob Storage-gebeurtenissen een try:

- [Over Event Grid](../../event-grid/overview.md)
- [Blob Storage-gebeurtenissen naar een aangepast eind punt van een web routeren](storage-blob-event-quickstart.md)
