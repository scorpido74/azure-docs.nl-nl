---
title: Referentiemateriaal voor aangepaste resource-cache
description: Aangepaste broncacheverwijzing voor Azure Custom Resource Providers. Dit artikel gaat door de vereisten voor eindpunten implementeren cache aangepaste bronnen.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: e1b8c44f020d18066423eed236018308fe88b607
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650381"
---
# <a name="custom-resource-cache-reference"></a>Naslaginformatie over aangepaste broncache

Dit artikel gaat door de vereisten voor eindpunten implementeren cache aangepaste bronnen. Als u niet bekend bent met Azure Custom Resource Providers, raadpleegt u [het overzicht over aangepaste resourceproviders.](overview.md)

## <a name="how-to-define-a-cache-resource-endpoint"></a>Een eindpunt van een cachebron definiëren

Er kan een proxybron worden gemaakt door de **routingType** naar 'Proxy, Cache' op te geven.

Voorbeeld van aangepaste resourceprovider:

```JSON
{
  "properties": {
    "resourceTypes": [
      {
        "name": "myCustomResources",
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

## <a name="building-proxy-resource-endpoint"></a>Eindpunt van proxyresource

Een **eindpunt** dat een **eindpunt** voor de bron proxy, cache implementeert, moet de aanvraag en het antwoord voor de nieuwe API in Azure verwerken. In dit geval genereert **de resourceType** een `PUT`nieuwe `GET`Azure-bron-API voor , en `DELETE` om `GET` CRUD uit te voeren op één resource, en om alle bestaande resources op te halen:

> [!NOTE]
> De Azure API genereert de `PUT` `GET`aanvraagmethoden `DELETE`en , maar het eindpunt `PUT` `DELETE` **van** de cache hoeft alleen te worden verwerkt en .
> We hebben aanbevolen **endpoint** dat het `GET`eindpunt ook implementeert.

### <a name="create-a-custom-resource"></a>Een aangepaste resource maken

Inkomende aanvraag voor Azure API:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Deze aanvraag wordt vervolgens doorgestuurd naar het **eindpunt** in het formulier:

``` HTTP
PUT https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

Op dezelfde manier wordt het antwoord van het **eindpunt** vervolgens doorgestuurd naar de klant. Het antwoord van het eindpunt moet terugkeren:

- Een geldig JSON-objectdocument. Alle arrays en tekenreeksen moeten onder een bovenobject worden genest.
- De `Content-Type` header moet worden ingesteld op "toepassing/ json; charset=utf-8".
- De aangepaste resourceprovider overschrijft `type`de `id` `name`velden en velden voor de aanvraag.
- De aangepaste resourceprovider retourneert `properties` alleen velden onder het object voor een cacheeindpunt.

**Eindpunt** Reactie:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

De `name` `id`velden `type` en velden worden automatisch gegenereerd voor de aangepaste resource door de aangepaste resourceprovider.

Reactie van Azure Custom Resource Provider:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="remove-a-custom-resource"></a>Een aangepaste resource verwijderen

Inkomende aanvraag voor Azure API:

``` HTTP
Delete https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Deze aanvraag wordt vervolgens doorgestuurd naar het **eindpunt** in het formulier:

``` HTTP
Delete https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Ook wordt de reactie van het **eindpunt** doorgestuurd naar de klant. Het antwoord van het eindpunt moet terugkeren:

- Een geldig JSON-objectdocument. Alle arrays en tekenreeksen moeten onder een bovenobject worden genest.
- De `Content-Type` header moet worden ingesteld op "toepassing/ json; charset=utf-8".
- De Azure Custom Resource Provider verwijdert het item alleen uit de cache als een reactie van 200 niveaus wordt geretourneerd. Zelfs als de resource niet bestaat, moet het **eindpunt** 204 retourneren.

**Eindpunt** Reactie:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

Reactie van Azure Custom Resource Provider:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
```

### <a name="retrieve-a-custom-resource"></a>Een aangepaste resource ophalen

Inkomende aanvraag voor Azure API:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

De aanvraag wordt **niet** doorgestuurd naar het **eindpunt**.

Reactie van Azure Custom Resource Provider:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "name": "{myCustomResourceName}",
    "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
    "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
    "properties": {
        "myProperty1": "myPropertyValue1",
        "myProperty2": {
            "myProperty3" : "myPropertyValue3"
        }
    }
}
```

### <a name="enumerate-all-custom-resources"></a>Alle aangepaste resources opsommen

Inkomende aanvraag voor Azure API:

``` HTTP
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources?api-version=2018-09-01-preview
Authorization: Bearer eyJ0e...
Content-Type: application/json
```

Deze aanvraag wordt **niet** doorgestuurd naar het **eindpunt**.

Reactie van Azure Custom Resource Provider:

``` HTTP
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8

{
    "value" : [
        {
            "name": "{myCustomResourceName}",
            "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}",
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "properties": {
                "myProperty1": "myPropertyValue1",
                "myProperty2": {
                    "myProperty3" : "myPropertyValue3"
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Custom Resource Providers](overview.md)
- [Snelstart: Azure Custom Resource Provider maken en aangepaste resources implementeren](./create-custom-provider.md)
- [Zelfstudie: Aangepaste acties en resources maken in Azure](./tutorial-get-started-with-custom-providers.md)
- [How To: Aangepaste acties toevoegen aan Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Naslaginformatie: Aangepaste naslaggids voor resourceproxy](proxy-resource-endpoint-reference.md)
