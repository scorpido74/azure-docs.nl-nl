---
title: Verwijzen naar een bestaand virtueel netwerk in een Azure Scale set-sjabloon
description: Meer informatie over het toevoegen van een virtueel netwerk aan een bestaande Azure virtual machine-schaal sets sjabloon
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mimckitt
ms.openlocfilehash: 83328a31dad8009c28e146c81b24d6d9244f88a8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273661"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Verwijzing naar een bestaand virtueel netwerk in een Azure-schaalset-sjabloon toevoegen

In dit artikel wordt beschreven hoe u de [basisschaalset-sjabloon](virtual-machine-scale-sets-mvss-start.md) kunt aanpassen om te implementeren in een bestaand virtueel netwerk in plaats van een nieuwe te maken.

## <a name="change-the-template-definition"></a>De sjabloon definitie wijzigen

In een [vorig artikel](virtual-machine-scale-sets-mvss-start.md) moesten we een basisschaalset-sjabloon maken. We gaan nu die eerdere sjabloon gebruiken en deze wijzigen om een sjabloon te maken waarmee een schaalset in een bestaand virtueel netwerk wordt geïmplementeerd. 

Voeg eerst een `subnetId` para meter toe. Deze teken reeks wordt door gegeven aan de configuratie van de schaalset, waardoor de schaalset het vooraf gemaakte subnet kan identificeren voor het implementeren van virtuele machines in. Deze teken reeks moet de volgende indeling hebben:`/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Als u bijvoorbeeld de schaalset wilt implementeren in een bestaand virtueel netwerk met de `myvnet`naam, `mysubnet`het subnet, `myrg`de resource groep `00000000-0000-0000-0000-000000000000`en het abonnement, zou de `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`subnetId:.

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

Verwijder vervolgens de bron van het virtuele netwerk uit `resources` de matrix, omdat u een bestaand virtueel netwerk gebruikt en niet hoeft een nieuwe te implementeren.

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

Het virtuele netwerk bestaat al voordat de sjabloon wordt geïmplementeerd. Daarom is het niet nodig om een dependsOn-component van de schaalset naar het virtuele netwerk op te geven. Verwijder de volgende regels:

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

Ga ten slotte door met `subnetId` de para meter die is ingesteld door de gebruiker `resourceId` (in plaats van te gebruiken om de id van een vnet op te halen in dezelfde implementatie. Dit is wat de basis sjabloon voor een levensvat bare schaalset is).

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
