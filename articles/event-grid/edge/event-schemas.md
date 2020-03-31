---
title: Gebeurtenisschema's — Azure Event Grid IoT Edge | Microsoft Documenten
description: Gebeurtenisschema's in gebeurtenisraster op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242461"
---
# <a name="event-schemas"></a>Gebeurtenisschema's

Event Grid module accepteert en levert evenementen in JSON-formaat. Er zijn momenteel drie schema's die worden ondersteund door Event Grid: -

* **EventGridSchema**
* **AangepastSchema**
* **CloudEventSchema**

U het schema configureren waaraan een uitgever moet voldoen tijdens het maken van onderwerpen. Als dit niet is opgegeven, wordt deze standaard ingesteld op **EventGridSchema**. Gebeurtenissen die niet voldoen aan het verwachte schema, worden afgewezen.

Abonnees kunnen ook het schema configureren waarin ze de gebeurtenissen willen leveren. Als dit niet is opgegeven, is standaard het schema van het onderwerp.
Momenteel moet het schema voor de levering van abonnees overeenkomen met het invoerschema van het onderwerp. 

## <a name="eventgrid-schema"></a>EventGrid-schema

EventGrid-schema bestaat uit een set vereiste eigenschappen waaraan een publicerende entiteit moet voldoen. Elke uitgever moet de velden op het hoogste niveau invullen.

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

### <a name="eventgrid-schema-properties"></a>Gebeurtenisgrid-schema-eigenschappen

Alle gebeurtenissen hebben de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Vereist | Beschrijving |
| -------- | ---- | ----------- |-----------
| onderwerp | tekenreeks | Nee | Moet overeenkomen met het onderwerp waarop het is gepubliceerd. Event Grid vult het met de naam van het onderwerp waarop het is gepubliceerd als niet gespecificeerd. |
| Onderwerp | tekenreeks | Ja | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| eventType | tekenreeks | Ja | Gebeurtenistype voor deze gebeurtenisbron, bijvoorbeeld BlobCreated. |
| eventTime | tekenreeks | Ja | De tijd dat de gebeurtenis wordt gegenereerd op basis van de UTC-tijd van de provider. |
| Id | tekenreeks | Nee | Unieke id voor de gebeurtenis. |
| data | object | Nee | Wordt gebruikt om gebeurtenisgegevens vast te leggen die specifiek zijn voor de publicerende entiteit. |
| dataVersion | tekenreeks | Ja | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | Nee | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

### <a name="example--eventgrid-schema-event"></a>Voorbeeld — Gebeurtenis EventGrid-schema

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>Schema CustomEvent

In het aangepaste schema zijn er geen verplichte eigenschappen die worden afgedwongen, zoals het EventGrid-schema. De publicatieentiteit kan het gebeurtenisschema volledig beheren. Het biedt maximale flexibiliteit en maakt scenario's mogelijk waarin u al een op gebeurtenissen gebaseerd systeem hebt en bestaande gebeurtenissen wilt hergebruiken en/of niet wilt worden gekoppeld aan een specifiek schema.

### <a name="custom-schema-properties"></a>Aangepaste schema-eigenschappen

Geen verplichte eigenschappen. Het is aan de publicerende entiteit om de payload te bepalen.

### <a name="example--custom-schema-event"></a>Voorbeeld — Aangepaste schemagebeurtenis

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>CloudEvent-schema

Naast de bovenstaande schema's ondersteunt Gebeurtenisraster gebeurtenissen in het [JSON-schema van CloudEvents.](https://github.com/cloudevents/spec/blob/master/json-format.md) CloudEvents is een open specificatie voor het beschrijven van gebeurtenisgegevens. Het vereenvoudigt de interoperabiliteit door een gemeenschappelijk gebeurtenisschema voor het publiceren en consumeren van gebeurtenissen op te geven. Het is onderdeel van [CNCF](https://www.cncf.io/) en momenteel beschikbaar versie is 1.0-rc1.

### <a name="cloudevent-schema-properties"></a>Eigenschappen van CloudEvent-schema

Raadpleeg [de CloudEvents-specificatie](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) voor de verplichte envelopeigenschappen.

### <a name="example--cloud-event"></a>Voorbeeld — cloudgebeurtenis
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
