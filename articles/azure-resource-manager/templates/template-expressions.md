---
title: Syntaxis en expressies van sjablonen
description: Beschrijft de declaratieve JSON-syntaxis voor Azure Resource Manager-sjablonen.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 172838fa24709eb60fbcb6a68277f44bbd42f01e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460106"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Syntaxis en expressies in Azure Resource Manager-sjablonen

De basissyntaxis van de sjabloon is JSON. U echter expressies gebruiken om de JSON-waarden die beschikbaar zijn in de sjabloon uit te breiden.  Expressies staan respectievelijk tussen de vierkante haken `[` en `]`. De waarde van de expressie wordt geëvalueerd als de sjabloon wordt geïmplementeerd. Een expressie kan een tekenreeks, een geheel getal, een booleaanse waarde of een object retourneren.

Een sjabloonexpressie mag niet groter zijn dan 24.576 tekens.

## <a name="use-functions"></a>Functies gebruiken

Azure Resource Manager biedt [functies](template-functions.md) die u in een sjabloon gebruiken. In het volgende voorbeeld wordt een expressie weergegeven die een functie gebruikt in de standaardwaarde van een parameter:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

Binnen de expressie `resourceGroup()` roept de syntaxis een van de functies aan die Resource manager biedt voor gebruik binnen een sjabloon. In dit geval is dit de functie [resourceGroep.](template-functions-resource.md#resourcegroup) Net als in JavaScript worden functieaanroepen opgemaakt als `functionName(arg1,arg2,arg3)`. De `.location` syntaxis haalt één eigenschap op uit het object dat door die functie wordt geretourneerd.

Sjabloonfuncties en hun parameters zijn hoofdlettergevoelig. Resource Manager lost bijvoorbeeld **variabelen('var1')** en **VARIABELEN('VAR1')** als hetzelfde op. Bij evaluatie, tenzij de functie uitdrukkelijk de case wijzigt (zoals toUpper of toLower), behoudt de functie de case. Bepaalde resourcetypen kunnen vereisten hebben die los staan van de manier waarop functies worden geëvalueerd.

Als u een tekenreekswaarde als parameter wilt doorgeven aan een functie, gebruikt u afzonderlijke aanhalingstekens.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

De meeste functies werken hetzelfde, of ze zijn geïmplementeerd in een resourcegroep, abonnement, beheergroep of tenant. De volgende functies hebben beperkingen op basis van het bereik:

* [resourceGroep](template-functions-resource.md#resourcegroup) - kan alleen worden gebruikt in implementaties naar een resourcegroep.
* [resourceId](template-functions-resource.md#resourceid) - kan worden gebruikt op elk bereik, maar de geldige parameters veranderen afhankelijk van het bereik.
* [abonnement](template-functions-resource.md#subscription) - kan alleen worden gebruikt in implementaties voor een resourcegroep of -abonnement.

## <a name="escape-characters"></a>Escape-personages

Als u een letterlijke tekenreeks `[` wilt laten beginnen `]`met een linkerbeugel en eindigt met een rechterbeugel, `[[`maar deze niet als een expressie wilt laten interpreteren, voegt u een extra beugel toe om de tekenreeks met te starten . Bijvoorbeeld, de variabele:

```json
"demoVar1": "[[test value]"
```

Lost op `[test value]`om .

Als de letterlijke tekenreeks echter niet eindigt met een beugel, ontsnap dan niet aan de eerste beugel. Bijvoorbeeld, de variabele:

```json
"demoVar2": "[test] value"
```

Lost op `[test] value`om .

Als u wilt ontsnappen aan dubbele aanhalingstekens in een expressie, zoals het toevoegen van een JSON-object in de sjabloon, gebruikt u de backslash.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Bij het doorgeven van parameterwaarden is het gebruik van vluchttekens afhankelijk van waar de parameterwaarde is opgegeven. Als u een standaardwaarde in de sjabloon instelt, hebt u de extra linkerbeugel nodig.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

Als u de standaardwaarde gebruikt, wordt de sjabloon geretourneerd `[test value]`.

Als u echter een parameterwaarde door de opdrachtregel passeert, worden de tekens letterlijk geïnterpreteerd. De vorige sjabloon implementeren met:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

Retourneert `[[test value]`. Gebruik in plaats daarvan:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

Dezelfde opmaak is van toepassing bij het doorgeven van waarden uit een parameterbestand. De personages worden letterlijk geïnterpreteerd. Bij gebruik met de voorgaande sjabloon `[test value]`wordt het volgende parameterbestand geretourneerd:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Null-waarden

Als u een eigenschap wilt instellen op null, u **null** of **[json('null')]** gebruiken. De [json-functie](template-functions-array.md#json) retourneert `null` een leeg object wanneer u als parameter opgeeft. In beide gevallen behandelen Resource Manager-sjablonen het alsof de eigenschap niet aanwezig is.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Sjabloonfuncties](template-functions.md)azure resource manager voor de volledige lijst met sjabloonfuncties .
* Zie De structuur en [syntaxis van Azure Resource Manager-sjablonen begrijpen](template-syntax.md)voor meer informatie over sjabloonbestanden.
