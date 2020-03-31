---
title: Onboarding van resources
description: Meer informatie over het uitvoeren van resource onboarding met Azure Custom Providers om beheer of configuratie toe te passen op andere Azure-brontypen.
author: jjbfour
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: jobreen
ms.openlocfilehash: 1846b036f12fe7e691021ec0248782cad946d9b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650407"
---
# <a name="azure-custom-providers-resource-onboarding-overview"></a>Overzicht van azure Custom Providers resource onboarding

Azure Custom Providers resource onboarding is een extensibiliteitsmodel voor Azure-brontypen. Hiermee u bewerkingen of beheer toepassen op bestaande Azure-resources op schaal. Zie [hoe Azure Custom Providers Azure kunnen uitbreiden](overview.md)voor meer informatie. In dit artikel wordt beschreven:

- Wat resource onboarding kan doen.
- Resource onboarding basics en hoe het te gebruiken.
- Waar gidsen en codevoorbeelden te vinden om aan de slag te gaan.

> [!IMPORTANT]
> Aangepaste providers is momenteel in openbare preview.
> Deze preview-versie wordt geleverd zonder een service-level overeenkomst, en we raden het niet aan voor productie workloads. Bepaalde functies worden mogelijk niet ondersteund of hebben mogelijk beperkte mogelijkheden.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

## <a name="what-can-resource-onboarding-do"></a>Wat kan resource onboarding doen?

Net als [bij aangepaste resources van Azure Custom Providers](./custom-providers-resources-endpoint-how-to.md)definieert resource onboarding een contract dat proxy 'onboarding' aanvragen naar een eindpunt. In tegenstelling tot aangepaste resources maakt resource onboarding geen nieuw resourcetype. In plaats daarvan maakt het de uitbreiding van bestaande resourcetypen mogelijk. En resource onboarding werkt met Azure Policy, zodat beheer en configuratie van resources op schaal kunnen worden uitgevoerd. Enkele voorbeelden van resource onboarding-workflows:

- Installeren en beheren op virtuele machine-extensies.
- Standaardinstellingen uploaden en configureren op Azure-opslagaccounts.
- Schakel diagnostische basislijninstellingen op schaal in.

## <a name="resource-onboarding-basics"></a>Basisbeginselen voor resourceonboarding

U configureert resource onboarding via Azure Custom Providers met behulp van Microsoft.CustomProviders/resourceProviders en Microsoft.CustomProviders/associations resourcetypes. Als u resource onboarding voor een aangepaste provider wilt inschakelen, maakt u tijdens het configuratieproces een **resourceType** met de naam 'koppelingen' met een **routingType** met 'Extensie'. De Microsoft.CustomProviders/-koppelingen en Microsoft.CustomProviders/resourceProviders hoeven niet tot dezelfde brongroep te behoren.

Hier is een voorbeeld van een aangepaste Azure-provider:

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

Eigenschap | Vereist? | Beschrijving
---|---|---
name | Ja | De naam van de eindpuntdefinitie. Voor resource onboarding moet de naam "associaties" zijn.
routingType | Ja | Hiermee bepaalt u het type contract met het eindpunt. Voor resource onboarding zijn de geldige **routeringstypen** 'Proxy,Cache, Extension' en 'Webhook,Cache, Extension'.
endpoint | Ja | Het eindpunt om de aanvragen naar te leiden. Dit zal de reactie en eventuele bijwerkingen van het verzoek te behandelen.

Nadat u de aangepaste provider hebt gemaakt met het resourcetype koppelingen, u targeten met Microsoft.CustomProviders/-koppelingen. Microsoft.CustomProviders/associations is een extensiebron die elke andere Azure-bron kan uitbreiden. Wanneer een instantie van Microsoft.CustomProviders/-koppelingen wordt gemaakt, wordt een **eigenschaptargetResourceId**nodig, die een geldige Microsoft.CustomProviders/resourceProviders of Microsoft.Solutions/applications resource ID moet zijn. In deze gevallen wordt de aanvraag doorgestuurd naar het resourcetype van koppelingen op het exemplaar Microsoft.CustomProviders/resourceProviders dat u hebt gemaakt.

> [!NOTE]
> Als een Microsoft.Solutions/applications resource ID wordt geleverd als de **targetResourceId,** moet er een Microsoft.CustomProviders/resourceProviders zijn geïmplementeerd in de beheerde resourcegroep met de naam 'openbaar'.

Voorbeeld van de koppeling Azure Custom Providers:

```JSON
{
  "properties": {
    "targetResourceId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
    ...
  }
}
```

Eigenschap | Vereist? | Beschrijving
---|---|---
targetResourceId | Ja | De bron-id van de Microsoft.CustomProviders/resourceProviders of Microsoft.Solutions/applications.

## <a name="how-to-use-resource-onboarding"></a>Resource onboarding gebruiken

Resource onboarding werkt door andere bronnen uit te breiden met de extensiebron Microsoft.CustomProviders/associations. In het volgende voorbeeld wordt de aanvraag voor een virtuele machine ingediend, maar elke resource kan worden uitgebreid.

Eerst moet u een aangepaste providerbron maken met een resourcetype voor koppelingen. Hiermee wordt de terugbel-URL aangegeven die wordt gebruikt wanneer een overeenkomstige Microsoft.CustomProviders/associations-bron wordt gemaakt, die zich richt op de aangepaste provider.

Voorbeeld van Microsoft.CustomProviders/resourceProviders maken aanvraag:

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

Nadat u de aangepaste provider hebt gemaakt, u andere bronnen targeten en de bijwerkingen van de aangepaste provider op deze bronnen toepassen.

Voorbeeld van Microsoft.CustomProviders/-koppelingen maken een aanvraag:

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

Deze aanvraag wordt vervolgens doorgestuurd naar het eindpunt dat is opgegeven in de aangepaste provider die u hebt gemaakt, waarnaar wordt verwezen door de **targetResourceId** in dit formulier:

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

Het eindpunt moet reageren met een `Content-Type` toepassing/json en een geldige JSON-responsinstantie. Velden die worden geretourneerd onder het **object Eigenschappen** van de JSON, worden toegevoegd aan de retourreactie van de koppeling.

## <a name="getting-help"></a>Ondersteuning vragen

Als u vragen hebt over de ontwikkeling van Azure Custom Resource Providers, probeert u deze te vragen op [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Een soortgelijke vraag kan al zijn beantwoord, dus controleer eerst voor het posten. Voeg de ```azure-custom-providers``` tag toe om snel te reageren!

## <a name="next-steps"></a>Volgende stappen

In dit artikel leerde u over aangepaste providers. Zie deze artikelen voor meer informatie:

- [Zelfstudie: Resource onboarding met aangepaste providers](./tutorial-resource-onboarding.md)
- [Zelfstudie: Aangepaste acties en resources maken in Azure](./tutorial-get-started-with-custom-providers.md)
- [Snelstart: een aangepaste resourceprovider maken en aangepaste resources implementeren](./create-custom-provider.md)
- [How to: Aangepaste acties toevoegen aan een Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [How to: Aangepaste resources toevoegen aan een Azure REST API](./custom-providers-resources-endpoint-how-to.md)
