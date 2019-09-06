---
title: Azure Event Grid event hubs-gebeurtenisschema
description: Beschrijft de eigenschappen die beschikbaar zijn voor gebeurtenissen van event hubs met Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: reference
ms.date: 01/17/2019
ms.author: spelluru
ms.openlocfilehash: 9c0113687d27bf43375f298057129a5594ec0a06
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60561825"
---
# <a name="azure-event-grid-event-schema-for-event-hubs"></a>Azure Event Grid-gebeurtenisschema voor eventhubs

Dit artikel bevat de eigenschappen en het schema voor event hubs-gebeurtenissen. Zie voor een inleiding tot gebeurtenisschema's, [Azure Event Grid-gebeurtenisschema](event-schema.md).

Zie voor een lijst met zelfstudies en voorbeelden van scripts, [Event Hubs-gebeurtenisbron](event-sources.md#event-hubs).

### <a name="available-event-types"></a>Typen van de gebeurtenis berichten beschikbaar

Eventhubs verzendt de **Microsoft.EventHub.CaptureFileCreated** gebeurtenistype wanneer een bestand vastleggen wordt gemaakt.

## <a name="example-event"></a>Voorbeeld van de gebeurtenis

Deze voorbeeldgebeurtenis ziet u het schema van een event hubs-gebeurtenis treedt op wanneer de capture-functie wordt een bestand opgeslagen: 

```json
[
    {
        "topic": "/subscriptions/<guid>/resourcegroups/rgDataMigrationSample/providers/Microsoft.EventHub/namespaces/tfdatamigratens",
        "subject": "eventhubs/hubdatamigration",
        "eventType": "Microsoft.EventHub.CaptureFileCreated",
        "eventTime": "2017-08-31T19:12:46.0498024Z",
        "id": "14e87d03-6fbf-4bb2-9a21-92bd1281f247",
        "data": {
            "fileUrl": "https://tf0831datamigrate.blob.core.windows.net/windturbinecapture/tfdatamigratens/hubdatamigration/1/2017/08/31/19/11/45.avro",
            "fileType": "AzureBlockBlob",
            "partitionId": "1",
            "sizeInBytes": 249168,
            "eventCount": 1500,
            "firstSequenceNumber": 2400,
            "lastSequenceNumber": 3899,
            "firstEnqueueTime": "2017-08-31T19:12:14.674Z",
            "lastEnqueueTime": "2017-08-31T19:12:44.309Z"
        },
        "dataVersion": "",
        "metadataVersion": "1"
    }
]
```

## <a name="event-properties"></a>Eigenschappen van gebeurtenis

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| topic | string | Volledige resource-pad naar de bron van de gebeurtenis. Dit veld is niet beschrijfbaar. Event Grid biedt deze waarde. |
| subject | string | Uitgever gedefinieerde pad naar het onderwerp van de gebeurtenis. |
| eventType | string | Een van de geregistreerde gebeurtenis-typen voor de bron van deze gebeurtenis. |
| eventTime | string | Het moment waarop dat de gebeurtenis is gegenereerd, is afhankelijk van de UTC-tijd van de provider. |
| id | string | De unieke id voor de gebeurtenis. |
| data | object | Gebeurtenisgegevens van de Event hub. |
| dataVersion | string | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | string | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema van de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| fileUrl | string | Het pad naar het bestand vastleggen. |
| fileType | string | Het bestandstype van het bestand vastleggen. |
| partitionId | string | De shard-ID. |
| sizeInBytes | integer | De bestandsgrootte. |
| eventCount | integer | Het aantal gebeurtenissen in het bestand. |
| firstSequenceNumber | integer | Het kleinste volgnummer uit de wachtrij. |
| lastSequenceNumber | integer | Het laatste volgnummer uit de wachtrij. |
| firstEnqueueTime | string | De eerste keer uit de wachtrij. |
| lastEnqueueTime | string | De laatste keer uit de wachtrij. |

## <a name="next-steps"></a>Volgende stappen

* Zie voor een inleiding tot Azure Event Grid, [wat is Event Grid?](overview.md)
* Zie voor meer informatie over het maken van een Azure Event Grid-abonnement [Event Grid-abonnementsschema](subscription-creation-schema.md).
* Zie voor meer informatie over het verwerken van gebeurtenissen van event hubs [Stream van big data in een datawarehouse](event-grid-event-hubs-integration.md).
