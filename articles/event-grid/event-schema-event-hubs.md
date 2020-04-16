---
title: Azure Event Hubs als gebeurtenisrasterbron
description: Beschrijft de eigenschappen die worden geleverd voor gebeurtenishubs met Azure Event Grid
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: fd65c20f07a091fa1fc8a6cbf003986e1096ebe3
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393345"
---
# <a name="azure-event-hubs-as-an-event-grid-source"></a>Azure Event Hubs als gebeurtenisrasterbron

In dit artikel worden de eigenschappen en het schema voor gebeurtenishubs-gebeurtenissen weergeven.Zie Azure Event Grid-gebeurtenisschema voor een inleiding tot gebeurtenisschema ['Azure Event Grid'.](event-schema.md)

## <a name="event-grid-event-schema"></a>Gebeurtenisschema gebeurtenisraster

### <a name="available-event-types"></a>Beschikbare gebeurtenistypen

Event Hubs zendt het gebeurtenistype **Microsoft.EventHub.CaptureFileCreated** uit wanneer een opnamebestand wordt gemaakt.

### <a name="example-event"></a>Voorbeeldgebeurtenis

In deze voorbeeldgebeurtenis wordt het schema weergegeven van een gebeurtenisgebeurtenis die wordt verhoogd wanneer de opnamefunctie een bestand opslaat: 

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

### <a name="event-properties"></a>Gebeurtenis-eigenschappen

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Volledig resourcepad naar de gebeurtenisbron. Dit veld is niet schrijfbaar. Event Grid biedt deze waarde. |
| Onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| eventType | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | De tijd dat de gebeurtenis wordt gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | Unieke id voor de gebeurtenis. |
| data | object | Gebeurtenishubgebeurtenisgegevens. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| fileUrl | tekenreeks | Het pad naar het opnamebestand. |
| Bestandstype | tekenreeks | Het bestandstype van het vastleggensbestand. |
| partitionId | tekenreeks | De shard-ID. |
| sizeInBytes | geheel getal | De bestandsgrootte. |
| gebeurtenisAantal | geheel getal | Het aantal gebeurtenissen in het bestand. |
| eersteSequenceNumber | geheel getal | Het kleinste volgnummer uit de wachtrij. |
| lastSequenceNumber | geheel getal | Het laatste volgnummer uit de wachtrij. |
| firstEnqueueTime | tekenreeks | De eerste keer vanuit de wachtrij. |
| lastEnqueueTime | tekenreeks | De laatste keer uit de wachtrij. |

## <a name="tutorials-and-how-tos"></a>Zelfstudies en handleidingen

|Titel  |Beschrijving  |
|---------|---------|
| [Zelfstudie: big data streamen naar een datawarehouse](event-grid-event-hubs-integration.md) | Wanneer Gebeurtenishubs een Capture-bestand maakt, stuurt gebeurtenisraster een gebeurtenis naar een functie-app. De app haalt het capture-bestand op en migreert gegevens naar een gegevensmagazijn. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is gebeurtenisraster voor](overview.md) een inleiding tot Azure Event Grid?
* Zie [Abonnement op gebeurtenisrastervoor](subscription-creation-schema.md)meer informatie over het maken van een Azure Event Grid-abonnement .
* Zie [Big Data streamen naar een datawarehouse](event-grid-event-hubs-integration.md)voor informatie over het afhandelen van gebeurtenishubs.