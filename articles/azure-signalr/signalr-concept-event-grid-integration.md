---
title: Reageren op Azure SignalR Service-gebeurtenissen
description: Gebruik Azure Event Grid om u te abonneren op Azure SignalR Service-gebeurtenissen. Andere downstream-services kunnen worden geactiveerd door deze gebeurtenissen.
services: azure-signalr,event-grid
author: chenyl
ms.author: chenyl
ms.reviewer: zhshang
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: signalr
ms.openlocfilehash: a8e25907b40b910f2b91884d355b6ac85eeaa250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158191"
---
# <a name="reacting-to-azure-signalr-service-events"></a>Reageren op Azure SignalR Service-gebeurtenissen

Azure SignalR Service-gebeurtenissen stellen toepassingen in staat om te reageren op clientverbindingen die zijn verbonden of losgekoppeld met behulp van moderne serverloze architecturen. Het doet dit zonder de noodzaak van ingewikkelde code of dure en inefficiënte polling diensten.  In plaats daarvan worden gebeurtenissen via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) doorgeschoven naar abonnees zoals Azure [Functions,](https://azure.microsoft.com/services/functions/) [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)of zelfs naar uw eigen aangepaste http-listener, en betaalt u alleen voor wat u gebruikt.

Azure SignalR Service-gebeurtenissen worden op betrouwbare wijze verzonden naar de Event Grid-service die betrouwbare leveringsservices biedt aan uw toepassingen via een uitgebreid beleid voor nieuwe investeringen en levering van dode brieven. Zie [Berichtbezorging van gebeurtenisrasteren voor](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)meer informatie en opnieuw proberen .

![Gebeurtenisrastermodel](https://docs.microsoft.com/azure/event-grid/media/overview/functional-model.png)

## <a name="serverless-state"></a>Serverloze status
Azure SignalR Service-gebeurtenissen zijn alleen actief wanneer clientverbindingen serverloos zijn. Over het algemeen, als een client niet naar een hubserver wordt doorgestuurd, gaat deze in de serverloze status. De klassieke modus werkt alleen wanneer de hub, waarmee clientverbindingen verbinding maken, geen hubserver heeft. De serverloze modus wordt echter aanbevolen om een probleem te voorkomen. Zie [Servicemodus kiezen](https://github.com/Azure/azure-signalr/blob/dev/docs/faq.md#what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose)voor meer informatie over de servicemodus .

## <a name="available-azure-signalr-service-events"></a>Beschikbare Azure SignalR-servicegebeurtenissen
Gebeurtenisraster gebruikt [gebeurtenisabonnementen](../event-grid/concepts.md#event-subscriptions) om gebeurtenisberichten door te sturen naar abonnees. Azure SignalR Service-gebeurtenisabonnementen ondersteunen twee soorten gebeurtenissen:  

|Naam van de gebeurtenis|Beschrijving|
|----------|-----------|
|`Microsoft.SignalRService.ClientConnectionConnected`|Verhoogd wanneer een clientverbinding is verbonden.|
|`Microsoft.SignalRService.ClientConnectionDisconnected`|Verhoogd wanneer de verbinding met een client wordt verbroken.|

## <a name="event-schema"></a>Gebeurtenisschema
Azure SignalR Service-gebeurtenissen bevatten alle informatie die u nodig hebt om te reageren op de wijzigingen in uw gegevens. U een Azure SignalR Service-gebeurtenis identificeren met de eigenschap eventType, die begint met 'Microsoft.SignalRService'. Additional information about the usage of Event Grid event properties is documented at [Event Grid event schema](../event-grid/event-schema.md).  

Hier is een voorbeeld van een clientverbinding verbonden gebeurtenis:
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

Zie het programma voor gebeurtenissen van [De SignalR Service](../event-grid/event-schema-azure-signalr.md)voor meer informatie .

## <a name="next-steps"></a>Volgende stappen

Meer informatie over gebeurtenisraster en Azure SignalR Service-gebeurtenissen proberen:

> [!div class="nextstepaction"]
> [Probeer een voorbeeld van gebeurtenisrasterintegratie met Azure SignalR-service](./signalr-howto-event-grid-integration.md)
> [over gebeurtenisraster](../event-grid/overview.md)
