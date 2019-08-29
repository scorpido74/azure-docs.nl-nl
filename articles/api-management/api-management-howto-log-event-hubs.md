---
title: Gebeurtenissen vastleggen in azure Event Hubs in azure API Management | Microsoft Docs
description: Meer informatie over het vastleggen van gebeurtenissen in azure Event Hubs in azure API Management.
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
ms.openlocfilehash: 646d9206ec82d5f35ccab9365e76276ff779d225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073492"
---
# <a name="how-to-log-events-to-azure-event-hubs-in-azure-api-management"></a>Gebeurtenissen vastleggen in azure Event Hubs in azure API Management
Azure Event Hubs is een zeer schaalbare service voor inkomende gegevens die miljoenen gebeurtenissen per seconde kan opnemen, voor verwerking en analyse van de enorme hoeveelheden gegevens die worden geproduceerd door verbonden apparaten en toepassingen. Event Hubs fungeert als de "front deur" voor een gebeurtenis pijplijn en wanneer gegevens worden verzameld in een Event Hub, kan deze worden getransformeerd en opgeslagen met behulp van een realtime analyse provider of batches/opslag adapters. Event Hubs koppelt de productie van een gebeurtenissenstroom los van het gebruik van deze gebeurtenissen, zodat de consumenten ervan toegang hebben tot de gebeurtenissen op basis van hun eigen planning.

