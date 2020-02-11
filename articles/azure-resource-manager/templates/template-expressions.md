---
title: Sjabloon syntaxis en expressies
description: Beschrijft de declaratieve JSON-syntaxis voor Azure Resource Manager sjablonen.
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 42649d4b04b03de32b82335fce68401192de75a3
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120596"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Syntaxis en expressies in Azure Resource Manager sjablonen

De basis syntaxis van de sjabloon is JSON. U kunt echter expressies gebruiken om de JSON-waarden uit te breiden die beschikbaar zijn in de sjabloon.  Expressies beginnen en eindigen met haakjes: respectievelijk `[` en `]`. De waarde van de expressie wordt geëvalueerd wanneer de sjabloon wordt geïmplementeerd. Een expressie kan een teken reeks, een geheel getal, een Booleaanse waarde, een matrix of een object retour neren.

Een sjabloon expressie mag niet langer zijn dan 24.576 tekens.

Expressies ondersteunen JSON (' null ') en eigenschappen bieden ondersteuning voor een letterlijke waarde van Null. In beide gevallen behandelen Resource Manager-sjablonen dit alsof de eigenschap niet aanwezig is.

## <a name="use-functions"></a>Functies gebruiken

In het volgende voor beeld ziet u een expressie in de standaard waarde van een para meter:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

Binnen de expressie roept de syntaxis `resourceGroup()` een van de functies aan die Resource Manager biedt voor gebruik in een sjabloon. In dit geval is het de functie [resourceGroup](template-functions-resource.md#resourcegroup) . Net als in Java script worden functie aanroepen opgemaakt als `functionName(arg1,arg2,arg3)`. De syntaxis `.location` haalt één eigenschap op uit het object dat door die functie wordt geretourneerd.

Sjabloon functies en de bijbehorende para meters zijn niet hoofdletter gevoelig. Met Resource Manager worden bijvoorbeeld **variabelen (' var1 ')** en **variabelen (' var1 ')** als hetzelfde omgezet. Als de functie wordt geëvalueerd, wordt de-functie in de gevallen bewaard, tenzij deze expliciet een hoofdletter gebruik wijzigt (zoals toUpper of toLower). Bepaalde resource typen kunnen Case vereisten hebben die gescheiden zijn van de manier waarop functies worden geëvalueerd.

Als u een teken reeks waarde wilt door geven als een para meter voor een functie, gebruikt u enkele aanhalings tekens.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

## <a name="escape-characters"></a>Escape tekens

Als u een letterlijke teken reeks wilt beginnen met een haakje `[` en eindigen met een haakje rechts `]`, maar niet als een expressie, voegt u een extra beugel toe om de teken reeks met `[[`te starten. Bijvoorbeeld de variabele:

```json
"demoVar1": "[[test value]"
```

Wordt omgezet in `[test value]`.

Als de letterlijke teken reeks niet eindigt met een haakje, hoeft u de eerste vier Kante haak niet te sluiten. Bijvoorbeeld de variabele:

```json
"demoVar2": "[test] value"
```

Wordt omgezet in `[test] value`.

Gebruik de back slash om dubbele aanhalings tekens in een expressie te escapen, zoals het toevoegen van een JSON-object in de sjabloon.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Resource Manager-sjabloon functies](template-functions.md)voor een volledige lijst met sjabloon functies.
* Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager-sjablonen](template-syntax.md)voor meer informatie over sjabloon bestanden.
