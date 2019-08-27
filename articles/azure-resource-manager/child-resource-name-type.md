---
title: Naam en type voor onderliggende resources in Azure Resource Manager sjablonen
description: Hierin wordt beschreven hoe u de naam en het type instelt voor onderliggende resources in een Azure Resource Manager sjabloon.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: tomfitz
ms.openlocfilehash: bd1c38547866c34eb5c4808ed5b185b50fa44a01
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035567"
---
# <a name="set-name-and-type-for-child-resources"></a>Naam en type voor onderliggende resources instellen

Onderliggende resources zijn bronnen die alleen bestaan in de context van een andere resource. Een extensie van een [virtuele machine](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) kan bijvoorbeeld niet bestaan zonder een [virtuele machine](/azure/templates/microsoft.compute/2019-03-01/virtualmachines). De extensie resource is een onderliggend element van de virtuele machine.

In een resource manager-sjabloon kunt u de onderliggende resource opgeven binnen de bovenliggende resource of buiten de bovenliggende resource. In het volgende voor beeld ziet u de onderliggende resource die is opgenomen in de eigenschap resources van de bovenliggende resource.

```json
"resources": [
  {
    <parent-resource>
    "resources": [
      <child-resource>
    ]
  }
]
```

In het volgende voor beeld wordt de onderliggende resource buiten de bovenliggende resource weer gegeven. U kunt deze aanpak gebruiken als de bovenliggende resource niet is geïmplementeerd in dezelfde sjabloon of als u een [kopie](resource-group-create-multiple.md) wilt gebruiken om meer dan één onderliggende resource te maken.

```json
"resources": [
  {
    <parent-resource>
  },
  {
    <child-resource>
  }
]
```

De waarden die u opgeeft voor de resource naam en het-type variëren afhankelijk van het feit of de onderliggende resource binnen of buiten de bovenliggende resource is gedefinieerd.

## <a name="within-parent-resource"></a>In bovenliggende resource

Wanneer u de waarde definieert in het bovenliggende resource type, formatteert u het type en de naam waarden als één enkel woord zonder slashes.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

In het volgende voor beeld ziet u een virtueel netwerk en een subnet. U ziet dat het subnet is opgenomen in de bronnen matrix voor het virtuele netwerk. De naam is ingesteld op **Subnet1** en het type is ingesteld op **subnetten**. De onderliggende resource is gemarkeerd als afhankelijk van de bovenliggende resource, omdat de bovenliggende resource moet bestaan voordat de onderliggende resource kan worden geïmplementeerd.

```json
"resources": [
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    },
    "resources": [
      {
        "apiVersion": "2018-10-01",
        "type": "subnets",
        "location": "[parameters('location')]",
        "name": "Subnet1",
        "dependsOn": [
          "VNet1"
        ],
        "properties": {
          "addressPrefix": "10.0.0.0/24"
        }
      }
    ]
  }
]
```

Het volledige bron type is nog steeds **micro soft. Network/virtualNetworks/subnets**. U beschikt niet over **micro soft. Network/virtualNetworks/** , omdat deze wordt aangenomen van het bovenliggende bron type.

De naam van de onderliggende resource is ingesteld op **Subnet1** , maar de volledige naam bevat de naam van het bovenliggende item. U geeft **VNet1** niet op omdat deze wordt aangenomen van de bovenliggende resource.

## <a name="outside-parent-resource"></a>Buiten bovenliggende resource

Wanneer u buiten de bovenliggende resource hebt gedefinieerd, formatteert u het type en met slashes om het bovenliggende type en de naam op te laten bevatten.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

In het volgende voor beeld ziet u een virtueel netwerk en een subnet dat beide zijn gedefinieerd op het hoofd niveau. U ziet dat het subnet niet is opgenomen in de bronnen matrix voor het virtuele netwerk. De naam is ingesteld op **VNet1/Subnet1** en het type is ingesteld op **micro soft. Network/virtualNetworks/subnets**. De onderliggende resource is gemarkeerd als afhankelijk van de bovenliggende resource, omdat de bovenliggende resource moet bestaan voordat de onderliggende resource kan worden geïmplementeerd.

```json
"resources": [
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "VNet1",
    "location": "[parameters('location')]",
    "properties": {
      "addressSpace": {
        "addressPrefixes": [
          "10.0.0.0/16"
        ]
      }
    }
  },
  {
    "apiVersion": "2018-10-01",
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "location": "[parameters('location')]",
    "name": "VNet1/Subnet1",
    "dependsOn": [
      "VNet1"
    ],
    "properties": {
      "addressPrefix": "10.0.0.0/24"
    }
  }
]
```

## <a name="next-steps"></a>Volgende stappen

* Zie [ontwerp sjablonen](resource-group-authoring-templates.md)voor meer informatie over het maken van Azure Resource Manager sjablonen. 

* Zie de [functie Reference](resource-group-template-functions-resource.md#reference)voor meer informatie over de indeling van de resource naam bij het verwijzen naar de resource.
