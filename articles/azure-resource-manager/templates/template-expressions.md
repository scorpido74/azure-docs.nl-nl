---
title: Sjabloon syntaxis en expressies
description: Beschrijft de declaratieve JSON-syntaxis voor Azure Resource Manager sjablonen.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 7bca3125f80225d2180734f483194a63e39d9cf5
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207397"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Syntaxis en expressies in Azure Resource Manager sjablonen

De basis syntaxis van de sjabloon is JSON. U kunt echter expressies gebruiken om de JSON-waarden uit te breiden die beschikbaar zijn in de sjabloon.  Expressies beginnen en eindigen met haakjes: respectievelijk `[` en `]`. De waarde van de expressie wordt geëvalueerd wanneer de sjabloon wordt geïmplementeerd. Een expressie kan een teken reeks, een geheel getal, een Booleaanse waarde, een matrix of een object retour neren.

Een sjabloon expressie mag niet langer zijn dan 24.576 tekens.

## <a name="use-functions"></a>Functies gebruiken

Azure Resource Manager biedt [functies](template-functions.md) die u in een sjabloon kunt gebruiken. In het volgende voor beeld ziet u een expressie die gebruikmaakt van een functie in de standaard waarde van een para meter:

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

De meeste functies werken hetzelfde, ongeacht of deze zijn geïmplementeerd in een resource groep, een abonnement, een beheer groep of een Tenant. Voor de volgende functies gelden beperkingen op basis van het bereik:

* [resourceGroup](template-functions-resource.md#resourcegroup) -kan alleen worden gebruikt in implementaties van een resource groep.
* [resourceId](template-functions-resource.md#resourceid) -kan in elk bereik worden gebruikt, maar de geldige para meters veranderen afhankelijk van het bereik.
* [abonnement](template-functions-resource.md#subscription) : kan alleen worden gebruikt in implementaties van een resource groep of abonnement.

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

## <a name="null-values"></a>Null-waarden

Als u een eigenschap op null wilt instellen, kunt u **Null** of **[JSON (' null ')]** gebruiken. De [json-functie](template-functions-array.md#json) retourneert een leeg object wanneer u `null` als de para meter opgeeft. In beide gevallen behandelen Resource Manager-sjablonen dit alsof de eigenschap niet aanwezig is.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Resource Manager-sjabloon functies](template-functions.md)voor een volledige lijst met sjabloon functies.
* Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager-sjablonen](template-syntax.md)voor meer informatie over sjabloon bestanden.
