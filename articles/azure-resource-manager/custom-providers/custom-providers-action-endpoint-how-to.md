---
title: Aangepaste acties toevoegen aan Azure REST API
description: Meer informatie over het toevoegen van aangepaste acties aan de Azure REST API. In dit artikel worden de vereisten en best practices voor eindpunten doorlopen die aangepaste acties willen implementeren.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 6110a7952b7c29609d2b98e135b61032aec3fa52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650394"
---
# <a name="adding-custom-actions-to-azure-rest-api"></a>Aangepaste acties toevoegen aan Azure REST API

In dit artikel worden de vereisten en aanbevolen procedures voor het maken van Azure Custom Resource Provider-eindpunten doorlopen die aangepaste acties implementeren. Als u niet bekend bent met Azure Custom Resource Providers, raadpleegt u [het overzicht over aangepaste resourceproviders.](overview.md)

## <a name="how-to-define-an-action-endpoint"></a>Een actieeindpunt definiëren

Een **eindpunt** is een URL die verwijst naar een service, die het onderliggende contract tussen het en Azure implementeert. Het eindpunt wordt gedefinieerd in de aangepaste resourceprovider en kan elke openbaar toegankelijke URL zijn. De onderstaande steekproef `myCustomAction` heeft een `endpointURL` **actie** genaamd uitgevoerd door .

Voorbeeld **van ResourceProvider:**

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
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-an-action-endpoint"></a>Een actieeindpunt bouwen

Een **eindpunt** dat een **actie** implementeert, moet de aanvraag en het antwoord voor de nieuwe API in Azure verwerken. Wanneer een aangepaste resourceprovider met een **actie** wordt gemaakt, genereert deze een nieuwe set API's in Azure. In dit geval genereert de actie een `POST` nieuwe Azure-actie-API voor aanroepen:

``` JSON
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction
```

Inkomende aanvraag voor Azure API:

``` HTTP
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Deze aanvraag wordt vervolgens doorgestuurd naar het **eindpunt** in het formulier:

``` HTTP
POST https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomAction

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Op dezelfde manier wordt het antwoord van het **eindpunt** vervolgens doorgestuurd naar de klant. Het antwoord van het eindpunt moet terugkeren:

- Een geldig JSON-objectdocument. Alle arrays en tekenreeksen moeten onder een bovenobject worden genest.
- De `Content-Type` header moet worden ingesteld op "toepassing/ json; charset=utf-8".

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

Reactie van Azure Custom Resource Provider:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "myProperty1": "myPropertyValue1",
    "myProperty2": {
        "myProperty3" : "myPropertyValue3"
    }
}
```

## <a name="calling-a-custom-action"></a>Een aangepaste actie aanroepen

Er zijn twee belangrijke manieren om een aangepaste actie af te roepen van een aangepaste resourceprovider:

- Azure-CLI
- Azure Resource Manager-sjablonen

### <a name="azure-cli"></a>Azure-CLI

```azurecli-interactive
az resource invoke-action --action {actionName} \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName} \
                          --request-body \
                            '{
                                "myProperty1": "myPropertyValue1",
                                "myProperty2": {
                                    "myProperty3": "myPropertyValue3"
                                }
                            }'
```

Parameter | Vereist | Beschrijving
---|---|---
action | *Ja* | De naam van de actie die is gedefinieerd in de **ResourceProvider**.
Ids | *Ja* | De resource-id van de **ResourceProvider**.
aanvraag-lichaam | *nee* | De aanvraaginstantie die naar het **eindpunt**wordt verzonden.

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sjabloon

> [!NOTE]
> Acties hebben beperkte ondersteuning in Azure Resource Manager-sjablonen. Om de actie in een sjabloon aan te [`list`](../templates/template-functions-resource.md#list) roepen, moet het voorvoegsel in de naam bevatten.

Voorbeeld **van resourceprovider** met lijstactie:

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "listMyCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://{endpointURL}/"
      }
    ]
  },
  "location": "eastus"
}
```

Voorbeeld van Azure Resource Manager-sjabloon:

``` JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "resourceIdentifier": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
        "apiVersion": "2018-09-01-preview",
        "functionValues": {
            "myProperty1": "myPropertyValue1",
            "myProperty2": {
                "myProperty3": "myPropertyValue3"
            }
        }
    },
    "resources": [],
    "outputs": {
        "myCustomActionOutput": {
            "type": "object",
            "value": "[listMyCustomAction(variables('resourceIdentifier'), variables('apiVersion'), variables('functionValues'))]"
        }
    }
}
```

Parameter | Vereist | Beschrijving
---|---|---
resourceIdentifier | *Ja* | De resource-id van de **ResourceProvider**.
apiVersion | *Ja* | De API-versie van de resourceruntime. Dit moet altijd "2018-09-01-preview" zijn.
functieWaarden | *nee* | De aanvraaginstantie die naar het **eindpunt**wordt verzonden.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Custom Resource Providers](overview.md)
- [Snelstart: Azure Custom Resource Provider maken en aangepaste resources implementeren](./create-custom-provider.md)
- [Zelfstudie: Aangepaste acties en resources maken in Azure](./tutorial-get-started-with-custom-providers.md)
- [How To: Aangepaste resources toevoegen aan Azure REST API](./custom-providers-resources-endpoint-how-to.md)
