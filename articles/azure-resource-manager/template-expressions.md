---
title: Syntaxis en expressies van Azure Resource Manager sjabloon
description: Beschrijft de declaratieve JSON-syntaxis voor Azure Resource Manager sjablonen.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/03/2019
ms.author: tomfitz
ms.openlocfilehash: 1e7288da19e2e81d609b952e03d5143b03a65c63
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259481"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Syntaxis en expressies in Azure Resource Manager sjablonen

De basis syntaxis van de sjabloon is JSON. U kunt echter expressies gebruiken om de JSON-waarden uit te breiden die beschikbaar zijn in de sjabloon.  Expressies beginnen en eindigen met vier Kante `[` haken `]`: en. De waarde van de expressie wordt geëvalueerd wanneer de sjabloon wordt geïmplementeerd. Een expressie kan een teken reeks, een geheel getal, een Booleaanse waarde, een matrix of een object retour neren.

Een sjabloon expressie mag niet langer zijn dan 24.576 tekens.

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

Binnen de expressie roept de syntaxis `resourceGroup()` een van de functies aan die Resource Manager biedt voor gebruik in een sjabloon. In dit geval is het de functie [resourceGroup](resource-group-template-functions-resource.md#resourcegroup) . Net als in Java script worden functie aanroepen opgemaakt `functionName(arg1,arg2,arg3)`als. De syntaxis `.location` haalt één eigenschap op uit het object dat door die functie wordt geretourneerd.

Sjabloon functies en de bijbehorende para meters zijn niet hoofdletter gevoelig. Met Resource Manager worden bijvoorbeeld **variabelen (' var1 ')** en **variabelen (' var1 ')** als hetzelfde omgezet. Als de functie wordt geëvalueerd, wordt de-functie in de gevallen bewaard, tenzij deze expliciet een hoofdletter gebruik wijzigt (zoals toUpper of toLower). Bepaalde resource typen kunnen Case vereisten hebben die gescheiden zijn van de manier waarop functies worden geëvalueerd.

Als u een teken reeks waarde wilt door geven als een para meter voor een functie, gebruikt u enkele aanhalings tekens.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

## <a name="escape-characters"></a>Escape tekens

Als u een letterlijke teken reeks begint met een `[` haakje en eindigend met een `]`haakje sluiten, maar niet als een expressie, voegt u een extra beugel toe om de teken reeks `[[`te starten met. Bijvoorbeeld de variabele:

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

* Zie [Azure Resource Manager-sjabloon functies](resource-group-template-functions.md)voor een volledige lijst met sjabloon functies.
* Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager-sjablonen](resource-group-authoring-templates.md)voor meer informatie over sjabloon bestanden.
