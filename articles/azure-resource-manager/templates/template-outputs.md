---
title: Uitvoer in sjablonen
description: Beschrijft hoe u uitvoerwaarden definieert in een Azure Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 203bfc66e9515ef14a5fe1315ef5b9ee07075041
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460021"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Uitvoer in azure resource manager-sjabloon

In dit artikel wordt beschreven hoe u uitvoerwaarden definieert in uw Azure Resource Manager-sjabloon. U gebruikt uitvoer wanneer u waarden uit de geïmplementeerde resources moet retourneren.

## <a name="define-output-values"></a>Uitvoerwaarden definiëren

In het volgende voorbeeld ziet u hoe u de bron-id retourneert voor een openbaar IP-adres:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Voorwaardelijke output

In de sectie uitvoer u een waarde voorwaardelijk retourneren. Normaal gesproken gebruikt u de voorwaarde in de uitvoer wanneer u een resource voorwaardelijk hebt [geïmplementeerd.](conditional-resource-deployment.md) In het volgende voorbeeld ziet u hoe u de resource-id voorwaardelijk retourneren voor een openbaar IP-adres op basis van de vraag of een nieuw IP-adres is geïmplementeerd:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Zie [voorwaardelijke uitvoersjabloon](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json)voor een eenvoudig voorbeeld van voorwaardelijke uitvoer.

## <a name="dynamic-number-of-outputs"></a>Dynamisch aantal uitgangen

In sommige scenario's weet u niet het aantal exemplaren van een waarde dat u moet retourneren bij het maken van de sjabloon. U een variabel aantal waarden retourneren met behulp van het **kopieerelement.**

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

Zie [Iteratie uitvoer uitvoer in Azure Resource Manager-sjablonen](copy-outputs.md)voor meer informatie.

## <a name="linked-templates"></a>Gekoppelde sjablonen

Als u de uitvoerwaarde wilt ophalen uit een gekoppelde sjabloon, gebruikt u de [referentiefunctie](template-functions-resource.md#reference) in de bovenliggende sjabloon. De syntaxis in de bovenliggende sjabloon is:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Wanneer u een uitvoereigenschap van een gekoppelde sjabloon krijgt, kan de eigenschapsnaam geen streepje bevatten.

In het volgende voorbeeld ziet u hoe u het IP-adres instelt op een load balancer door een waarde uit een gekoppelde sjabloon op te halen.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

U de `reference` functie niet gebruiken in het gedeelte uitvoer van een [geneste sjabloon.](linked-templates.md#nested-template) Als u de waarden voor een geïmplementeerde resource wilt retourneren in een geneste sjabloon, converteert u uw geneste sjabloon naar een gekoppelde sjabloon.

## <a name="get-output-values"></a>Uitvoerwaarden ophalen

Wanneer de implementatie slaagt, worden de uitvoerwaarden automatisch geretourneerd in de resultaten van de implementatie.

Als u uitvoerwaarden uit de implementatiegeschiedenis wilt halen, u script gebruiken.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Voorbeeldsjablonen

In de volgende voorbeelden worden scenario's gedemonstreerd voor het gebruik van uitvoer.

|Template  |Beschrijving  |
|---------|---------|
|[Variabelen kopiëren](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Hiermee maakt u complexe variabelen en worden deze waarden uitgevoerd. Zet geen resources in. |
|[Openbaar IP-adres](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Hiermee maakt u een openbaar IP-adres en wordt de resource-id uitgevoerd. |
|[Load balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Koppelingen naar de voorgaande sjabloon. Gebruikt de resource-id in de uitvoer bij het maken van de load balancer. |

## <a name="next-steps"></a>Volgende stappen

* Zie [De structuur en de syntaxis van Azure Resource Manager-sjablonen begrijpen](template-syntax.md)voor meer informatie over de beschikbare eigenschappen voor uitvoer.
