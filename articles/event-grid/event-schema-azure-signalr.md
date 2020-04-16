---
title: Azure SingnalR als gebeurtenisrasterbron
description: Beschrijft de eigenschappen die worden geleverd voor Azure SignalR-gebeurtenissen met Azure Event Grid
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: babanisa
ms.openlocfilehash: 730d1a7a053ab636c45313dd0c35a537434eb782
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393395"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Azure Event Grid-gebeurtenisschema voor SignalR-service

In dit artikel worden de eigenschappen en het schema voor gebeurtenissen van de SignalR-service weergegeven.Zie Azure Event Grid-gebeurtenisschema voor een inleiding tot gebeurtenisschema ['Azure Event Grid'.](event-schema.md) Het geeft u ook een lijst met snelle starts en zelfstudies om Azure SignalR als gebeurtenisbron te gebruiken.

## <a name="event-grid-event-schema"></a>Gebeurtenisschema gebeurtenisraster

### <a name="available-event-types"></a>Beschikbare gebeurtenistypen

SignalR Service zendt de volgende gebeurtenistypen uit:

| Gebeurtenistype | Beschrijving |
| ---------- | ----------- |
| Microsoft.signalrService.ClientConnectionConnected | Verhoogd wanneer een clientverbinding is verbonden. |
| Microsoft.signalrservice.clientConnectionDisconnected | Verhoogd wanneer een clientverbinding is verbroken. |

### <a name="example-event"></a>Voorbeeldgebeurtenis

In het volgende voorbeeld wordt het schema van een clientverbinding weergegeven die is verbonden: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionConnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Het schema voor een verbroken gebeurtenis met de clientverbinding is vergelijkbaar: 

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourceGroups/signalr-rg/providers/Microsoft.SignalRService/SignalR/signalr-resource",
  "subject": "/hub/chat",
  "eventType": "Microsoft.SignalRService.ClientConnectionDisconnected",
  "eventTime": "2019-06-10T18:41:00.9584103Z",
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "data": {
    "timestamp": "2019-06-10T18:41:00.9584103Z",
    "hubName": "chat",
    "connectionId": "crH0uxVSvP61p5wkFY1x1A",
    "userId": "user-eymwyo23",
    "errorMessage": "Internal server error."
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
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
| data | object | Gegevens over de gebeurtenis signalr service. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevensobject heeft de volgende eigenschappen:

| Eigenschap | Type | Beschrijving |
| -------- | ---- | ----------- |
| tijdstempel | tekenreeks | De tijd dat de gebeurtenis wordt gegenereerd op basis van de UTC-tijd van de provider. |
| hubNaam | tekenreeks | De hub waartoe de clientverbinding behoort. |
| verbindingId | tekenreeks | De unieke id voor de clientverbinding. |
| userId | tekenreeks | De gebruikers-id gedefinieerd in claim. |
| Errormessage | tekenreeks | De fout die ervoor zorgt dat de verbinding wordt verbroken. |

## <a name="tutorials-and-how-tos"></a>Zelfstudies en handleidingen
|Titel | Beschrijving |
|---------|---------|
| [Reageren op Azure SignalR Service-gebeurtenissen met gebeurtenisraster](../azure-signalr/signalr-concept-event-grid-integration.md) | Overzicht van de integratie van Azure SignalR Service met Event Grid. |
| [Azure SignalR Service-gebeurtenissen verzenden naar gebeurtenisraster](../azure-signalr/signalr-howto-event-grid-integration.md) | Hier ziet u hoe u Azure SignalR Service-gebeurtenissen naar een toepassing verzendt via gebeurtenisraster. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is gebeurtenisraster voor](overview.md) een inleiding tot Azure Event Grid?
* Zie [Abonnement op gebeurtenisrastervoor](subscription-creation-schema.md)meer informatie over het maken van een Azure Event Grid-abonnement .
