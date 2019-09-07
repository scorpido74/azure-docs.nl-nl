---
title: Door de gebruiker gedefinieerde functies in Azure Resource Manager sjabloon
description: Hierin wordt beschreven hoe u door de gebruiker gedefinieerde functies definieert en gebruikt in een Azure Resource Manager sjabloon.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 227ecb57b125264e7d0eba56e634966d677e0f58
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70387287"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Door de gebruiker gedefinieerde functies in Azure Resource Manager sjabloon

U kunt binnen uw sjabloon uw eigen functies maken. Deze functies zijn beschikbaar voor gebruik in uw sjabloon. Door de gebruiker gedefinieerde functies zijn gescheiden van de [standaard sjabloon functies](resource-group-template-functions.md) die automatisch beschikbaar zijn in uw sjabloon. Maak uw eigen functies wanneer u gecompliceerde expressies hebt die herhaaldelijk worden gebruikt in uw sjabloon.

In dit artikel wordt beschreven hoe u door de gebruiker gedefinieerde functies in uw Azure Resource Manager-sjabloon kunt toevoegen.

## <a name="define-the-function"></a>Definieer de functie

Uw functies vereisen een naam ruimte waarde om naam conflicten met sjabloon functies te voor komen. In het volgende voor beeld ziet u een functie die de naam van een opslag account retourneert:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

## <a name="use-the-function"></a>Gebruik de functie

In het volgende voor beeld ziet u hoe u uw functie aanroept.

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="limitations"></a>Beperkingen

Bij het definiëren van een gebruikers functie gelden enkele beperkingen:

* De functie heeft geen toegang tot variabelen.
* De functie kan alleen para meters gebruiken die in de functie zijn gedefinieerd. Wanneer u de functie [para meters](resource-group-template-functions-deployment.md#parameters) in een door de gebruiker gedefinieerde functie gebruikt, bent u beperkt tot de para meters voor die functie.
* De functie kan geen andere door de gebruiker gedefinieerde functies aanroepen.
* De functie kan de functie [Reference](resource-group-template-functions-resource.md#reference) of een van de [lijst](resource-group-template-functions-resource.md#list) functies niet gebruiken.
* Para meters voor de functie kunnen geen standaard waarden hebben.


## <a name="next-steps"></a>Volgende stappen

* Zie [inzicht krijgen in de structuur en de syntaxis van Azure Resource Manager-sjablonen](resource-group-authoring-templates.md)voor meer informatie over de beschik bare eigenschappen voor door de gebruiker gedefinieerde functies.
* Zie [Azure Resource Manager-sjabloon functies](resource-group-template-functions.md)voor een lijst met de beschik bare sjabloon functies.
