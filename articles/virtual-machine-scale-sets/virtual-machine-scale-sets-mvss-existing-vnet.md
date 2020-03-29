---
title: Een bestaand virtueel netwerk in een sjabloon voor Azure-schaalset verwijzen
description: Meer informatie over het toevoegen van een virtueel netwerk aan een bestaande sjabloon voor azure-sjabloon voor het schalen van virtuele machines
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: e725e75b8b19fd8b3295226639b5e5aeb3736e34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275537"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Verwijzing naar een bestaand virtueel netwerk toevoegen aan een sjabloon voor Azure-schaalsets

In dit artikel ziet u hoe u de [sjabloon voor basisschaalset kunt](virtual-machine-scale-sets-mvss-start.md) wijzigen om te implementeren in een bestaand virtueel netwerk in plaats van een nieuw netwerk te maken.

## <a name="change-the-template-definition"></a>De sjabloondefinitie wijzigen

In een [vorig artikel](virtual-machine-scale-sets-mvss-start.md) hadden we een basissjabloon voor schaalsets gemaakt. We gebruiken die eerdere sjabloon nu en wijzigen deze om een sjabloon te maken die een schaalset implementeert in een bestaand virtueel netwerk. 

Voeg eerst `subnetId` een parameter toe. Deze tekenreeks wordt doorgegeven in de configuratie van de schaalset, zodat de schaalset het vooraf gemaakte subnet kan identificeren om virtuele machines in te zetten. Deze tekenreeks moet van het formulier zijn:`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Als u bijvoorbeeld de schaalset wilt implementeren `myvnet`in een `mysubnet`bestaand `myrg`virtueel netwerk `00000000-0000-0000-0000-000000000000`met naam , subnet, resourcegroep en abonnement, zou de subnetId zijn: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`.

```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "subnetId": {
+      "type": "string"
     }
   },
```

Verwijder vervolgens de virtuele netwerkbron uit de `resources` array, omdat u een bestaand virtueel netwerk gebruikt en geen nieuw netwerk hoeft te implementeren.

```diff
   "variables": {},
   "resources": [
-    {
-      "type": "Microsoft.Network/virtualNetworks",
-      "name": "myVnet",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "2018-11-01",
-      "properties": {
-        "addressSpace": {
-          "addressPrefixes": [
-            "10.0.0.0/16"
-          ]
-        },
-        "subnets": [
-          {
-            "name": "mySubnet",
-            "properties": {
-              "addressPrefix": "10.0.0.0/16"
-            }
-          }
-        ]
-      }
-    },
```

Het virtuele netwerk bestaat al voordat de sjabloon wordt geïmplementeerd, dus het is niet nodig om een dependsOn-clausule op te geven van de schaal die is ingesteld op het virtuele netwerk. Verwijder de volgende regels:

```diff
     {
       "type": "Microsoft.Compute/virtualMachineScaleSets",
       "name": "myScaleSet",
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01",
-      "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
-      ],
       "sku": {
         "name": "Standard_A1",
         "capacity": 2
```

Tot slot, `subnetId` pass in de parameter ingesteld `resourceId` door de gebruiker (in plaats van te gebruiken om de ID van een vnet te krijgen in dezelfde implementatie, dat is wat de fundamentele levensvatbare schaal set sjabloon doet).

```diff
                       "name": "myIpConfig",
                       "properties": {
                         "subnet": {
-                          "id": "[concat(resourceId('Microsoft.Network/virtualNetworks', 'myVnet'), '/subnets/mySubnet')]"
+                          "id": "[parameters('subnetId')]"
                         }
                       }
                     }
```




## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
