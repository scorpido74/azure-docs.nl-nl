---
title: Azure Event Grid-gebeurtenisschema
description: Beschrijft de eigenschappen en het schema die voor alle gebeurtenissen aanwezig zijn.Gebeurtenissen bestaan uit een set van vijf vereiste tekenreekseigenschappen en een vereist gegevensobject.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/21/2020
ms.author: babanisa
ms.openlocfilehash: 35cea2e6df311d2f4071686c21c8e4c36477abc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244834"
---
# <a name="azure-event-grid-event-schema"></a>Azure Event Grid-gebeurtenisschema

In dit artikel worden de eigenschappen en het schema beschreven die voor alle gebeurtenissen aanwezig zijn.Gebeurtenissen bestaan uit een set van vijf vereiste tekenreekseigenschappen en een vereist gegevensobject. De eigenschappen zijn gemeenschappelijk voor alle gebeurtenissen van elke uitgever. Het gegevensobject heeft eigenschappen die specifiek zijn voor elke uitgever. Voor systeemonderwerpen zijn deze eigenschappen specifiek voor de resourceprovider, zoals Azure Storage of Azure Event Hubs.

Gebeurtenisbronnen verzenden gebeurtenissen naar Azure Event Grid in een array, die meerdere gebeurtenisobjecten kan hebben. Wanneer u gebeurtenissen plaatst in een onderwerp van het gebeurtenisraster, kan de array een totale grootte van maximaal 1 MB hebben. Elke gebeurtenis in de array is beperkt tot 64 KB (Algemene beschikbaarheid) of 1 MB (voorbeeld). Als een gebeurtenis of de array groter is dan de groottelimieten, ontvangt u het antwoord **413 Payload Too Large**.

> [!NOTE]
> Een gebeurtenis van grootte tot 64 KB wordt gedekt door de ALGEMENE Beschikbaarheid (GA) Service Level Agreement (SLA). De ondersteuning voor een gebeurtenis van maximaal 1 MB is momenteel in preview. Gebeurtenissen van meer dan 64 KB worden in stappen van 64 KB in rekening gebracht. 

Event Grid stuurt de gebeurtenissen naar abonnees in een array met één gebeurtenis. Dit gedrag kan in de toekomst veranderen.

U het JSON-schema voor de gebeurtenis Gebeurtenisraster en de gegevenspayload van elke Azure-uitgever vinden in het [gebeurtenisschemaarchief](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/eventgrid/data-plane).

## <a name="event-schema"></a>Gebeurtenisschema

In het volgende voorbeeld worden de eigenschappen weergegeven die door alle gebeurtenis-uitgevers worden gebruikt:

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

Het schema dat is gepubliceerd voor een Azure Blob-opslaggebeurtenis is bijvoorbeeld:

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

## <a name="event-properties"></a>Gebeurtenis-eigenschappen

Alle gebeurtenissen hebben dezelfde volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Vereist | Beschrijving |
| -------- | ---- | -------- | ----------- |
| onderwerp | tekenreeks | Nee, maar als dit is opgenomen, moet het Azure Resource Manager-id van het gebeurtenisraster exact overeenkomen. Als dit niet is inbegrepen, zal Event Grid op het evenement stempelen. | Volledig resourcepad naar de gebeurtenisbron. Dit veld is niet schrijfbaar. Event Grid biedt deze waarde. |
| Onderwerp | tekenreeks | Ja | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| eventType | tekenreeks | Ja | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | Ja | De tijd dat de gebeurtenis wordt gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | Ja | Unieke id voor de gebeurtenis. |
| data | object | Nee | Gebeurtenisgegevens die specifiek zijn voor de resourceprovider. |
| dataVersion | tekenreeks | Nee, maar zal worden gestempeld met een lege waarde. | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | Niet vereist, maar indien inbegrepen, moet `metadataVersion` overeenkomen met `1`de Event Grid Schema precies (op dit moment, alleen ). Als dit niet is inbegrepen, zal Event Grid op het evenement stempelen. | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Zie de gebeurtenisbron voor meer informatie over de eigenschappen in het gegevensobject:

* [Azure-abonnementen (beheerbewerkingen)](event-schema-subscriptions.md)
* [Containerregister](event-schema-container-registry.md)
* [Blob-opslag](event-schema-blob-storage.md)
* [Gebeurtenishubs](event-schema-event-hubs.md)
* [IoT-hub](event-schema-iot-hub.md)
* [Media Services](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json)
* [Resourcegroepen (beheerbewerkingen)](event-schema-resource-groups.md)
* [Servicebus](event-schema-service-bus.md)
* [Azure SignalR](event-schema-azure-signalr.md)
* [Azure Machine Learning](event-schema-machine-learning.md)

Voor aangepaste onderwerpen bepaalt de gebeurtenis-uitgever het gegevensobject. De gegevens op het hoogste niveau moeten dezelfde velden hebben als standaardgebeurtenissen met resources.

Wanneer u gebeurtenissen publiceert naar aangepaste onderwerpen, maakt u onderwerpen voor uw evenementen waarmee abonnees gemakkelijk kunnen weten of ze geïnteresseerd zijn in het evenement. Abonnees gebruiken het onderwerp om gebeurtenissen te filteren en te routeren. Overweeg het pad op te geven voor waar de gebeurtenis is gebeurd, zodat abonnees kunnen filteren op segmenten van dat pad. Met het pad kunnen abonnees gebeurtenissen in beperkte of brede lijnen filteren. Als u bijvoorbeeld een pad met `/A/B/C` drie segmenten opgeeft, zoals in `/A` het onderwerp, kunnen abonnees filteren op het eerste segment om een brede set gebeurtenissen te krijgen. Die abonnees krijgen evenementen `/A/B/C` met `/A/D/E`onderwerpen als of . Andere abonnees kunnen `/A/B` filteren op een smallere set gebeurtenissen te krijgen.

Soms moet uw onderwerp meer details over wat er gebeurd is. De uitgever **Opslagaccounts** geeft bijvoorbeeld `/blobServices/default/containers/<container-name>/blobs/<file>` het onderwerp aan wanneer een bestand aan een container wordt toegevoegd. Een abonnee kan filteren `/blobServices/default/containers/testcontainer` op het pad om alle gebeurtenissen voor die container op te halen, maar niet andere containers in het opslagaccount. Een abonnee kan ook filteren of `.txt` routeren door het achtervoegsel om alleen met tekstbestanden te werken.

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is gebeurtenisraster voor](overview.md) een inleiding tot Azure Event Grid?
* Zie [Abonnement op gebeurtenisrastervoor](subscription-creation-schema.md)meer informatie over het maken van een Azure Event Grid-abonnement .
