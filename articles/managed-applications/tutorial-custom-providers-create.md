---
title: Een aangepaste provider maken en gebruiken
description: Deze zelf studie gaat over het maken en gebruiken van een aangepaste provider.
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 053cf9fca03bf58cf10c313ae2569ce1918a46b9
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172891"
---
# <a name="create-and-use-a-custom-provider"></a>Een aangepaste provider maken en gebruiken

Een aangepaste provider is een contract tussen Azure en een eind punt. Met aangepaste providers kunt u werk stromen wijzigen in Azure. In deze zelf studie wordt het proces voor het maken van een aangepaste provider weer gegeven. Als u niet bekend bent met aangepaste Azure-providers, raadpleegt u [het overzicht van Azure Custom resource providers](./custom-providers-overview.md).

## <a name="create-a-custom-provider"></a>Een aangepaste provider maken

> [!NOTE]
> In deze zelf studie wordt niet weer gegeven hoe u een eind punt kunt ontwerpen. Als u geen REST-eind punt hebt, volgt u de [zelf studie over het ontwerpen van rest-eind punten](./tutorial-custom-providers-function-authoring.md). Dit is de basis van de huidige zelf studie.

Nadat u een eind punt hebt gemaakt, kunt u een aangepaste provider maken om een contract tussen de provider en het eind punt te genereren. Met een aangepaste provider kunt u een lijst met eindpunt definities opgeven:

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Eigenschap | Vereist | Description
---|---|---
**name** | Ja | De naam van de eindpunt definitie. Deze naam wordt door Azure weer gegeven via de API onder/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}
**routingType** | Nee | Het type eindpunt contract. Als de waarde niet is opgegeven, wordt standaard ' proxy ' gebruikt.
**endpoints** | Ja | Het eind punt waarnaar de aanvragen worden gerouteerd. Dit eind punt verwerkt het antwoord en eventuele neven effecten van de aanvraag.

De waarde van het **eind punt** is de trigger-URL van de Azure function-app. De `<yourapp>`tijdelijke `<funcname>`aanduidingen `<functionkey>` , en moeten worden vervangen door waarden voor de gemaakte functie-app.

## <a name="define-custom-actions-and-resources"></a>Aangepaste acties en resources definiëren

De aangepaste provider bevat een lijst met eindpunt definities die zijn gemodelleerd onder de eigenschappen **Actions** en **resourceTypes** . De eigenschap **Actions** wordt toegewezen aan de aangepaste acties die worden weer gegeven door de aangepaste provider en de eigenschap **resourceTypes** is de aangepaste resources. In deze zelf studie heeft de aangepaste provider de eigenschap `myCustomAction` **Actions** en een **resourceTypes** eigenschap met `myCustomResources`de naam.

```JSON
{
  "properties": {
    "actions": [
      {
        "name": "myCustomAction",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ],
    "resourceTypes": [
      {
        "name": "myCustomResources",
        "routingType": "Proxy",
        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
      }
    ]
  },
  "location": "eastus"
}
```

## <a name="deploy-the-custom-provider"></a>De aangepaste provider implementeren

> [!NOTE]
> U moet de **eindpunt** waarden vervangen door de trigger-URL uit de functie-app die u in de vorige zelf studie hebt gemaakt.

U kunt de vorige aangepaste provider implementeren met behulp van een Azure Resource Manager sjabloon:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders",
            "name": "myCustomProvider",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "actions": [
                    {
                        "name": "myCustomAction",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ],
                "resourceTypes": [
                    {
                        "name": "myCustomResources",
                        "routingType": "Proxy",
                        "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
                    }
                ]
            }
        }
    ]
}
```

## <a name="use-custom-actions-and-resources"></a>Aangepaste acties en resources gebruiken

Nadat u een aangepaste provider hebt gemaakt, kunt u de nieuwe Azure-Api's gebruiken. In de volgende tabbladen wordt uitgelegd hoe u een aangepaste provider aanroept en gebruikt.

### <a name="custom-actions"></a>Aangepaste acties

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

> [!NOTE]
> U moet de `{subscriptionId}` en `{resourceGroupName}` tijdelijke aanduidingen vervangen door het abonnement en de resource groep waar u de aangepaste provider hebt geïmplementeerd.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Parameter | Vereist | Description
---|---|---
*optreden* | Ja | De naam van de actie die is gedefinieerd in de aangepaste provider
*id's* | Ja | De resource-ID van de aangepaste provider
*aanvraag-hoofd tekst* | Nee | De hoofd tekst van de aanvraag die naar het eind punt wordt verzonden

# <a name="templatetabtemplate"></a>[Sjabloon](#tab/template)

Geen.

---

### <a name="custom-resources"></a>Aangepaste resources

# <a name="azure-clitabazure-cli"></a>[Azure-CLI](#tab/azure-cli)

> [!NOTE]
> U moet de `{subscriptionId}` en `{resourceGroupName}` tijdelijke aanduidingen vervangen door het abonnement en de resource groep waar u de aangepaste provider hebt geïmplementeerd.

#### <a name="create-a-custom-resource"></a>Een aangepaste resource maken

```azurecli-interactive
az resource create --is-full-object \
                   --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1 \
                   --properties
                    '{
                        "location": "eastus",
                        "properties": {
                            "hello" : "world"
                        }
                    }'
```

Parameter | Vereist | Description
---|---|---
*is-Full-object* | Ja | Hiermee wordt aangegeven of het eigenschappen object andere opties bevat zoals locatie, tags, SKU of plan.
*id* | Ja | De resource-ID van de aangepaste resource. Deze ID is een uitbrei ding van de resource-ID van de aangepaste provider.
*Eigenschappen* | Ja | De hoofd tekst van de aanvraag die naar het eind punt wordt verzonden.

#### <a name="delete-a-custom-resource"></a>Een aangepaste resource verwijderen

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parameter | Vereist | Description
---|---|---
*id* | Ja | De resource-ID van de aangepaste resource. Deze ID is een uitbrei ding van de resource-ID van de aangepaste provider.

#### <a name="retrieve-a-custom-resource"></a>Een aangepaste resource ophalen

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parameter | Vereist | Description
---|---|---
*id* | Ja | De resource-ID van de aangepaste resource. Deze ID is een uitbrei ding van de resource-ID van de aangepaste provider.

# <a name="templatetabtemplate"></a>[Sjabloon](#tab/template)

Een voor beeld van een resource manager-sjabloon:

```JSON
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.CustomProviders/resourceProviders/myCustomResources",
            "name": "myCustomProvider/myTestResourceName1",
            "apiVersion": "2018-09-01-preview",
            "location": "eastus",
            "properties": {
                "hello": "world"
            }
        }
    ]
}
```

Parameter | Vereist | Description
---|---|---
*resourceTypeName* | Ja | De `name` waarde van de eigenschap **resourceTypes** die in de aangepaste provider is gedefinieerd.
*resourceProviderName* | Ja | De naam van het aangepaste provider exemplaar.
*customResourceName* | Ja | De aangepaste resource naam.

---

> [!NOTE]
> Wanneer u klaar bent met het implementeren en gebruiken van de aangepaste provider, moet u alle gemaakte resources opschonen, inclusief de Azure-functie-app.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over aangepaste providers. Zie voor meer informatie:

- [Procedure: Aangepaste acties toevoegen aan Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [Procedure: Aangepaste resources toevoegen aan Azure REST API](./custom-providers-resources-endpoint-how-to.md)
