---
title: Azure Event Grid-gebeurtenis schema
description: Beschrijft de eigenschappen die worden gegeven voor gebeurtenissen met Azure Event Grid
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/20/2019
ms.author: babanisa
ms.openlocfilehash: 44cc611a9a7d71a3ac4ac7b0d779b18778d0aacd
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607608"
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Grid-gebeurtenis schema

In dit artikel worden de eigenschappen en schema's beschreven die voor alle gebeurtenissen aanwezig zijn. Gebeurtenissen bestaan uit een set van vijf vereiste teken reeks eigenschappen en een vereist gegevens object. De eigenschappen zijn gebruikelijk voor alle gebeurtenissen van elke uitgever. Het gegevens object heeft eigenschappen die specifiek zijn voor elke uitgever. Voor systeem onderwerpen zijn deze eigenschappen specifiek voor de resource provider, zoals Azure Storage of Azure Event Hubs.

Gebeurtenis bronnen verzenden gebeurtenissen naar Azure Event Grid in een matrix, die verschillende gebeurtenis objecten kunnen hebben. Bij het posten van gebeurtenissen naar een event grid-onderwerp kan de matrix een totale grootte hebben van Maxi maal 1 MB. Elke gebeurtenis in de matrix is beperkt tot 64 KB (algemene Beschik baarheid) of 1 MB (preview-versie). Als een gebeurtenis of de matrix groter is dan de maximale grootte, ontvangt u het antwoord **413 Payload te groot**.

> [!NOTE]
> Een gebeurtenis met een grootte van Maxi maal 64 KB wordt gedekt door de algemene Beschik baarheid (GA) Service Level Agreement (SLA). De ondersteuning voor een gebeurtenis met een grootte van Maxi maal 1 MB is momenteel als preview-versie beschikbaar. Gebeurtenissen van meer dan 64 KB worden in rekening gebracht in stappen van 64-KB. 

Event Grid verzendt de gebeurtenissen naar abonnees in een matrix met één gebeurtenis. Dit gedrag kan in de toekomst worden gewijzigd.

U vindt het JSON-schema voor de Event Grid gebeurtenis en elke nettolading van de gegevens van de Azure-uitgever in de [gebeurtenis schema opslag](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Gebeurtenisschema

In het volgende voor beeld ziet u de eigenschappen die worden gebruikt door alle gebeurtenis uitgevers:

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

Het schema dat is gepubliceerd voor een Azure Blob-opslag gebeurtenis is bijvoorbeeld:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    },
    "dataVersion": "",
    "metadataVersion": "1"
  }
]
```

## <a name="event-properties"></a>Gebeurtenis eigenschappen

Alle gebeurtenissen hebben dezelfde gegevens op het hoogste niveau:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld is niet beschrijfbaar. Event Grid levert deze waarde. |
| Onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenis onderwerp. |
| Type | tekenreeks | Een van de geregistreerde gebeurtenis typen voor deze gebeurtenis bron. |
| eventTime | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | De unieke id voor de gebeurtenis. |
| gegevens | object | Gebeurtenis gegevens die specifiek zijn voor de resource provider. |
| dataVersion | tekenreeks | De schema versie van het gegevens object. De uitgever definieert de schema versie. |
| metadataVersion | tekenreeks | De schema versie van de meta gegevens van de gebeurtenis. Event Grid definieert het schema van de eigenschappen op het hoogste niveau. Event Grid levert deze waarde. |

Zie de gebeurtenis bron voor meer informatie over de eigenschappen in het gegevens object:

* [Azure-abonnementen (beheer bewerkingen)](event-schema-subscriptions.md)
* [Container Registry](event-schema-container-registry.md)
* [Blob Storage](event-schema-blob-storage.md)
* [Event Hubs](event-schema-event-hubs.md)
* [IoT Hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Resource groepen (beheer bewerkingen)](event-schema-resource-groups.md)
* [Service Bus](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

Voor aangepaste onderwerpen wordt het gegevens object bepaald door de uitgever van de gebeurtenis. De gegevens op het hoogste niveau moeten dezelfde velden hebben als standaard, door de resource gedefinieerde gebeurtenissen.

Bij het publiceren van gebeurtenissen naar aangepaste onderwerpen maakt u onderwerpen voor uw evenementen waarmee abonnees eenvoudig kunnen zien of ze geïnteresseerd zijn in de gebeurtenis. Abonnees gebruiken het onderwerp om gebeurtenissen te filteren en te routeren. Overweeg het pad op te geven waar de gebeurtenis zich voordeed, zodat abonnee servers kunnen filteren op segmenten van dat pad. Met het pad kunnen abonnees de gebeurtenissen op een smalle of brede manier filteren. Als u bijvoorbeeld een drie segment pad als `/A/B/C` in het onderwerp opgeeft, kunnen abonnees filteren op het eerste segment `/A` om een grote set gebeurtenissen te verkrijgen. Deze abonnees ontvangen gebeurtenissen met onderwerpen als `/A/B/C` of `/A/D/E`. Andere abonnees kunnen filteren op `/A/B` om een smalle set gebeurtenissen te verkrijgen.

Soms moet uw onderwerp meer details over wat er is gebeurd. De uitgever van **opslag accounts** levert bijvoorbeeld het onderwerp `/blobServices/default/containers/<container-name>/blobs/<file>` wanneer een bestand wordt toegevoegd aan een container. Een abonnee kan filteren op het pad `/blobServices/default/containers/testcontainer` om alle gebeurtenissen voor die container op te halen, maar niet andere containers in het opslag account. Een abonnee kan ook filteren op of route ring van het achtervoegsel `.txt` om alleen met tekst bestanden te werken.

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Event grid?](overview.md) voor een inleiding tot Azure Event grid.
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.
