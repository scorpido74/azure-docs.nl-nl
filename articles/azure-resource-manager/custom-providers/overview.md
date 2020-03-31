---
title: Overzicht van aangepaste providers
description: Meer informatie over Azure Custom Resource Providers en hoe u het Azure API-vlak uitbreidt naar uw werkstromen.
author: jjbfour
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: fd21117219ea3db6946e7a1b889d92702af65b58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650485"
---
# <a name="azure-custom-resource-providers-overview"></a>Overzicht van Azure Custom Resource Providers

Azure Custom Resource Providers is een uitbreidbaarheidsplatform voor Azure. Hiermee u aangepaste API's definiëren die kunnen worden gebruikt om de standaardAzure-ervaring te verrijken. In deze documentatie wordt beschreven:

- Een Azure Custom Resource Provider bouwen en implementeren.
- Azure Custom Resource Providers gebruiken om bestaande werkstromen uit te breiden.
- Waar gidsen en codevoorbeelden te vinden om aan de slag te gaan.

![Overzicht van aangepaste provider](./media/overview/overview.png)

> [!IMPORTANT]
> Aangepaste providers is momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

## <a name="what-can-custom-resource-providers-do"></a>Wat kunnen aangepaste resourceproviders doen

Hier volgen enkele voorbeelden van wat u bereiken met Azure Custom Resource Providers:

- Breid de REST API van Azure Resource Manager uit met interne en externe services.
- Aangepaste scenario's inschakelen bovenop bestaande Azure-werkstromen.
- Het besturingselement en het effect azure resource beheersjablonen aanpassen.

## <a name="what-is-a-custom-resource-provider"></a>Wat is een aangepaste resourceprovider

Azure Custom Resource Providers worden gemaakt door een contract te maken tussen Azure en een eindpunt. In dit contract wordt een lijst met nieuwe bronnen en acties gedefinieerd via een nieuwe bron, **Microsoft.CustomProviders/resourceProviders.** De aangepaste resourceprovider stelt deze nieuwe API's vervolgens bloot in Azure. Azure Custom Resource Providers bestaan uit drie delen: aangepaste resourceprovider, **eindpunten**en aangepaste resources.

## <a name="how-to-build-custom-resource-providers"></a>Aangepaste resourceproviders bouwen

Aangepaste resourceproviders zijn een lijst met contracten tussen Azure en eindpunten. In dit contract wordt beschreven hoe Azure moet omgaan met een eindpunt. De resourceprovider werkt als een proxy en stuurt aanvragen en antwoorden door naar en van het opgegeven **eindpunt.** Een resourceprovider kan twee soorten contracten opgeven: [**resourcetypen**](./custom-providers-resources-endpoint-how-to.md) en [**acties**](./custom-providers-action-endpoint-how-to.md). Deze zijn ingeschakeld door middel van endpoint definities. Een eindpuntdefinitie bestaat uit drie velden: **naam,** **routingType**en **eindpunt**.

Voorbeeldeindpunt:

```JSON
{
  "name": "{endpointDefinitionName}",
  "routingType": "Proxy",
  "endpoint": "https://{endpointURL}/"
}
```

Eigenschap | Vereist | Beschrijving
---|---|---
name | *Ja* | De naam van de eindpuntdefinitie. Azure stelt deze naam bloot via de API onder '/abonnementen/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/<br>resourceProviders/{resourceProviderName}/{endpointDefinitionName}'
routingType | *nee* | Hiermee bepaalt u het contracttype met het **eindpunt**. Als dit niet is opgegeven, wordt deze standaard 'Proxy' weergegeven.
endpoint | *Ja* | Het eindpunt om de aanvragen naar te leiden. Dit zal omgaan met de reactie en eventuele bijwerkingen van het verzoek.

### <a name="building-custom-resources"></a>Aangepaste resources bouwen

**ResourceTypes** beschrijven nieuwe aangepaste resources die aan Azure worden toegevoegd. Deze leggen basisRESTful CRUD-methoden bloot. Meer [informatie over het maken van aangepaste bronnen](./custom-providers-resources-endpoint-how-to.md)

Voorbeeld van aangepaste resourceprovider met **resourcetypen:**

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

API's die aan Azure zijn toegevoegd voor het bovenstaande voorbeeld:

HttpMethod | Voorbeeld VAN URI | Beschrijving
---|---|---
PUT | /abonnementen/{subscriptionId}/resourceGroepen/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | De Azure REST API-aanroep om een nieuwe bron te maken.
DELETE | /abonnementen/{subscriptionId}/resourceGroepen/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | De Azure REST API-aanroep om een bestaande bron te verwijderen.
GET | /abonnementen/{subscriptionId}/resourceGroepen/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{customResourceName}?api-version=2018-09-01-preview | De Azure REST API-aanroep om een bestaande bron op te halen.
GET | /abonnementen/{subscriptionId}/resourceGroepen/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources?api-version=2018-09-01-preview | De Azure REST API-aanroep om de lijst met bestaande resources op te halen.

### <a name="building-custom-actions"></a>Aangepaste acties bouwen

**Acties** beschrijven nieuwe acties die zijn toegevoegd aan Azure. Deze kunnen worden blootgesteld op de top van de resource provider of genest onder een **resourceType**. Meer [informatie over het maken van aangepaste acties](./custom-providers-action-endpoint-how-to.md)

Voorbeeld van aangepaste resourceprovider met **acties:**

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

API's die aan Azure zijn toegevoegd voor het bovenstaande voorbeeld:

HttpMethod | Voorbeeld VAN URI | Beschrijving
---|---|---
POST | /abonnementen/{subscriptionId}/resourceGroepen/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomAction?api-version=2018-09-01-preview | De Azure REST API-aanroep om de actie te activeren.

## <a name="looking-for-help"></a>Op zoek naar hulp

Als u vragen hebt over de ontwikkeling van Azure Custom Resource Provider, probeert u vragen te stellen op [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-custom-providers). Een soortgelijke vraag kan al zijn gesteld en beantwoord, dus controleer eerst voor het posten. Voeg de ```azure-custom-providers``` tag toe om snel te reageren!

## <a name="next-steps"></a>Volgende stappen

In dit artikel leerde u over aangepaste providers. Ga naar het volgende artikel om een aangepaste provider te maken.

- [Snelstart: Azure Custom Resource Provider maken en aangepaste resources implementeren](./create-custom-provider.md)
- [Zelfstudie: Aangepaste acties en resources maken in Azure](./tutorial-get-started-with-custom-providers.md)
- [How To: Aangepaste acties toevoegen aan Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [How To: Aangepaste resources toevoegen aan Azure REST API](./custom-providers-resources-endpoint-how-to.md)
