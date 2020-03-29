---
title: Gebeurtenissen aanmelden bij Azure Event Hubs in Azure API Management | Microsoft Documenten
description: Meer informatie over het aanmelden van gebeurtenissen op Azure Event Hubs in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 88f6507d-7460-4eb2-bffd-76025b73f8c4
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/29/2018
ms.author: apimpm
ms.openlocfilehash: 2f07f6a27e78ee4df8c64a09918758d02c28c6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76898784"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Gebeurtenissen aanmelden bij Azure Event Hubs in Azure API-beheer
Azure Event Hubs is een zeer schaalbare service voor inkomende gegevens die miljoenen gebeurtenissen per seconde kan opnemen, voor verwerking en analyse van de enorme hoeveelheden gegevens die worden geproduceerd door verbonden apparaten en toepassingen. Event Hubs fungeert als de "voordeur" voor een gebeurtenispijplijn en zodra gegevens worden verzameld in een gebeurtenishub, kan deze worden getransformeerd en opgeslagen met behulp van realtime analyseprovider of batching/storage-adapters. Event Hubs koppelt de productie van een gebeurtenissenstroom los van het gebruik van deze gebeurtenissen, zodat de consumenten ervan toegang hebben tot de gebeurtenissen op basis van hun eigen planning.

Dit artikel is een aanvulling op de video [Azure API Management integreren met Event Hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) en beschrijft hoe API-beheergebeurtenissen kunnen worden logboeken met Azure Event Hubs.

## <a name="create-an-azure-event-hub"></a>Een Azure Event Hub maken

Zie Een naamruimte voor gebeurtenishubs maken [en een gebeurtenishub maken met de Azure-portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)voor gedetailleerde stappen over het maken van een gebeurtenishub en het ophalen van verbindingstekenreeksen die u moet verzenden en ontvangen van gebeurtenissen van en naar de gebeurtenishub.

## <a name="create-an-api-management-logger"></a>Een API-beheerlogger maken
Nu u een Event Hub hebt, is de volgende stap het configureren van een [Logger](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger) in uw API Management-service, zodat gebeurtenissen kunnen worden aangemeld bij de Gebeurtenishub.

API Management loggers zijn geconfigureerd met behulp van de [API Management REST API](https://aka.ms/apimapi). Voor gedetailleerde aanvraagvoorbeelden u zien [hoe u Loggers maakt.](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger/createorupdate)

## <a name="configure-log-to-eventhubs-policies"></a>Beleid voor log-to-eventhubs configureren

Zodra uw logger is geconfigureerd in API-beheer, u uw beleid voor log-to-eventhubs configureren om de gewenste gebeurtenissen te registreren. Het beleid log-to-eventhubs kan worden gebruikt in de sectie binnenkomend beleid of in de sectie uitgaand beleid.

1. Blader naar de APIM-instantie.
2. Selecteer het tabblad API.
3. Selecteer de API waaraan u het beleid wilt toevoegen. In dit voorbeeld voegen we een beleid toe aan de **Echo API** in het **Onbeperkte** product.
4. Selecteer **Alle bewerkingen**.
5. Selecteer boven aan het scherm het tabblad Ontwerpen.
6. Klik in het venster Binnenkomende of uitgaande verwerking op het driehoekje (naast het potlood).
7. Selecteer de codeeditor. Zie [Beleid instellen of bewerken](set-edit-policies.md)voor meer informatie .
8. Plaats de cursor `inbound` `outbound` in de sectie of beleid.
9. Selecteer in het venster aan de rechterkant **Geavanceerd beleid** > **Log naar EventHub**. Hiermee wordt `log-to-eventhub` de sjabloon beleidsverklaring ingevoegd.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Vervang `logger-id` de waarde waarvoor `{new logger name}` u in de URL hebt gebruikt om de logger in de vorige stap te maken.

U elke expressie gebruiken die een `log-to-eventhub` tekenreeks retourneert als de waarde voor het element. In dit voorbeeld wordt een tekenreeks met de datum en tijd, servicenaam, aanvraag-id, ip-adres aanvragen en de naam van de bewerking geregistreerd.

Klik **op Opslaan** om de bijgewerkte beleidsconfiguratie op te slaan. Zodra het is opgeslagen, is het beleid actief en worden gebeurtenissen aangemeld bij de aangewezen gebeurtenishub.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over Azure Event Hubs
  * [Aan de slag met Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Berichten ontvangen met EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Programmeerhandleiding voor gebeurtenishubs](../event-hubs/event-hubs-programming-guide.md)
* Meer informatie over API-beheer en eventhubs-integratie
  * [Referentie van loggerentiteit](https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/logger)
  * [log-to-eventhub-beleidsverwijzing](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Uw API's controleren met Azure API-beheer, gebeurtenishubs en Moesif](api-management-log-to-eventhub-sample.md)  
* Meer informatie over [integratie met Azure Application Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
