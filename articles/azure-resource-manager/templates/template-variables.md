---
title: Variabelen in sjablonen
description: Beschrijft hoe u variabelen definieert in een Azure Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 09/05/2019
ms.openlocfilehash: cf135959d30702ea58b7a1d4fdd82625a39245d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483815"
---
# <a name="variables-in-azure-resource-manager-template"></a>Variabelen in azure resource manager-sjabloon

In dit artikel wordt beschreven hoe u variabelen in uw Azure Resource Manager-sjabloon definiëren en gebruiken. U gebruikt variabelen om uw sjabloon te vereenvoudigen. In plaats van ingewikkelde expressies in uw sjabloon te herhalen, definieert u een variabele die de ingewikkelde expressie bevat. Vervolgens verwijst u naar die variabele als dat nodig is in uw sjabloon.

Resourcemanager lost variabelen op voordat de implementatiebewerkingen worden gestart. Waar de variabele in de sjabloon wordt gebruikt, vervangt Resource Manager deze door de opgeloste waarde.

## <a name="define-variable"></a>Variabele definiëren

In het volgende voorbeeld ziet u een variabele definitie. Hiermee wordt een tekenreekswaarde gemaakt voor een naam van een opslagaccount. Het maakt gebruik van verschillende sjabloonfuncties om een parameterwaarde te krijgen en voegt deze toe aan een unieke tekenreeks.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

U de [referentiefunctie](template-functions-resource.md#reference) of een van de [lijstfuncties](template-functions-resource.md#list) in de sectie variabelen niet gebruiken. Deze functies krijgen de runtime-status van een resource en kunnen niet worden uitgevoerd voordat de implementatie wordt uitgevoerd wanneer variabelen zijn opgelost.

## <a name="use-variable"></a>Variabele gebruiken

In de sjabloon verwijst u naar de waarde voor de parameter met behulp van de functie [Variabelen.](template-functions-deployment.md#variables) In het volgende voorbeeld ziet u hoe u de variabele voor een resourceeigenschap gebruikt.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

## <a name="configuration-variables"></a>Configuratievariabelen

U variabelen definiëren met gerelateerde waarden voor het configureren van een omgeving. U definieert de variabele als een object met de waarden. In het volgende voorbeeld wordt een object weergegeven dat waarden bevat voor twee omgevingen - **test** en **prik .**

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

In parameters maakt u een waarde die aangeeft welke configuratiewaarden u wilt gebruiken.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Als u instellingen voor de opgegeven omgeving wilt ophalen, gebruikt u de variabele en de parameter samen.

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="example-templates"></a>Voorbeeldsjablonen

In de volgende voorbeelden worden scenario's gedemonstreerd voor het gebruik van variabelen.

|Template  |Beschrijving  |
|---------|---------|
| [variabele definities](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Toont de verschillende soorten variabelen. De sjabloon implementeert geen resources. Het construeert variabele waarden en retourneert deze waarden. |
| [configuratievariabele](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Hiermee wordt het gebruik van een variabele aangetoond die configuratiewaarden definieert. De sjabloon implementeert geen resources. Het construeert variabele waarden en retourneert deze waarden. |
| [netwerkbeveiligingsregels](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) en [parameterbestand](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Hiermee wordt een array in de juiste indeling gebouwd voor het toewijzen van beveiligingsregels aan een netwerkbeveiligingsgroep. |

## <a name="next-steps"></a>Volgende stappen

* Zie [De structuur en syntaxis van Azure Resource Manager-sjablonen begrijpen](template-syntax.md)voor meer informatie over de beschikbare eigenschappen voor variabelen.
* Zie Aanbevolen procedures - [variabelen](template-best-practices.md#variables)voor aanbevelingen over het maken van variabelen.
