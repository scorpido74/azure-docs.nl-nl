---
title: Verwijzen naar een bestaand virtueel netwerk in een Azure Scale set-sjabloon
description: Meer informatie over het toevoegen van een virtueel netwerk aan een bestaande Azure virtual machine-schaal sets sjabloon
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: manayar
ms.openlocfilehash: e725e75b8b19fd8b3295226639b5e5aeb3736e34
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275537"
---
# <a name="add-reference-to-an-existing-virtual-network-in-an-azure-scale-set-template"></a>Verwijzing naar een bestaand virtueel netwerk in een Azure-schaalset-sjabloon toevoegen

In dit artikel wordt beschreven hoe u de [basisschaalset-sjabloon](virtual-machine-scale-sets-mvss-start.md) kunt aanpassen om te implementeren in een bestaand virtueel netwerk in plaats van een nieuwe te maken.

## <a name="change-the-template-definition"></a>De sjabloon definitie wijzigen

In een [vorig artikel](virtual-machine-scale-sets-mvss-start.md) moesten we een basisschaalset-sjabloon maken. We gaan nu die eerdere sjabloon gebruiken en deze wijzigen om een sjabloon te maken waarmee een schaalset in een bestaand virtueel netwerk wordt geïmplementeerd. 

Voeg eerst een `subnetId`-para meter toe. Deze teken reeks wordt door gegeven aan de configuratie van de schaalset, waardoor de schaalset het vooraf gemaakte subnet kan identificeren voor het implementeren van virtuele machines in. Deze teken reeks moet de volgende indeling hebben: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>/subnets/<subnet-name>`

Als u bijvoorbeeld de schaalset wilt implementeren in een bestaand virtueel netwerk met de naam `myvnet`, het subnet `mysubnet`, de `myrg`van de resource groep en het abonnement `00000000-0000-0000-0000-000000000000`, zou de subnetId: `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myrg/providers/Microsoft.Network/virtualNetworks/myvnet/subnets/mysubnet`zijn.

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

Verwijder vervolgens de bron van het virtuele netwerk uit de `resources`-matrix, omdat u een bestaand virtueel netwerk gebruikt en geen nieuwe hoeft te implementeren.

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

Geef ten slotte de para meter `subnetId` door die door de gebruiker is ingesteld (in plaats van `resourceId` om de ID van een vnet in dezelfde implementatie op te halen. Dit is wat de basis sjabloon voor het maken van een schaalset is).

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
