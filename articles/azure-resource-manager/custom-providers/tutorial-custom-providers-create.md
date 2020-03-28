---
title: Een aangepaste provider maken en gebruiken
description: In deze zelfstudie ziet u hoe u een Azure Custom Provider maakt en gebruikt. Gebruik aangepaste providers om werkstromen op Azure te wijzigen.
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 09df78955de6423244c2d8ec94e1e1c06ecab257
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75650028"
---
# <a name="create-and-use-a-custom-provider"></a>Een aangepaste provider maken en gebruiken

Een aangepaste provider is een contract tussen Azure en een eindpunt. Met aangepaste providers u werkstromen op Azure wijzigen. In deze zelfstudie wordt het proces van het maken van een aangepaste provider weergegeven. Als u niet bekend bent met Azure Custom Providers, raadpleegt u [het overzicht van Azure Custom Resource Providers](overview.md).

## <a name="create-a-custom-provider"></a>Een aangepaste provider maken

> [!NOTE]
> In deze zelfstudie wordt niet weergegeven hoe u een eindpunt maakt. Als u geen RESTFUL-eindpunt hebt, volgt u de [zelfstudie over het ontwerpen van RESTful-eindpunten](./tutorial-custom-providers-function-authoring.md), wat de basis is voor de huidige zelfstudie.

Nadat u een eindpunt hebt gemaakt, u een aangepaste provider maken om een contract tussen de provider en het eindpunt te genereren. Met een aangepaste provider u een lijst met eindpuntdefinities opgeven:

```JSON
{
  "name": "myEndpointDefinition",
  "routingType": "Proxy",
  "endpoint": "https://<yourapp>.azurewebsites.net/api/<funcname>?code=<functionkey>"
}
```

Eigenschap | Vereist | Beschrijving
---|---|---
**Naam** | Ja | De naam van de eindpuntdefinitie. Azure onthult deze naam via de API onder /abonnementen/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders<br>/resourceProviders/{resourceProviderName}/{endpointDefinitionName}
**routingType** | Nee | Het type eindpuntcontract. Als de waarde niet is opgegeven, wordt deze standaard weergegeven als 'Proxy'.
**Eindpunt** | Ja | Het eindpunt om de aanvragen naar te leiden. Dit eindpunt verwerkt het antwoord en eventuele bijwerkingen van de aanvraag.

De waarde van **eindpunt** is de trigger-URL van de Azure-functie-app. De `<yourapp>` `<funcname>`tijdelijke `<functionkey>` aanduidingen en tijdelijke aanduidingen moeten worden vervangen door waarden voor de gemaakte functie-app.

## <a name="define-custom-actions-and-resources"></a>Aangepaste acties en resources definiëren

De aangepaste provider bevat een lijst met eindpuntdefinities die zijn gemodelleerd onder de eigenschappen **acties** en **resourceTypes.** De eigenschap **acties** worden toegewezen aan de aangepaste acties die door de aangepaste provider zijn blootgesteld en de eigenschap **resourceTypes** zijn de aangepaste resources. In deze zelfstudie heeft de aangepaste `myCustomAction` provider een eigenschap **acties** met de naam en een eigenschap **resourceTypes** met de naam `myCustomResources`.

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
> U moet de **eindpuntwaarden** vervangen door de trigger-URL van de functie-app die in de vorige zelfstudie is gemaakt.

U de vorige aangepaste provider implementeren met behulp van een Azure Resource Manager-sjabloon:

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

Nadat u een aangepaste provider hebt gemaakt, u de nieuwe Azure API's gebruiken. In de volgende tabbladen wordt uitgelegd hoe u een aangepaste provider bellen en gebruiken.

### <a name="custom-actions"></a>Aangepaste acties

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

> [!NOTE]
> U moet `{subscriptionId}` de `{resourceGroupName}` tijdelijke aanduidingen en tijdelijke aanduidingen vervangen door de abonnements- en resourcegroep van waar u de aangepaste provider hebt geïmplementeerd.

```azurecli-interactive
az resource invoke-action --action myCustomAction \
                          --ids /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider \
                          --request-body
                            '{
                                "hello": "world"
                            }'
```

Parameter | Vereist | Beschrijving
---|---|---
*Actie* | Ja | De naam van de actie die is gedefinieerd in de aangepaste provider
*Ids* | Ja | De resource-id van de aangepaste provider
*aanvraag-lichaam* | Nee | De aanvraaginstantie die naar het eindpunt wordt verzonden

# <a name="template"></a>[Sjabloon](#tab/template)

Geen.

---

### <a name="custom-resources"></a>Aangepaste resources

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

> [!NOTE]
> U moet `{subscriptionId}` de `{resourceGroupName}` tijdelijke aanduidingen en tijdelijke aanduidingen vervangen door de abonnements- en resourcegroep van waar u de aangepaste provider hebt geïmplementeerd.

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

Parameter | Vereist | Beschrijving
---|---|---
*is-full-object* | Ja | Hiermee geeft u aan of het eigenschappenobject andere opties bevat, zoals locatie, tags, SKU of plan.
*id* | Ja | De resource-id van de aangepaste resource. Deze ID is een uitbreiding van de resource-id van de aangepaste provider.
*Eigenschappen* | Ja | De aanvraaginstantie die naar het eindpunt wordt verzonden.

#### <a name="delete-a-custom-resource"></a>Een aangepaste bron verwijderen

```azurecli-interactive
az resource delete --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parameter | Vereist | Beschrijving
---|---|---
*id* | Ja | De resource-id van de aangepaste resource. Deze ID is een uitbreiding van de resource-id van de aangepaste provider.

#### <a name="retrieve-a-custom-resource"></a>Een aangepaste resource ophalen

```azurecli-interactive
az resource show --id /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/myCustomProvider/myCustomResources/myTestResourceName1
```

Parameter | Vereist | Beschrijving
---|---|---
*id* | Ja | De resource-id van de aangepaste resource. Deze ID is een uitbreiding van de resource-id van de aangepaste provider.

# <a name="template"></a>[Sjabloon](#tab/template)

Een voorbeeld van resourcemanagersjabloon:

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

Parameter | Vereist | Beschrijving
---|---|---
*resourceTypeName* | Ja | De `name` waarde van de eigenschap **resourceTypes** gedefinieerd in de aangepaste provider.
*resourceProviderName* | Ja | De naam van de aangepaste providerinstantie.
*aangepaste ResourceName* | Ja | De aangepaste resourcenaam.

---

> [!NOTE]
> Nadat u klaar bent met het implementeren en gebruiken van de aangepaste provider, moet u ervoor zorgen dat alle gemaakte resources, waaronder de Azure-functie-app, worden opgeschoond.

## <a name="next-steps"></a>Volgende stappen

In dit artikel leerde u over aangepaste providers. Zie voor meer informatie:

- [How to: Aangepaste acties toevoegen aan Azure REST API](./custom-providers-action-endpoint-how-to.md)
- [How to: Aangepaste resources toevoegen aan Azure REST API](./custom-providers-resources-endpoint-how-to.md)
