---
title: Azure-Signa lering als Event Grid bron
description: Hierin worden de eigenschappen beschreven die worden gegeven voor Azure-seingevings gebeurtenissen met Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 2ac391f366c4b9a82741a1b6b3135f5d7b5fe331
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86106648"
---
# <a name="azure-event-grid-event-schema-for-signalr-service"></a>Azure Event Grid-gebeurtenis schema voor de signaal service

Dit artikel bevat de eigenschappen en het schema voor de seingevings service-gebeurtenissen.Zie [Azure Event grid-gebeurtenis schema](event-schema.md)voor een inleiding tot gebeurtenis schema's. Het biedt ook een lijst met snel starten en zelf studies voor het gebruik van Azure signalering als een gebeurtenis bron.

## <a name="event-grid-event-schema"></a>Event Grid-gebeurtenisschema

### <a name="available-event-types"></a>Beschik bare gebeurtenis typen

De seingevings service verzendt de volgende gebeurtenis typen:

| Gebeurtenistype | Description |
| ---------- | ----------- |
| Micro soft. SignalRService. ClientConnectionConnected | Deze gebeurtenis treedt op wanneer een verbinding met een client is gemaakt. |
| Micro soft. SignalRService. ClientConnectionDisconnected | Deze gebeurtenis treedt op wanneer de verbinding van een client verbinding is verbroken. |

### <a name="example-event"></a>Voorbeeld gebeurtenis

In het volgende voor beeld ziet u het schema van een gebeurtenis verbonden met een client verbinding: 

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

Het schema voor een gebeurtenis waarmee een client verbinding is verbroken, is vergelijkbaar met het volgende: 

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

### <a name="event-properties"></a>Gebeurtenis eigenschappen

Een gebeurtenis heeft de volgende gegevens op het hoogste niveau:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| onderwerp | tekenreeks | Volledige bronpad naar de bron van de gebeurtenis. Dit veld kan niet worden geschreven. Event Grid biedt deze waarde. |
| Onderwerp | tekenreeks | Het door de uitgever gedefinieerde pad naar het gebeurtenisonderwerp. |
| Type | tekenreeks | Een van de geregistreerde gebeurtenistypen voor deze gebeurtenisbron. |
| eventTime | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| id | tekenreeks | De unieke id voor de gebeurtenis. |
| gegevens | object | Gebeurtenis gegevens van de signaal/service. |
| dataVersion | tekenreeks | De schemaversie van het gegevensobject. De uitgever definieert de schemaversie. |
| metadataVersion | tekenreeks | De schemaversie van de metagegevens van de gebeurtenis. Event Grid definieert het schema voor de eigenschappen op het hoogste niveau. Event Grid biedt deze waarde. |

Het gegevens object heeft de volgende eigenschappen:

| Eigenschap | Type | Description |
| -------- | ---- | ----------- |
| tijdstempel | tekenreeks | Het tijdstip waarop de gebeurtenis is gegenereerd op basis van de UTC-tijd van de provider. |
| hubName | tekenreeks | De hub waartoe de client verbinding behoort. |
| connectionId | tekenreeks | De unieke id voor de client verbinding. |
| userId | tekenreeks | De gebruikers-id die is gedefinieerd in claim. |
| errorMessage | tekenreeks | De fout die ervoor zorgt dat de verbinding is verbroken. |

## <a name="tutorials-and-how-tos"></a>Zelfstudies en handleidingen
|Titel | Beschrijving |
|---------|---------|
| [Reageren op gebeurtenissen van de Azure signalerings service met behulp van Event Grid](../azure-signalr/signalr-concept-event-grid-integration.md) | Overzicht van het integreren van de Azure signalerings service met Event Grid. |
| [Service gebeurtenissen van Azure signalering verzenden naar Event Grid](../azure-signalr/signalr-howto-event-grid-integration.md) | Laat zien hoe u Azure signalerings service-gebeurtenissen verzendt naar een toepassing via Event Grid. |

## <a name="next-steps"></a>Volgende stappen

* Zie [Wat is Event grid?](overview.md) voor een inleiding tot Azure Event grid.
* Zie [Event grid Subscription schema](subscription-creation-schema.md)voor meer informatie over het maken van een Azure Event grid-abonnement.
