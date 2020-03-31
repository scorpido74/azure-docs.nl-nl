---
title: Door de gebruiker gedefinieerde functies in sjablonen
description: Beschrijft hoe u door gebruikers gedefinieerde functies definieert en gebruikt in een Azure Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 2c09572a460aa028b23987033d2b77e2aad8a0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943224"
---
# <a name="user-defined-functions-in-azure-resource-manager-template"></a>Door de gebruiker gedefinieerde functies in de sjabloon Azure Resource Manager

Binnen uw sjabloon u uw eigen functies maken. Deze functies zijn beschikbaar voor gebruik in uw sjabloon. Door de gebruiker gedefinieerde functies staan los van de [standaardsjabloonfuncties](template-functions.md) die automatisch beschikbaar zijn in uw sjabloon. Maak uw eigen functies wanneer u ingewikkelde expressies hebt die herhaaldelijk in uw sjabloon worden gebruikt.

In dit artikel wordt beschreven hoe u door de gebruiker gedefinieerde functies toevoegt aan uw Azure Resource Manager-sjabloon.

## <a name="define-the-function"></a>De functie definiëren

Voor uw functies is een naamruimtewaarde vereist om te voorkomen dat naamgeving in strijd is met sjabloonfuncties. In het volgende voorbeeld wordt een functie weergegeven die een unieke naam retourneert:

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

## <a name="use-the-function"></a>De functie gebruiken

In het volgende voorbeeld wordt een sjabloon weergegeven die een door de gebruiker gedefinieerde functie bevat. Het gebruikt die functie om een unieke naam voor een opslagaccount te krijgen. De sjabloon heeft een parameter met de naam **storageNamePrefix** die als parameter naar de functie wordt doorgegeven.

```json
{
 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
 "contentVersion": "1.0.0.0",
 "parameters": {
   "storageNamePrefix": {
     "type": "string",
     "maxLength": 11
   }
 },
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
 "resources": [
   {
     "type": "Microsoft.Storage/storageAccounts",
     "apiVersion": "2019-04-01",
     "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
     "location": "South Central US",
     "sku": {
       "name": "Standard_LRS"
     },
     "kind": "StorageV2",
     "properties": {
       "supportsHttpsTrafficOnly": true
     }
   }
 ]
}
```

## <a name="limitations"></a>Beperkingen

Bij het definiëren van een gebruikersfunctie zijn er enkele beperkingen:

* De functie heeft geen toegang tot variabelen.
* De functie kan alleen parameters gebruiken die in de functie zijn gedefinieerd. Wanneer u de [functie parameters](template-functions-deployment.md#parameters) binnen een door de gebruiker gedefinieerde functie gebruikt, bent u beperkt tot de parameters voor die functie.
* De functie kan geen andere door de gebruiker gedefinieerde functies aanroepen.
* De functie kan de [referentiefunctie](template-functions-resource.md#reference) of een van de [lijstfuncties](template-functions-resource.md#list) niet gebruiken.
* Parameters voor de functie kunnen geen standaardwaarden hebben.


## <a name="next-steps"></a>Volgende stappen

* Zie [De structuur en de syntaxis van Azure Resource Manager-sjablonen begrijpen](template-syntax.md)voor meer informatie over de beschikbare eigenschappen voor door de gebruiker gedefinieerde functies.
* Zie [Sjabloonfuncties](template-functions.md)azure resource manager voor een lijst met beschikbare sjabloonfuncties .
