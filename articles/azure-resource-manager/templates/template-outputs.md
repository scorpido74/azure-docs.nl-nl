---
title: Uitvoer in sjablonen
description: Hierin wordt beschreven hoe u uitvoer waarden definieert in een Azure Resource Manager sjabloon.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: ec96b45cdc5ccf488d46c2d8da03caf16d002dfa
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77622843"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Uitvoer in Azure Resource Manager sjabloon

In dit artikel wordt beschreven hoe u uitvoer waarden definieert in uw Azure Resource Manager sjabloon. U gebruikt uitvoer wanneer u waarden van de geïmplementeerde resources moet retour neren.

## <a name="define-output-values"></a>Uitvoer waarden definiëren

Het volgende voorbeeld laat zien hoe geretourneerd van de resource-ID voor een openbaar IP-adres:

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

## <a name="dynamic-number-of-outputs"></a>Dynamisch aantal uitvoer bewerkingen

In sommige scenario's weet u niet het aantal exemplaren van een waarde die u moet retour neren bij het maken van de sjabloon. U kunt een variabele aantal waarden retour neren met behulp van het element **copy** .

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

Zie [herhalingen in azure Resource Manager-sjablonen](copy-outputs.md)voor meer informatie.

## <a name="linked-templates"></a>Gekoppelde sjablonen

Gebruik de functie [Reference](template-functions-resource.md#reference) in de bovenliggende sjabloon om de uitvoer waarde van een gekoppelde sjabloon op te halen. De syntaxis van de bovenliggende sjabloon is:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Bij het ophalen van een eigenschap van een uitvoer van een gekoppelde sjabloon, kan niet de eigenschapsnaam van de een streepje bestaan.

In het volgende voor beeld ziet u hoe u het IP-adres instelt op een load balancer door een waarde op te halen uit een gekoppelde sjabloon.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

U kunt de functie `reference` niet gebruiken in het gedeelte outputs van een [geneste sjabloon](linked-templates.md#nested-template). Als u wilt de waarden voor een geïmplementeerde resource in een geneste sjabloon, uw geneste sjabloon te converteren naar een gekoppelde sjabloon.

## <a name="get-output-values"></a>Uitvoer waarden ophalen

Wanneer de implementatie is geslaagd, worden de uitvoer waarden automatisch geretourneerd in de resultaten van de implementatie.

Als u uitvoer waarden wilt ophalen uit de implementatie geschiedenis, kunt u script gebruiken.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group deployment show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Voorbeeldsjablonen

In de volgende voor beelden ziet u scenario's voor het gebruik van uitvoer.

|Template  |Beschrijving  |
|---------|---------|
|[Variabelen kopiëren](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Complexe variabelen maakt en deze waarden weergeeft. Niet alle resources niet implementeren. |
|[Openbaar IP-adres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Hiermee maakt u een openbaar IP-adres en de uitvoer van de resource-ID. |
|[Load balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Koppelingen naar de voorgaande sjabloon. Maakt gebruik van de resource-ID in de uitvoer bij het maken van de load balancer. |

## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager-sjablonen](template-syntax.md)voor meer informatie over de beschik bare eigenschappen voor uitvoer.
