---
title: Azure Event Grid Azure App Configuration Event event schema
description: Beschrijft de eigenschappen die worden geleverd voor Azure App Configuration-gebeurtenissen met Azure Event Grid
services: event-grid
author: jimmyca
ms.service: event-grid
ms.topic: reference
ms.date: 05/30/2019
ms.author: jimmyca
ms.openlocfilehash: fe0274f723692eea3cfd25cc0e9e146b35dce2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66735779"
---
# <a name="azure-event-grid-event-schema-for-azure-app-configuration"></a>Azure Event Grid-gebeurtenisschema voor Azure App-configuratie

In dit artikel vindt u de eigenschappen en het schema voor Azure App Configuration-gebeurtenissen. Zie Azure Event Grid-gebeurtenisschema voor een inleiding tot gebeurtenisschema ['Azure Event Grid'.](event-schema.md)

Zie [gebeurtenisbron Azure App Configuration voor](event-sources.md#app-configuration)een lijst met voorbeeldscripts en -zelfstudies .

## <a name="available-event-types"></a>Beschikbare gebeurtenistypen

Azure App Configuration zendt de volgende gebeurtenistypen uit:

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Microsoft.appConfiguration.KeyValueGewijzigd | Verhoogd wanneer een sleutelwaarde wordt gemaakt of vervangen. |
| Microsoft.AppConfiguration.KeyValueDeleted | Verhoogd wanneer een sleutelwaarde wordt verwijderd. |

## <a name="example-event"></a>Voorbeeldgebeurtenis

In het volgende voorbeeld wordt het schema van een gewijzigde gebeurtenis met sleutelwaarde weergegeven: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueModified",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

Het schema voor een verwijderde gebeurtenis met sleutelwaarde is vergelijkbaar: 

```json
[{
  "id": "84e17ea4-66db-4b54-8050-df8f7763f87b",
  "topic": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/microsoft.appconfiguration/configurationstores/contoso",
  "subject": "https://contoso.azconfig.io/kv/Foo?label=FizzBuzz",
  "data": {
    "key": "Foo",
    "label": "FizzBuzz",
    "etag": "FnUExLaj2moIi4tJX9AXn9sakm0"
  },
  "eventType": "Microsoft.AppConfiguration.KeyValueDeleted",
  "eventTime": "2019-05-31T20:05:03Z",
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```
 
## <a name="event-properties"></a>Gebeurtenis-eigenschappen

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Volledig resourcepad naar de gebeurtenisbron. Dit veld is niet schrijfbaar. Event Grid biedt deze waarde. |
| Onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| eventType | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | De tijd dat de gebeurtenis wordt gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | Unieke id voor de gebeurtenis. |
| data | object | Gebeurtenisgegevens voor app-configuratie. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| sleutel | tekenreeks | De sleutel van de sleutelwaarde die is gewijzigd of verwijderd. |
| label | tekenreeks | Het label, indien aanwezig, van de sleutelwaarde die is gewijzigd of verwijderd. |
| etag etag | tekenreeks | Voor `KeyValueModified` de etag van de nieuwe sleutelwaarde. Voor `KeyValueDeleted` de etag van de sleutelwaarde die is verwijderd. |
 
## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is gebeurtenisraster voor](overview.md) een inleiding tot Azure Event Grid?
* Zie [Abonnement op gebeurtenisrastervoor](subscription-creation-schema.md)meer informatie over het maken van een Azure Event Grid-abonnement .
* Zie Route Azure App Configuration events [- Azure CLI](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json)voor een inleiding tot het werken met azure app-configuratiegebeurtenissen. 