---
title: Aangepaste resources toevoegen aan Azure REST API
description: Meer informatie over het toevoegen van aangepaste resources aan de Azure REST API. In dit artikel worden de vereisten en aanbevolen procedures voor eindpunten doorlopen die aangepaste resources willen implementeren.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: b6c5f5b8e437ad2dc2e8a3be3f3f2ed03a613b44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650524"
---
# <a name="adding-custom-resources-to-azure-rest-api"></a>Aangepaste resources toevoegen aan Azure REST API

In dit artikel worden de vereisten en aanbevolen procedures voor het maken van Azure Custom Resource Provider-eindpunten doorlopen die aangepaste resources implementeren. Als u niet bekend bent met Azure Custom Resource Providers, raadpleegt u [het overzicht over aangepaste resourceproviders.](overview.md)

## <a name="how-to-define-a-resource-endpoint"></a>Een eindpunt van een resource definiëren

Een **eindpunt** is een URL die verwijst naar een service, die het onderliggende contract tussen het en Azure implementeert. Het eindpunt wordt gedefinieerd in de aangepaste resourceprovider en kan elke openbaar toegankelijke URL zijn. Het onderstaande voorbeeld heeft `myCustomResource` een `endpointURL` **resourceType** genaamd geïmplementeerd door .

Voorbeeld **van ResourceProvider:**

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

## <a name="building-a-resource-endpoint"></a>Een resourceeindpunt bouwen

Een **eindpunt** dat een **resourceType implementeert,** moet de aanvraag en het antwoord voor de nieuwe API in Azure verwerken. Wanneer een aangepaste resourceprovider met een **resourceType** wordt gemaakt, genereert deze een nieuwe set API's in Azure. In dit geval genereert **de resourceType** een `PUT`nieuwe `GET`Azure-bron-API voor , en om `DELETE` CRUD uit te voeren op één resource en `GET` om alle bestaande resources op te halen:

Eén resource`PUT`manipuleren `GET`( `DELETE`, en :

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource/{myCustomResourceName}
```

Alle resources`GET`ophalen ( ):

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResource
```

Voor aangepaste resources bieden aangepaste resourceproviders twee`Proxy`soorten **routeringstypen:**" " en "`Proxy, Cache`".

### <a name="proxy-routing-type"></a>type proxyroutering

De`Proxy`" **" routingType** proxy's alle aanvraagmethoden naar het **eindpunt** opgegeven in de aangepaste resource provider. Wanneer te`Proxy`gebruiken " ":

- Volledige controle over de reactie is nodig.
- Het integreren van systemen met bestaande bronnen.

Zie de aangepaste`Proxy` [resourceproxyverwijzing](proxy-resource-endpoint-reference.md) voor meer informatie over " resources

### <a name="proxy-cache-routing-type"></a>type proxycacheroutering

De`Proxy, Cache`" **" routingType** `PUT` proxy's alleen en `DELETE` verzoek methoden naar het **eindpunt** opgegeven in de aangepaste resource provider. De aangepaste resourceprovider retourneert `GET` automatisch aanvragen op basis van wat deze in de cache heeft opgeslagen. Als een aangepaste resource is gemarkeerd met cache, voegt de aangepaste resourceprovider ook velden toe / overschrijft in het antwoord om de API's Azure compliant te maken. Wanneer te`Proxy, Cache`gebruiken " ":

- Het creëren van een nieuw systeem dat geen bestaande bronnen heeft.
- Werken met het bestaande Azure-ecosysteem.

Zie de referentie`Proxy, Cache` [voor aangepaste broncache](proxy-cache-resource-endpoint-reference.md) voor meer informatie over " resources

## <a name="creating-a-custom-resource"></a>Een aangepaste resource maken

Er zijn twee belangrijke manieren om een aangepaste resource te maken voor een aangepaste resourceprovider:

- Azure-CLI
- Azure Resource Manager-sjablonen

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

Parameter | Vereist | Beschrijving
---|---|---
is-full-object | *Ja* | Geeft aan dat het eigenschappenobject andere opties bevat, zoals locatie, tags, sku en/of plan.
id | *Ja* | De resource-id van de aangepaste resource. Dit moet bestaan off van de **ResourceProvider**
properties | *Ja* | De aanvraaginstantie die naar het **eindpunt**wordt verzonden.

Een aangepaste Azure-bron verwijderen:

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parameter | Vereist | Beschrijving
---|---|---
id | *Ja* | De resource-id van de aangepaste resource. Dit moet bestaan off van de **ResourceProvider**.

Een Azure Custom Resource ophalen:

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{resourceTypeName}/{customResourceName}
```

Parameter | Vereist | Beschrijving
---|---|---
id | *Ja* | De resource-id van de aangepaste resource. Dit moet bestaan off van de **ResourceProvider**

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

> [!NOTE]
> Resources vereisen dat het `id`antwoord `name`een `type` passend antwoord bevat , en vanaf het **eindpunt**.

Azure Resource Manager-sjablonen `name`vereisen `type` dat `id`, en correct worden geretourneerd vanaf het downstream eindpunt. Een geretourneerde resourcereactie moet in de vorm staan:

Eindpuntrespons **van** de steekproef:

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

Voorbeeld van Azure Resource Manager-sjabloon:

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

Parameter | Vereist | Beschrijving
---|---|---
resourceTypeName | *Ja* | De **naam** van de **resourceType** die is gedefinieerd in de aangepaste provider.
resourceProviderName | *Ja* | De instantienaam van de aangepaste resourceprovider.
aangepaste ResourceName | *Ja* | De aangepaste resourcenaam.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Custom Resource Providers](overview.md)
- [Snelstart: Azure Custom Resource Provider maken en aangepaste resources implementeren](./create-custom-provider.md)
- [Zelfstudie: Aangepaste acties en resources maken in Azure](./tutorial-get-started-with-custom-providers.md)
- [How To: Aangepaste acties toevoegen aan Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Naslaginformatie: Aangepaste naslaggids voor resourceproxy](proxy-resource-endpoint-reference.md)
- [Naslaginformatie: Verwijzing naar aangepaste cachebronnen](proxy-cache-resource-endpoint-reference.md)
