---
title: Aangepaste resources toevoegen aan Azure REST API
description: Meer informatie over het toevoegen van aangepaste resources aan Azure REST API. In dit artikel worden de vereisten en aanbevolen procedures besproken voor eind punten die aangepaste resources willen implementeren.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75650524"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Aangepaste resources toevoegen aan Azure REST API

In dit artikel worden de vereisten en aanbevolen procedures beschreven voor het maken van eind punten van een aangepaste Azure-resource provider die aangepaste resources implementeert. Als u niet bekend bent met aangepaste Azure-resource providers, raadpleegt u [het overzicht van aangepaste resource providers](overview.md).

## <a name="how-to-define-a-resource-endpoint"></a>Een resource-eind punt definiëren

Een **eind punt** is een URL die verwijst naar een service, waarmee het onderliggende contract tussen IT en Azure wordt geïmplementeerd. Het eind punt wordt gedefinieerd in de aangepaste resource provider en kan een openbaar toegankelijke URL zijn. Het onderstaande voor beeld bevat een **resource type** met de naam `myCustomResource` geïmplementeerd door `endpointURL`.

Voor beeld van **resource provider**:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResource",
        "routingType": "Proxy, Cache",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-a-resource-endpoint"></a>Een resource-eind punt bouwen

Een **eind punt** dat een **resource type** implementeert, moet de aanvraag en het antwoord voor de nieuwe API in azure afhandelen. Wanneer er een aangepaste resource provider met een **resource type** wordt gemaakt, wordt er een nieuwe set Api's in azure gegenereerd. In dit geval genereert het **resource type** een nieuwe Azure-resource-API voor `PUT`, `GET`en `DELETE` voor het uitvoeren van ruw op één resource en `GET` om alle bestaande resources op te halen:

Eén resource (`PUT`, `GET`en `DELETE`) bewerken:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Alle resources ophalen (`GET`):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Aangepaste resource providers bieden aangepaste resources twee soorten **routingTypes**: "`Proxy`" en "`Proxy, Cache`".

### <a name="proxy-routing-type"></a>proxy routerings type

Met de **routingType** '`Proxy`' worden alle aanvraag methoden geproxy's naar het **eind punt** dat is opgegeven in de aangepaste resource provider. Wanneer gebruikt u '`Proxy`':

- Volledige controle over het antwoord is vereist.
- Systemen integreren met bestaande bronnen.

Zie voor meer informatie over de bronnen van '`Proxy`' [de verwijzing naar de aangepaste resource proxy](proxy-resource-endpoint-reference.md)

### <a name="proxy-cache-routing-type"></a>routerings type van proxy cache

De`Proxy, Cache`-proxy's **routingType** alleen `PUT` en `DELETE` aanvraag methoden naar het **eind punt** dat is opgegeven in de aangepaste resource provider. De aangepaste resource provider retourneert automatisch `GET` aanvragen op basis van de gegevens die zijn opgeslagen in de cache. Als een aangepaste resource is gemarkeerd met cache, worden in de aangepaste resource provider ook velden toegevoegd/overschreven in het antwoord om de Api's van Azure compatibel te maken. Wanneer gebruikt u '`Proxy, Cache`':

- Er wordt een nieuw systeem gemaakt dat geen bestaande resources heeft.
- Werken met een bestaand Azure-ecosysteem.

Zie voor meer informatie over de resources van '`Proxy, Cache`' [de verwijzing naar de aangepaste resource-cache](proxy-cache-resource-endpoint-reference.md)

## <a name="creating-a-custom-resource"></a>Een aangepaste resource maken

Er zijn twee belang rijke manieren voor het maken van een aangepaste resource uit een aangepaste resource provider:

- Azure-CLI
- Azure Resource Manager sjablonen

### <a name="azure-cli"></a>Azure-CLI

Een aangepaste resource maken:

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName} \
                   --properties \
                    '{
                        "location": "eastus",
                        "properties": {
                            "myProperty1": "myPropertyValue1",
                            "myProperty2": {
                                "myProperty3": "myPropertyValue3"
                            }
                        }
                    }'
```

Parameter | Verplicht | Beschrijving
---|---|---
is-Full-object | *klikt* | Geeft aan dat het eigenschappen object andere opties bevat, zoals locatie, tags, SKU en/of plan.
id | *klikt* | De resource-ID van de aangepaste resource. Dit moet bestaan uit de **resource provider**
properties | *klikt* | De hoofd tekst van de aanvraag die naar het **eind punt**wordt verzonden.

Een aangepaste Azure-resource verwijderen:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parameter | Verplicht | Beschrijving
---|---|---
id | *klikt* | De resource-ID van de aangepaste resource. Dit zou moeten bestaan uit de **resource provider**.

Een aangepaste Azure-resource ophalen:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parameter | Verplicht | Beschrijving
---|---|---
id | *klikt* | De resource-ID van de aangepaste resource. Dit moet bestaan uit de **resource provider**

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

> [!NOTE]
> Resources vereisen dat het antwoord een geschikte `id`, `name`en `type` bevat van het **eind punt**.

Azure Resource Manager sjablonen vereisen dat `id`, `name`en `type` correct worden geretourneerd vanaf het downstream-eind punt. Een geretourneerd resource antwoord moet de volgende indeling hebben:

Voor beeld van **eindpunt** reactie:

``` JSON
{
  "properties": {
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3": "myPropertyValue3"
    }
  },
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{customResourceName}",
  "name": "{customResourceName}",
  "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}"
}
```

Voor beeld van Azure Resource Manager sjabloon:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/{resourceTypeName}",
            "name": "{resourceProviderName}/{customResourceName}",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3": "myPropertyValue3"
                }
            }
        }
    ]
}
```

Parameter | Verplicht | Beschrijving
---|---|---
resourceTypeName | *klikt* | De **naam** van het **resource type** dat in de aangepaste provider is gedefinieerd.
resourceProviderName | *klikt* | De naam van de aangepaste resource provider-instantie.
customResourceName | *klikt* | De aangepaste resource naam.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van aangepaste Azure-resource providers](overview.md)
- [Snelstartgids: een aangepaste Azure-resource provider maken en aangepaste resources implementeren](./create-custom-provider.md)
- [Zelf studie: aangepaste acties en resources maken in azure](./tutorial-get-started-with-custom-providers.md)
- [Procedure: aangepaste acties toevoegen aan Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Naslag informatie: Naslag informatie over aangepaste resource proxy](proxy-resource-endpoint-reference.md)
- [Verwijzing: verwijzing naar aangepaste resource-cache](proxy-cache-resource-endpoint-reference.md)
