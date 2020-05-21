---
title: Onboarding als een Azure Event Grid partner
description: Onboarding als een type Azure Event Grid partner onderwerp. Meer informatie over het resource model en de publicatie stroom voor de onderwerpen van partners.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 3c2c2e3d5a2ef48ddc212fc0df4906c91071d803
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725922"
---
# <a name="become-and-event-grid-partner"></a>Partner worden en Event Grid

In dit artikel wordt beschreven hoe u de resources van de Event Grid partner privé kunt gebruiken en hoe u een openbaar beschikbaar partner onderwerp type kunt worden.

U hebt geen speciale machtiging nodig om te beginnen met het gebruik van de Event Grid resource typen die zijn gekoppeld aan publicatie gebeurtenissen als een Event Grid partner. U kunt ze vandaag ook gebruiken om gebeurtenissen privé te publiceren naar uw eigen Azure-abonnementen en om het resource model te testen als u overweegt een partner te worden.

## <a name="becoming-an-event-grid-partner"></a>Een Event Grid partner worden

Als u een open bare Event Grid partner wilt worden, moet u eerst [dit formulier](https://aka.ms/gridpartnerform)invullen en vervolgens contact opnemen met het event grid team op [GridPartner@microsoft.com](mailto:gridpartner@microsoft.com) .

## <a name="how-partner-topics-work"></a>Hoe partner onderwerpen werken
De onderwerpen van partners nemen de bestaande architectuur die Event Grid al gebruikt voor het publiceren van gebeurtenissen van Azure-resources, zoals opslag en IoT Hub, en maakt deze hulpprogram ma's openbaar voor iedereen die ze kunnen gebruiken. Het gebruik van deze hulpprogram ma's is standaard alleen privé voor uw Azure-abonnement. Als u uw evenementen openbaar beschikbaar wilt maken, vult u het bovenstaande formulier in en [neemt u contact op met het event grid team](mailto:gridpartner@microsoft.com).

Met partner onderwerpen kunt u gebeurtenissen publiceren naar Azure Event Grid voor het gebruik van meerdere tenants.

### <a name="onboarding-and-event-publishing-overview"></a>Overzicht van het voorbereiden en publiceren van gebeurtenissen

#### <a name="partner-flow"></a>Partner stroom

1. Maak een Azure-Tenant als u er nog geen hebt.
1. Met CLI een nieuwe Event Grid maken `partnerRegistration` . Deze bron bevat informatie zoals weergave naam, beschrijving, installatie-URI, enzovoort.

    ![Partner onderwerp maken](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Maak een of meer `partnerNamespaces` in elke regio waarvoor u gebeurtenissen wilt publiceren. Als onderdeel hiervan richt Event Grid-Service een publicatie-eind punt in (bijvoorbeeld, https://contoso.westus-1.eventgrid.azure.net/api/events) en toegangs sleutels.

    ![Partner naam ruimte maken](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Bieden klanten een manier om zich te registreren in uw systeem en dat ze graag een partner onderwerp zouden moeten hebben.
1. Neem contact op met het Event Grid team om ons te laten weten dat het type partner onderwerp openbaar moet worden.

#### <a name="customer-flow"></a>Klant stroom

1. Uw klant gaat naar de Azure Portal om te zien wat de Azure-abonnements-ID en de resource groep zijn voor het partner onderwerp dat wordt gemaakt in.
1. De klant vraagt een partner onderwerp op via uw systeem. Als antwoord maakt u een gebeurtenis tunnel van uw partner naam ruimte.
1. Event Grid maakt een in **behandeling zijnde** partner onderwerp in het Azure-abonnement en de resource groep van de klant.

    ![Gebeurtenis kanaal maken](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. De klant activeert het partner onderwerp via de Azure Portal. Gebeurtenissen kunnen nu vanuit uw service worden verwerkt naar het Azure-abonnement van de klant.

    ![Partner onderwerp activeren](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Resource model

Hieronder ziet u het resource model voor onderwerpen over partners.

### <a name="partner-registrations"></a>Partner registraties
* Resource`partnerRegistrations`
* Gebruikt door: partners
* Beschrijving: Hiermee worden de algemene meta gegevens van de SaaS-partner vastgelegd (bijvoorbeeld naam, weergave naam, beschrijving en installatie-URI).
    
    Het maken/bijwerken van een partner registratie is een zelfbesparende bewerking voor de partners. Met deze selfservice mogelijkheid kunnen partners de volledige end-to-end-stroom bouwen en testen.
    
    Alleen door micro soft goedgekeurde partnerRegistrations kunnen door klanten worden gedetecteerd.
* Bereik: gemaakt in het Azure-abonnement van de partner. Meta gegevens worden weer gegeven aan klanten wanneer ze openbaar zijn.

### <a name="partner-namespaces"></a>Partner naam ruimten
* Resource: partnerNamespaces
* Gebruikt door: partners
* Beschrijving: biedt een regionale resource voor het publiceren van klant gebeurtenissen naar. Elke partner naam ruimte heeft een publicatie-eind punt en verificatie sleutels. De naam ruimte is ook de manier waarop de partner een partner onderwerp voor een bepaalde klant aanvraagt en de actieve klanten vermeldt.
* Bereik: levens duur in het abonnement van de partner.

### <a name="event-channel"></a>Gebeurtenis kanaal
* Resource`partnerNamespaces/eventChannels`
* Gebruikt door: partners
* Beschrijving: de gebeurtenis tunnels zijn een mirror van het partner onderwerp van de klant. Door een gebeurtenis tunnel te maken en het Azure-abonnement en de resource groep van de klant op te geven in de meta gegevens, geeft u aan Event Grid een partner onderwerp voor de klant te maken. Event Grid wordt een ARM-aanroep voor het maken van een bijbehorende partnerTopic in het abonnement van de klant. Het partner onderwerp wordt gemaakt met de status ' in behandeling '. Er is een 1-1-koppeling tussen elke eventTunnel en een partnerTopic.
* Bereik: levens duur in het abonnement van de partner.

### <a name="partner-topics"></a>Onderwerpen over partners
* Resource`partnerTopics`
* Gebruikt door: klanten
* Beschrijving: partner onderwerpen zijn vergelijkbaar met het aangepaste onderwerp en het onderwerp System in Event Grid. Elk onderwerp van de partner is gekoppeld aan een specifieke bron (bijvoorbeeld `Contoso:myaccount` ) en een specifiek type partner onderwerp (bijvoorbeeld ' Contoso '). Klanten maken gebeurtenis abonnementen op het partner onderwerp voor het door sturen van gebeurtenissen naar verschillende gebeurtenis-handlers.

    Klanten kunnen deze resource niet rechtstreeks maken. De enige manier om een partner onderwerp te maken, is via een partner bewerking om een event tunnel te maken.
* Bereik: leven in het abonnement van de klant.

### <a name="partner-topic-types"></a>Typen partner onderwerpen
* Resource`partnerTopicTypes`
* Gebruikt door: klanten
* Beschrijving: typen partners voor de hele Tenant zijn bron typen voor het gehele netwerk waarmee klanten de lijst met goedgekeurde partner typen kunnen detecteren. De URL ziet er als volgt uit:https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Bereik: globaal

## <a name="publishing-events-to-event-grid"></a>Gebeurtenissen publiceren naar Event Grid
Wanneer u een partnerNamespace in een Azure-regio maakt, krijgt u een regionaal eind punt en de bijbehorende verificatie sleutels. Publiceer batches van gebeurtenissen naar dit eind punt voor alle gebeurtenis tunnels van klanten in die naam ruimte. Op basis van het veld bron in de gebeurtenis, wijst Azure Event Grid elke gebeurtenis toe aan de bijbehorende partner onderwerp (en).

### <a name="event-schema-cloudevents-v10"></a>Gebeurtenis schema: CloudEvents v 1.0
Gebeurtenissen publiceren naar Azure Event Grid met behulp van het CloudEvents 1,0-schema. Event Grid ondersteunt zowel de gestructureerde modus als de batch modus. CloudEvents 1,0 is het enige ondersteunde gebeurtenis schema voor partner naam ruimten.

### <a name="example-flow"></a>Voorbeeld stroom

1.  De publicatie service voert een HTTP POST naar uit https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01 .
2.  Neem in de aanvraag een header-waarde met de naam AEG-SAS-sleutel op die een sleutel voor verificatie bevat. Deze sleutel is ingericht tijdens het maken van de partnerNamespace. Een geldige header-waarde is bijvoorbeeld AEG-SAS-Key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg = =.
3.  Stel de content-type-header in op ' Application/cloudevents-batch + JSON; charset = UTF-8.
4.  Voer een HTTP POST naar de bovenstaande publicatie-URL uit met een batch-gebeurtenis die overeenkomt met die regio. Bijvoorbeeld:

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

Nadat u naar het partnerNamespace-eind punt hebt gepost, ontvangt u een antwoord. Het antwoord is een standaard-HTTP-antwoord code. Enkele veelvoorkomende reacties zijn:

| Resultaat                             | Antwoord              |
|------------------------------------|-----------------------|
| Geslaagd                            | 200 OK                |
| De gebeurtenis gegevens hebben een onjuiste indeling    | 400 ongeldige aanvraag       |
| Ongeldige toegangs sleutel                 | 401 niet gemachtigd      |
| Onjuist eind punt                 | 404 Niet gevonden         |
| Matrix of gebeurtenis overschrijdt grootte limieten | 413 Payload is te groot |

## <a name="reference"></a>Verwijzing

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [ARM-sjabloon](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)
  * [ARM-sjabloon schema](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [REST-API’s](https://docs.microsoft.com/rest/api/eventgrid/partnernamespaces)
  * [CLI-extensie](https://docs.microsoft.com/cli/azure/ext/eventgrid/?view=azure-cli-latest)

### <a name="sdks"></a>SDK's
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [OK](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Volgende stappen
- [Overzicht van de onderwerpen over partners](partner-topics-overview.md)
- [Formulier voor het voorbereiden van partner onderwerpen](https://aka.ms/gridpartnerform)
- [Auth0-partner onderwerp](auth0-overview.md)
- [Het Auth0-partner onderwerp gebruiken](auth0-how-to.md)
