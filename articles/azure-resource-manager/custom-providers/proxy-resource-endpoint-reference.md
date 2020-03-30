---
title: Referentiemateriaal voor aangepaste resource-proxy
description: Aangepaste resourceproxyverwijzing voor Azure Custom Resource Providers. Dit artikel gaat door de vereisten voor eindpunten implementeren proxy aangepaste resources.
ms.topic: conceptual
ms.author: jobreen
author: jjbfour
ms.date: 06/20/2019
ms.openlocfilehash: 46b38686b39836f3d4bfb80686d514f932a79bf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75650459"
---
# <a name="custom-resource-proxy-reference"></a>Naslaginformatie over aangepaste resourceproxy

Dit artikel gaat door de vereisten voor eindpunten implementeren proxy aangepaste resources. Als u niet bekend bent met Azure Custom Resource Providers, raadpleegt u [het overzicht over aangepaste resourceproviders.](overview.md)

## <a name="how-to-define-a-proxy-resource-endpoint"></a>Een eindpunt van een proxyresource definiëren

Er kan een proxybron worden gemaakt door de **routingType** naar 'Proxy' op te geven.

Voorbeeld van aangepaste resourceprovider:

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
  "location": "eastus",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}",
  "name": "{resourceProviderName}"
}
```

## <a name="building-proxy-resource-endpoint"></a>Eindpunt van proxyresource

Een **eindpunt** dat een **eindpunt** voor de resource 'Proxy' implementeert, moet de aanvraag en het antwoord voor de nieuwe API in Azure verwerken. In dit geval genereert **de resourceType** een `PUT`nieuwe `GET`Azure-bron-API voor , en `DELETE` om `GET` CRUD uit te voeren op één resource, en om alle bestaande resources op te halen.

> [!NOTE]
> De `id` `name`velden `type` en velden zijn niet vereist, maar zijn nodig om de aangepaste bron te integreren met het bestaande Azure-ecosysteem.

Voorbeeldbron:

``` JSON
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

Parameterreferentie:

Eigenschap | Voorbeeld | Beschrijving
---|---|---
name | {myCustomResourceName}' | De naam van de aangepaste resource.
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | De naamruimte van het resourcetype.
id | '/abonnementen/{subscriptionId}/resourceGroepen/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>myCustomResources/{myCustomResourceName}' | De resource-id.

### <a name="create-a-custom-resource"></a>Een aangepaste resource maken

Inkomende aanvraag voor Azure API:

``` HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resource-provider-name}/myCustomResources/{myCustomResourceName}?api-version=2018-09-01-preview
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

**Eindpunt** Reactie:

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

Ook wordt het antwoord van het **eindpunt** doorgestuurd naar de klant. Het antwoord van het eindpunt moet terugkeren:

- Geldig JSON-objectdocument. Alle arrays en tekenreeksen moeten onder een bovenobject worden genest.
- De `Content-Type` header moet worden ingesteld op "toepassing/ json; charset=utf-8".

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

Deze aanvraag wordt vervolgens doorgestuurd naar het **eindpunt** in het formulier:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources/{myCustomResourceName}
```

Op dezelfde manier wordt het antwoord van het **eindpunt** vervolgens doorgestuurd naar de klant. Het antwoord van het eindpunt moet terugkeren:

- Een geldig JSON-objectdocument. Alle arrays en tekenreeksen moeten onder een bovenobject worden genest.
- De `Content-Type` header moet worden ingesteld op "toepassing/ json; charset=utf-8".

**Eindpunt** Reactie:

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

Deze aanvraag wordt vervolgens doorgestuurd naar het **eindpunt** in het formulier:

``` HTTP
GET https://{endpointURL}/?api-version=2018-09-01-preview
Content-Type: application/json
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/myCustomResources
```

Op dezelfde manier wordt het antwoord van het **eindpunt** vervolgens doorgestuurd naar de klant. Het antwoord van het eindpunt moet terugkeren:

- Een geldig JSON-objectdocument. Alle arrays en tekenreeksen moeten onder een bovenobject worden genest.
- De `Content-Type` header moet worden ingesteld op "toepassing/ json; charset=utf-8".
- De lijst met resources moet onder `value` de eigenschap op het hoogste niveau worden geplaatst.

**Eindpunt** Reactie:

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
- [Naslaginformatie: Verwijzing naar aangepaste cachebronnen](proxy-cache-resource-endpoint-reference.md)
