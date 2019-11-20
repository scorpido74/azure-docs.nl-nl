---
title: Reageren op Azure-app configuratie sleutel-waarde gebeurtenissen | Microsoft Docs
description: Gebruik Azure Event Grid om u te abonneren op app-configuratie gebeurtenissen.
services: azure-app-configuration,event-grid
author: jimmyca
ms.author: jimmyca
ms.date: 05/30/2019
ms.topic: article
ms.service: azure-app-configuration
ms.openlocfilehash: 5da64155f2823712eee7a60427b1c1e80abec068
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185286"
---
# <a name="reacting-to-azure-app-configuration-events"></a>Reageren op Azure-app configuratie gebeurtenissen

Met Azure-app configuratie gebeurtenissen kunnen toepassingen reageren op wijzigingen in sleutel waarden. Dit gebeurt zonder dat er complexe code of dure en inefficiënte polling services nodig zijn. In plaats daarvan worden gebeurtenissen gepusht via [Azure Event grid](https://azure.microsoft.com/services/event-grid/) naar abonnees zoals [Azure functions](https://azure.microsoft.com/services/functions/), [Azure Logic apps](https://azure.microsoft.com/services/logic-apps/)of zelfs naar uw eigen aangepaste HTTP-listener en betaalt u alleen voor wat u gebruikt.

Azure-app configuratie gebeurtenissen worden verzonden naar de Azure Event Grid die betrouw bare leverings services beschikbaar maakt voor uw toepassingen via een uitgebreid beleid voor opnieuw proberen en levering van onbestelbare berichten. Zie [Event grid aflevering van berichten en probeer het opnieuw](https://docs.microsoft.com/azure/event-grid/delivery-and-retry).

Algemene scenario's voor app-configuratie gebeurtenissen zijn onder andere het vernieuwen van de toepassings configuratie, het activeren van implementaties of een configuratie gerichte werk stroom. Als de wijzigingen niet worden doorgevoerd, maar voor uw scenario een onmiddellijke reactie nodig is, kan de architectuur op basis van gebeurtenissen bijzonder efficiënt zijn.

Bekijk de [Route Azure-app configuratie gebeurtenissen naar een aangepast webeindpunt-cli](./howto-app-configuration-event.md) voor een kort voor beeld. 

![Event Grid model](./media/event-grid-functional-model.png)

## <a name="available-azure-app-configuration-events"></a>Beschik bare Azure-app configuratie gebeurtenissen
Event grid gebruikt [gebeurtenis abonnementen](../event-grid/concepts.md#event-subscriptions) om gebeurtenis berichten te routeren naar abonnees. Azure-app configuratie gebeurtenis abonnementen kunnen twee typen gebeurtenissen bevatten:  

> |Gebeurtenis naam|Beschrijving|
> |----------|-----------|
> |`Microsoft.AppConfiguration.KeyValueModified`|Wordt geactiveerd wanneer een sleutel waarde wordt gemaakt of vervangen|
> |`Microsoft.AppConfiguration.KeyValueDeleted`|Wordt geactiveerd wanneer een sleutel waarde wordt verwijderd|

## <a name="event-schema"></a>Gebeurtenisschema
Azure-app configuratie gebeurtenissen bevatten alle informatie die u nodig hebt om te reageren op wijzigingen in uw gegevens. U kunt een gebeurtenis voor de configuratie van een app identificeren omdat de eigenschap Event type begint met ' micro soft. AppConfiguration '. Meer informatie over het gebruik van Event Grid gebeurtenis eigenschappen wordt beschreven in [Event grid-gebeurtenis schema](../event-grid/event-schema.md).  

> |Eigenschap|Type|Beschrijving|
> |-------------------|------------------------|-----------------------------------------------------------------------|
> |topic|tekenreeks|Volledige Azure Resource Manager-id van de app-configuratie waarmee de gebeurtenis wordt verzonden.|
> |subject|tekenreeks|De URI van de sleutel waarde die het onderwerp van de gebeurtenis is.|
> |eventTime|tekenreeks|De datum/tijd waarop de gebeurtenis is gegenereerd, in ISO 8601-indeling.|
> |eventType|tekenreeks|"Micro soft. AppConfiguration. KeyValueModified" of "micro soft. AppConfiguration. KeyValueDeleted".|
> |Id|tekenreeks|Een unieke id van deze gebeurtenis.|
> |dataVersion|tekenreeks|De schema versie van het gegevens object.|
> |metadataVersion|tekenreeks|De schema versie van de eigenschappen op het hoogste niveau.|
> |data|object|Verzameling van Azure-app configuratie-specifieke gebeurtenis gegevens|
> |data. key|tekenreeks|De sleutel van de sleutel waarde die is gewijzigd of verwijderd.|
> |data.label|tekenreeks|Het label, indien van toepassing, van de sleutel waarde die is gewijzigd of verwijderd.|
> |data. ETAG|tekenreeks|Voor `KeyValueModified` van de ETAG van de nieuwe sleutel waarde. Voor `KeyValueDeleted` van de ETAG van de sleutel waarde die is verwijderd.|

Hier volgt een voor beeld van een KeyValueModified-gebeurtenis:
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

Zie [Azure-app schema configuratie gebeurtenissen](../event-grid/event-schema-app-configuration.md)voor meer informatie.

## <a name="practices-for-consuming-events"></a>Procedures voor het gebruiken van gebeurtenissen
Toepassingen die app-configuratie gebeurtenissen verwerken, moeten een aantal aanbevolen procedures volgen:
> [!div class="checklist"]
> * Omdat meerdere abonnementen kunnen worden geconfigureerd voor het routeren van gebeurtenissen naar dezelfde gebeurtenis-handler, is het belang rijk om te voor komen dat gebeurtenissen afkomstig zijn uit een bepaalde bron, maar om het onderwerp van het bericht te controleren om ervoor te zorgen dat het afkomstig is van de app-configuratie die u verwacht.
> * Controleer ook of de Event type is ingesteld als een voor bereiding op het proces en ga er niet van uit dat alle gebeurtenissen die u ontvangt, de verwachte typen zijn.
> * Wanneer berichten in aflopende volg orde en na enige vertraging kunnen aankomen, gebruikt u de ETAG-velden om te begrijpen of uw informatie over objecten nog steeds actueel is.  Gebruik ook de sequencer-velden om de volg orde van gebeurtenissen op een bepaald object te begrijpen.
> * Gebruik het onderwerpveld om toegang te krijgen tot de sleutel waarde die is gewijzigd.


## <a name="next-steps"></a>Volgende stappen

Meer informatie over Event Grid en het geven van Azure-app configuratie gebeurtenissen een try:

- [Over Event Grid](../event-grid/overview.md)
- [Azure-app configuratie gebeurtenissen naar een aangepast eind punt van een web routeren](./howto-app-configuration-event.md)