Dit artikel is een aanvulling op de [integratie van azure API Management met Event hubs](https://azure.microsoft.com/documentation/videos/integrate-azure-api-management-with-event-hubs/) video en beschrijft hoe u API Management gebeurtenissen registreert met behulp van Azure Event hubs.

## <a name="create-an-azure-event-hub"></a>Een Azure Event Hub maken

Zie [een event hubs naam ruimte en een event hub maken met behulp van de Azure Portal](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)voor gedetailleerde stappen voor het maken van een event hub en het ophalen van verbindings reeksen die u nodig hebt om gebeurtenissen te verzenden en te ontvangen van en naar de Event hub.

## <a name="create-an-api-management-logger"></a>Een API Management logger maken
Nu u een event hub hebt, is de volgende stap het configureren van een [logger](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) in uw API Management-service zodat deze gebeurtenissen kan registreren in de Event hub.

API Management-logboeken worden geconfigureerd met behulp van de [API Management rest API](https://aka.ms/smapi). Voordat u de REST API voor de eerste keer gebruikt, moet u de [vereisten](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest) controleren en ervoor zorgen dat u [toegang tot de rest API hebt ingeschakeld](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/api-management-rest#EnableRESTAPI).

Als u een logger wilt maken, maakt u een HTTP PUT-aanvraag met behulp van de volgende URL-sjabloon:

`https://{your service}.management.azure-api.net/loggers/{new logger name}?api-version=2017-03-01`

* Vervang `{your service}` door de naam van uw API Management service-exemplaar.
* Vervang `{new logger name}` door de gewenste naam voor de nieuwe logboek registratie. U verwijst naar deze naam wanneer u het [logboek-naar-eventhub-](/azure/api-management/api-management-advanced-policies#log-to-eventhub) beleid configureert

Voeg de volgende headers toe aan de aanvraag:

* Content-type: Application/JSON
* Autorisatie SharedAccessSignature 58...
  * Voor instructies over het genereren `SharedAccessSignature` van de Zie [Azure API Management rest API-verificatie](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication).

Geef de aanvraag tekst op met behulp van de volgende sjabloon:

```json
{
  "loggerType" : "AzureEventHub",
  "description" : "Sample logger description",
  "credentials" : {
    "name" : "Name of the Event Hub from the portal",
    "connectionString" : "Endpoint=Event Hub Sender connection string"
    }
}
```

* `loggerType`moet worden ingesteld op `AzureEventHub`.
* `description`geeft een optionele beschrijving van het logboek en kan desgewenst een teken reeks met een lengte van nul zijn.
* `credentials`bevat de `name` en `connectionString` van uw Azure Event hub.

Wanneer u de aanvraag doet, `201 Created` wordt de status code geretourneerd als de logboek registratie is gemaakt. Hieronder ziet u een voor beeld van een reactie op basis van de bovenstaande voorbeeld aanvraag.

```json
{
    "id": "/loggers/{new logger name}",
    "loggerType": "azureEventHub",
    "description": "Sample logger description",
    "credentials": {
        "name": "Name of the Event Hub from the Portal",
        "connectionString": "{{Logger-Credentials-xxxxxxxxxxxxxxx}}"
    },
    "isBuffered": true,
    "resourceId": null
}
```

> [!NOTE]
> Zie [een logboek maken](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity#PUT)voor andere mogelijke retour codes en hun redenen. Zie de documentatie van de [traceer](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity) entiteit voor meer informatie over het uitvoeren van andere bewerkingen, zoals lijst, bijwerken en verwijderen.
>
>

## <a name="configure-log-to-eventhubs-policies"></a>Logboek-naar-Event hubs-beleid configureren

Zodra uw logboek is geconfigureerd in API Management, kunt u de beleids regels voor aanmelden op Event hubs configureren om de gewenste gebeurtenissen te registreren. Het logboek-naar-Event hubs-beleid kan worden gebruikt in de sectie inkomend beleid of in de sectie uitgaand beleid.

1. Blader naar de APIM-instantie.
2. Selecteer het tabblad API.
3. Selecteer de API waaraan u het beleid wilt toevoegen. In dit voor beeld voegen we een beleid toe aan de **echo-API** in het product Unlimited.
4. Selecteer **Alle bewerkingen**.
5. Selecteer boven aan het scherm het tabblad ontwerpen.
6. Klik in het venster inkomend of uitgaand verkeer op het drie hoekje (naast het potlood).
7. Selecteer de code-editor. Zie [beleid instellen of bewerken](set-edit-policies.md)voor meer informatie.
8. Plaats de cursor in de `inbound` sectie `outbound` of beleids regel.
9. Selecteer in het venster aan de rechter kant het **Geavanceerde beleid** > **logboek op EventHub**. Hiermee wordt de `log-to-eventhub` sjabloon voor de beleids verklaring ingevoegd.

```xml
<log-to-eventhub logger-id ='logger-id'>
  @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name))
</log-to-eventhub>
```
Vervang `logger-id` door de waarde `{new logger name}` die u in de URL hebt gebruikt om de logger in de vorige stap te maken.

U kunt elke expressie gebruiken die een teken reeks retourneert als de waarde van `log-to-eventhub` het element. In dit voor beeld wordt een teken reeks met de datum en tijd, de service naam, de aanvraag-id, het IP-adres van de aanvraag en de bewerkings naam vastgelegd.

Klik op **Opslaan** om de bijgewerkte beleids configuratie op te slaan. Zodra het beleid is opgeslagen, is het actief en worden gebeurtenissen geregistreerd in de aangewezen Event hub.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over Azure Event Hubs
  * [Aan de slag met Azure Event Hubs](../event-hubs/event-hubs-c-getstarted-send.md)
  * [Berichten ontvangen met EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md)
  * [Programmeerhandleiding voor Event Hubs](../event-hubs/event-hubs-programming-guide.md)
* Meer informatie over de integratie van API Management en Event Hubs
  * [Verwijzing naar traceer entiteit](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-logger-entity)
  * [Naslag informatie voor het aanmelden bij het eventhub-beleid](https://docs.microsoft.com/azure/api-management/api-management-advanced-policies#log-to-eventhub)
  * [Bewaak uw Api's met Azure API Management, Event Hubs en Moesif](api-management-log-to-eventhub-sample.md)  
* Meer informatie over [integratie met Azure-toepassing Insights](api-management-howto-app-insights.md)

[publisher-portal]: ./media/api-management-howto-log-event-hubs/publisher-portal.png
[create-event-hub]: ./media/api-management-howto-log-event-hubs/create-event-hub.png
[event-hub-connection-string]: ./media/api-management-howto-log-event-hubs/event-hub-connection-string.png
[event-hub-dashboard]: ./media/api-management-howto-log-event-hubs/event-hub-dashboard.png
[receiving-policy]: ./media/api-management-howto-log-event-hubs/receiving-policy.png
[sending-policy]: ./media/api-management-howto-log-event-hubs/sending-policy.png
[event-hub-policy]: ./media/api-management-howto-log-event-hubs/event-hub-policy.png
[add-policy]: ./media/api-management-howto-log-event-hubs/add-policy.png
