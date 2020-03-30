---
title: Reageren op gebeurtenissen met de waarde van azure-app-configuratie
description: Gebruik Azure Event Grid om je te abonneren op gebeurtenissen voor app-configuratie.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 02/20/2020
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: a4f61d147ba1abf73ada6360b8d0d965d8e063a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523795"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reageren op gebeurtenissen voor Azure App-configuratie

Met azure-app-configuratiegebeurtenissen kunnen toepassingen reageren op wijzigingen in de belangrijkste waarden. Dit gebeurt zonder de noodzaak van ingewikkelde code of dure en inefficiënte polling diensten. In plaats daarvan worden gebeurtenissen via [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) doorgeschoven naar abonnees zoals Azure [Functions,](https://azure.microsoft.com/services/functions/) [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)of zelfs naar uw eigen aangepaste http-listener. Kritisch, je betaalt alleen voor wat je gebruikt.

Azure App Configuration-gebeurtenissen worden verzonden naar het Azure Event Grid, dat betrouwbare leveringsservices biedt aan uw toepassingen via uitgebreide retry-beleid en levering van dode letters. Zie [Berichtbezorging van gebeurtenisrasteren voor](https://docs.microsoft.com/azure/event-grid/delivery-and-retry)meer informatie en opnieuw proberen .

Veelvoorkomende gebeurtenisscenario's voor app-configuratie omvatten het vernieuwen van de toepassingsconfiguratie, het activeren van implementaties of een configuratiegerichte werkstroom. Wanneer wijzigingen zeldzaam zijn, maar uw scenario onmiddellijke responsiviteit vereist, kan architectuur op basis van gebeurtenissen bijzonder efficiënt zijn.

Bekijk [routeAzure App Configuration-gebeurtenissen naar een aangepast webeindpunt - CLI](./howto-app-configuration-event.md) voor een snel voorbeeld. 

![Gebeurtenisrastermodel](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Beschikbare azure-app-configuratiegebeurtenissen
Gebeurtenisraster gebruikt [gebeurtenisabonnementen](../event-grid/concepts.md#event-subscriptions) om gebeurtenisberichten door te sturen naar abonnees. Gebeurtenisabonnementen voor Azure App-configuratie kunnen twee typen gebeurtenissen bevatten:  

> |Naam van de gebeurtenis|Beschrijving|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Ontslagen wanneer een sleutelwaarde wordt gemaakt of vervangen|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Ontslagen wanneer een sleutelwaarde wordt verwijderd|

## <a name="event-schema"></a>Gebeurtenisschema
Azure App Configuration events bevatten alle informatie die u nodig hebt om te reageren op wijzigingen in uw gegevens. U een gebeurtenis app-configuratie identificeren omdat de eigenschap eventType begint met 'Microsoft.AppConfiguration'. Additional information about the usage of Event Grid event properties is documented in [Event Grid event schema](../event-grid/event-schema.md).  

> |Eigenschap|Type|Beschrijving|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |onderwerp|tekenreeks|Volledige Azure Resource Manager-id van de app-configuratie die de gebeurtenis uitzendt.|
> |Onderwerp|tekenreeks|De URI van de sleutelwaarde die het onderwerp van de gebeurtenis is.|
> |eventTime|tekenreeks|De datum/tijd waarop de gebeurtenis is gegenereerd, in de ISO 8601-indeling.|
> |eventType|tekenreeks|"Microsoft.appConfiguration.KeyValueModified" of "Microsoft.AppConfiguration.KeyValueDeleted".|
> |Id|tekenreeks|Een unieke id van deze gebeurtenis.|
> |dataVersion|tekenreeks|De schemaversie van het gegevensobject.|
> |metadataVersion|tekenreeks|De schemaversie van eigenschappen op het hoogste niveau.|
> |data|object|Verzameling van azure-app-configuratiespecifieke gebeurtenisgegevens|
> |data.key|tekenreeks|De sleutel van de sleutelwaarde die is gewijzigd of verwijderd.|
> |data.label|tekenreeks|Het label, indien aanwezig, van de sleutelwaarde die is gewijzigd of verwijderd.|
> |data.etag|tekenreeks|Voor `KeyValueModified` de etag van de nieuwe sleutelwaarde. Voor `KeyValueDeleted` de etag van de sleutelwaarde die is verwijderd.|

Hier is een voorbeeld van een KeyValueModified-gebeurtenis:
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

Zie Azure [App Configuration Events schema](../event-grid/event-schema-app-configuration.md)voor meer informatie .

## <a name="practices-for-consuming-events"></a>Praktijken voor het consumeren van gebeurtenissen
Toepassingen die gebeurtenissen in app-configuratie verwerken, moeten de volgende aanbevolen procedures volgen:
> [!div class="checklist"]
> * Meerdere abonnementen kunnen worden geconfigureerd om gebeurtenissen naar dezelfde gebeurtenishandler te leiden, dus ga er niet van uit dat gebeurtenissen van een bepaalde bron zijn. Controleer in plaats daarvan het onderwerp van het bericht om ervoor te zorgen dat de instantie App-configuratie de gebeurtenis verzendt.
> * Controleer de eventType en ga er niet van uit dat alle gebeurtenissen die u ontvangt, de typen zijn die u verwacht.
> * Gebruik de etag-velden om te begrijpen of uw informatie over objecten nog steeds up-to-date is.  
> * Gebruik de sequencervelden om de volgorde van gebeurtenissen op een bepaald object te begrijpen.
> * Gebruik het onderwerpveld om toegang te krijgen tot de sleutelwaarde die is gewijzigd.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over gebeurtenisraster en Azure App Configuration-gebeurtenissen proberen:

- [Over Event Grid](../event-grid/overview.md)
- [Azure App-configuratiegebeurtenissen routeren naar een aangepast webeindpunt](./howto-app-configuration-event.md)