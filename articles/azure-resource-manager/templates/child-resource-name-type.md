---
title: Onderliggende bronnen in sjablonen
description: Beschrijft hoe u de naam en het type voor onderliggende resources in stelt in een Azure Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: 3a69829e674925982c618807f49433a033d8c5f9
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743836"
---
# <a name="set-name-and-type-for-child-resources"></a>Naam en type instellen voor onderliggende bronnen

Onderliggende bronnen zijn bronnen die alleen bestaan binnen de context van een andere resource. Een extensie [van](/azure/templates/microsoft.compute/2019-03-01/virtualmachines/extensions) een virtuele machine kan bijvoorbeeld niet bestaan zonder een [virtuele machine.](/azure/templates/microsoft.compute/2019-03-01/virtualmachines) De extensiebron is een onderliggend kind van de virtuele machine.

In een sjabloon Resourcemanager u de onderliggende bron opgeven binnen de bovenliggende resource of buiten de bovenliggende resource. In het volgende voorbeeld wordt de onderliggende resource weergegeven die is opgenomen in de eigenschap resources van de bovenliggende resource.

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

In het volgende voorbeeld wordt de onderliggende resource buiten de bovenliggende resource weergegeven. U deze benadering gebruiken als de bovenliggende resource niet in dezelfde sjabloon is geïmplementeerd of als u [kopiëren](copy-resources.md) wilt gebruiken om meer dan één onderliggende bron te maken.

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

De waarden die u voor de resourcenaam en het type opgeeft, variëren afhankelijk van de vraag of de onderliggende resource binnen of buiten de bovenliggende resource is gedefinieerd.

## <a name="within-parent-resource"></a>Binnen bovenliggende bron

Wanneer u wordt gedefinieerd binnen het bovenliggende brontype, maakt u de type- en naamwaarden op als één woord zonder slashes.

```json
"type": "{child-resource-type}",
"name": "{child-resource-name}",
```

In het volgende voorbeeld ziet u een virtueel netwerk en een subnet. Merk op dat het subnet is opgenomen in de resources array voor het virtuele netwerk. De naam is ingesteld op **Subnet1** en het type is ingesteld op **subnetten**. De onderliggende resource is gemarkeerd als afhankelijk van de bovenliggende resource omdat de bovenliggende resource moet bestaan voordat de onderliggende resource kan worden geïmplementeerd.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
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
        "type": "subnets",
        "apiVersion": "2018-10-01",
        "name": "Subnet1",
        "location": "[parameters('location')]",
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

Het volledige brontype is nog steeds **Microsoft.Network/virtualNetworks/subnetten.** U verstrekt geen **Microsoft.Network/virtualNetworks/** omdat dit wordt aangenomen van het bovenliggende brontype.

De naam van de onderliggende bron is ingesteld op **Subnet1,** maar de volledige naam bevat de bovenliggende naam. U verstrekt Geen **VNet1** omdat deze wordt aangenomen uit de bovenliggende bron.

## <a name="outside-parent-resource"></a>Externe bovenliggende bron

Wanneer u buiten de bovenliggende resource wordt gedefinieerd, maakt u het type en met slashes op om het bovenliggende type en de bovenliggende naam op te nemen.

```json
"type": "{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}",
"name": "{parent-resource-name}/{child-resource-name}",
```

In het volgende voorbeeld ziet u een virtueel netwerk en subnet dat beide op basisniveau zijn gedefinieerd. Merk op dat het subnet niet is opgenomen in de array resources voor het virtuele netwerk. De naam is ingesteld op **VNet1/Subnet1** en het type is ingesteld op **Microsoft.Network/virtualNetworks/subnetten.** De onderliggende resource is gemarkeerd als afhankelijk van de bovenliggende resource omdat de bovenliggende resource moet bestaan voordat de onderliggende resource kan worden geïmplementeerd.

```json
"resources": [
  {
    "type": "Microsoft.Network/virtualNetworks",
    "apiVersion": "2018-10-01",
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
    "type": "Microsoft.Network/virtualNetworks/subnets",
    "apiVersion": "2018-10-01",
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

* Zie [Sjablonen voor ontwerpen](template-syntax.md)voor meer informatie over het maken van Azure Resource Manager-sjablonen.

* Zie de [referentiefunctie](template-functions-resource.md#reference)voor meer informatie over de indeling van de resourcenaam wanneer u naar de bron verwijst.
