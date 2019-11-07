---
title: Resource voor onboarding van Azure Custom providers
description: Meer informatie over het onboarden van resources met behulp van aangepaste Azure-providers om beheer of configuratie toe te passen op andere Azure-resource typen.
author: jjbfour
ms.service: managed-applications
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 6f9dcf4118dd2167f4cef35408d5ead79bf33877
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73609149"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Overzicht van de resource voor het onboarding van Azure Custom providers

Het onboarden van resources van Azure Custom providers is een uitbreidings model voor Azure-resource typen. Zo kunt u bewerkingen of beheer Toep assen op bestaande Azure-resources op schaal. Zie How to extend Azure [Custom providers Azure](./custom-providers-overview.md)voor meer informatie. Deze documentatie beschrijft:

- Wat kan onboarding van resources doen.
- Basis beginselen voor het onboarding van resources en hoe u deze kunt gebruiken.
- Waar u gidsen en code voorbeelden kunt vinden om aan de slag te gaan.

> [!IMPORTANT]
> Aangepaste providers zijn momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="what-can-resource-onboarding-do"></a>Wat kan onboarding van resources doen

Net als bij [aangepaste Azure Custom provider-resources](./custom-providers-resources-endpoint-how-to.md), wordt in de bron onboarding een contract gedefinieerd, waarmee aanvragen voor het uitvoeren van een proxy voor een eind punt worden uitgevoerd. In tegens telling tot aangepaste resources maakt resource-onboarding geen nieuw resource type, maar staat de uitbrei ding van bestaande resource typen toe. Daarnaast werkt de voor bereiding van resources met Azure Policy, zodat het beheer en de configuratie van resources op schaal kunnen worden uitgevoerd. Enkele voor beelden van werk stromen voor onboarding van resources:

- Installeren en beheren op Virtual Machines-extensies.
- Upload en configureer standaard instellingen voor Azure Storage accounts.
- De basis instellingen voor diagnostische gegevens op schaal inschakelen.

## <a name="resource-onboarding-basics"></a>Basis beginselen van resources voor onboarding

Het onboarden van resources wordt geconfigureerd via aangepaste Azure-providers met de resource typen micro soft. CustomProviders/resourceProviders en micro soft. CustomProviders/Associations. Als u het voorbereiden van resources voor een aangepaste provider wilt inschakelen, maakt u tijdens het configuratie proces een **resource type** met de naam ' Associations ' met een **RoutingType** dat ' extension ' bevat. Daarnaast hoeven de ' micro soft. CustomProviders/Associations ' en ' micro soft. CustomProviders/resourceProviders ' niet tot dezelfde resource groep te behoren.

Voor beeld van een aangepaste Azure-provider:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://microsoft.com/"
      }
    ]
  },
  "location": "eastus"
}
```

Eigenschap | Vereist | Beschrijving
---|---|---
naam | *klikt* | De naam van de eindpunt definitie. Voor het onboarden van de bron moet de naam ' Associations ' zijn.
routingType | *klikt* | Bepaalt het contract type met het **eind punt**. Voor onboarding van resources zijn de geldige **routingTypes** ' proxy, cache, extensie ' en ' webhook, cache, extensie '.
endpoint | *klikt* | Het eind punt waarnaar de aanvragen worden gerouteerd. Hiermee worden de reactie en eventuele neven effecten van de aanvraag verwerkt.

Zodra de aangepaste provider met het resource type ' Association ' is gemaakt, kunt u het doel bereiken met ' micro soft. CustomProviders/Associations '. ' Micro soft. CustomProviders/Associations ' is een uitbreidings resource waarmee elke andere Azure-resource kan worden uitgebreid. Wanneer een exemplaar van ' micro soft. CustomProviders/Associations ' wordt gemaakt, wordt er een eigenschap **targetResourceId**, die een geldige resource-id ' micro soft. CustomProviders/resourceProviders ' of ' micro soft. Solutions/Applications ' is. In deze gevallen wordt de aanvraag doorgestuurd naar het resource type ' Association ' op het exemplaar ' micro soft. CustomProviders/resourceProviders ' dat we hebben gemaakt.

> [!Note]
> Als de resource-ID ' micro soft. Solutions/Applications ' is ingesteld als **targetResourceId**, moet er een ' micro soft. CustomProviders/resourceProviders ' zijn geïmplementeerd in de beheerde resource groep met de naam ' Public '.

Voor beeld van een aangepaste Azure-provider koppeling:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Eigenschap | Vereist | Beschrijving
---|---|---
TargetResourceId | *klikt* | De resource-ID van de ' micro soft. CustomProviders/resourceProviders ' of ' micro soft. Solutions/Applications '.

## <a name="how-to-use-resource-onboarding"></a>Het gebruik van resource-onboarding

Het onboarden van resources werkt door andere resources uit te breiden met de extensie resource ' micro soft. CustomProviders/Associations '. In het volgende voor beeld wordt de aanvraag ingediend voor een virtuele machine, maar een resource kan worden uitgebreid.

U moet eerst een resource van een aangepaste provider met het resource type ' Association ' maken. Hiermee wordt de call back-URL gedeclareerd die wordt gebruikt wanneer een bijbehorende resource ' micro soft. CustomProviders/Associations ' wordt gemaakt, die de aangepaste provider verzorgt.

Voor beeld van ' micro soft. CustomProviders/resourceProviders '-aanvraag maken:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "resourceTypes": [
      {
        "name": "associations",
        "routingType": "Proxy,Cache,Extension",
        "endpoint": "https://{myCustomEndpoint}/"
      }
    ]
  },
  "location": "{location}"
}
```

Nadat de aangepaste provider is gemaakt, kunnen we nu andere resources richten en de neven effecten van de aangepaste provider hierop Toep assen.

Voor beeld van ' micro soft. CustomProviders/Associations '-aanvraag maken:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Deze aanvraag wordt vervolgens doorgestuurd naar het eind punt dat is opgegeven in de eerste gemaakte aangepaste provider, waarnaar wordt verwezen door de targetResourceId in de vorm:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/associations/{associationName}
X-MS-CustomProviders-ExtensionPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}/providers/Microsoft.CustomProviders/associations/{associationName}
X-MS-CustomProviders-ExtendedResource: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{virtualMachineName}

{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
  }
}
```

Het eind punt moet reageren met de `Content-Type` application/json en een geldige JSON-antwoord tekst. Velden die worden geretourneerd onder het object eigenschappen van de JSON, worden toegevoegd aan de Association retour-antwoord.

## <a name="looking-for-help"></a>Zoeken naar Help

Als u vragen hebt over de ontwikkeling van de aangepaste Azure-resource provider, kunt u de vragen stellen op [stack overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Er is mogelijk al een vergelijk bare vraag gesteld en beantwoord, dus controleer eerst vóór het boeken. Voeg de tag ```azure-custom-providers``` toe om snel antwoord te krijgen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over aangepaste providers. Ga naar het volgende artikel om een aangepaste provider te maken.

- [Zelf studie: onboarding van resources met aangepaste providers](./tutorial-custom-providers-resource-onboarding.md)
- [Zelf studie: aangepaste acties en resources maken in azure](./tutorial-custom-providers-101.md)
- [Snelstartgids: een aangepaste Azure-resource provider maken en aangepaste resources implementeren](./create-custom-provider.md)
- [Procedure: aangepaste acties toevoegen aan Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Procedure: aangepaste resources toevoegen aan Azure REST API](./custom-providers-resources-endpoint-how-to.md)
