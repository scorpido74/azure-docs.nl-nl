---
title: Uitvoer in sjablonen
description: Hierin wordt beschreven hoe u uitvoer waarden definieert in een Azure Resource Manager sjabloon.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: 61120b9941a6a20812ea046265ecbe13014d769e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689148"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Uitvoer in Azure Resource Manager sjabloon

In dit artikel wordt beschreven hoe u uitvoer waarden definieert in uw Azure Resource Manager sjabloon. U gebruikt uitvoer wanneer u waarden van de geïmplementeerde resources moet retour neren.

## <a name="define-output-values"></a>Uitvoer waarden definiëren

In het volgende voor beeld ziet u hoe u de resource-ID voor een openbaar IP-adres als resultaat kunt geven:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Voorwaardelijke uitvoer

In de sectie outputs kunt u voorwaardelijk een waarde Retour neren. Normaal gesp roken gebruikt u de voor waarde in de uitvoer wanneer u een resource [voorwaardelijk hebt geïmplementeerd](conditional-resource-deployment.md) . In het volgende voor beeld ziet u hoe u de resource-ID voor een openbaar IP-adres voorwaardelijk kunt retour neren op basis van het feit of er een nieuw pakket is geïmplementeerd:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Zie [voorwaardelijke uitvoer sjabloon](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)voor een eenvoudig voor beeld van voorwaardelijke uitvoer.

## <a name="linked-templates"></a>Gekoppelde sjablonen

Gebruik de functie [Reference](resource-group-template-functions-resource.md#reference) in de bovenliggende sjabloon om de uitvoer waarde van een gekoppelde sjabloon op te halen. De syntaxis van de bovenliggende sjabloon is:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Bij het ophalen van een uitvoer eigenschap van een gekoppelde sjabloon, mag de naam van de eigenschap geen streepje bevatten.

In het volgende voor beeld ziet u hoe u het IP-adres instelt op een load balancer door een waarde op te halen uit een gekoppelde sjabloon.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

U kunt de functie `reference` niet gebruiken in het gedeelte outputs van een [geneste sjabloon](resource-group-linked-templates.md#nested-template). Als u de waarden voor een geïmplementeerde resource in een geneste sjabloon wilt retour neren, converteert u de geneste sjabloon naar een gekoppelde sjabloon.

## <a name="get-output-values"></a>Uitvoer waarden ophalen

Wanneer de implementatie is geslaagd, worden de uitvoer waarden automatisch geretourneerd in de resultaten van de implementatie.

Als u uitvoer waarden wilt ophalen uit de implementatie geschiedenis, kunt u script gebruiken.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group deployment show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Voorbeeld sjablonen

In de volgende voor beelden ziet u scenario's voor het gebruik van uitvoer.

|Sjabloon  |Beschrijving  |
|---------|---------|
|[Variabelen kopiëren](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Maakt complexe variabelen en voert deze waarden uit. Implementeert geen resources. |
|[Openbaar IP-adres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Hiermee maakt u een openbaar IP-adres en voert u de resource-ID uit. |
|[Load balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Koppelingen naar de vorige sjabloon. Maakt gebruik van de resource-ID in de uitvoer bij het maken van de load balancer. |

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager-sjablonen](resource-group-authoring-templates.md)voor meer informatie over de beschik bare eigenschappen voor uitvoer.
