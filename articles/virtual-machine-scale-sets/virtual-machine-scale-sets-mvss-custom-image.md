---
title: Verwijzen naar een aangepaste installatie kopie in een sjabloon van een Azure-schaalset
description: Meer informatie over het toevoegen van een aangepaste installatie kopie aan een bestaande Azure virtual machine Scale set-sjabloon
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimckitt
ms.openlocfilehash: 3965090239949b5e1116ceebe427728e49ffafe4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81273692"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Een aangepaste installatiekopie toevoegen aan een Azure-schaalsetsjabloon

In dit artikel wordt beschreven hoe u de [basisschaalset-sjabloon](virtual-machine-scale-sets-mvss-start.md) kunt aanpassen om te implementeren vanuit een aangepaste installatie kopie.

## <a name="change-the-template-definition"></a>De sjabloon definitie wijzigen
In een [vorig artikel](virtual-machine-scale-sets-mvss-start.md) moesten we een basisschaalset-sjabloon maken. We gaan nu die eerdere sjabloon gebruiken en deze wijzigen om een sjabloon te maken waarmee een schaalset vanuit een aangepaste installatie kopie wordt geïmplementeerd.  

### <a name="creating-a-managed-disk-image"></a>Een beheerde schijf installatie kopie maken

Als u al een aangepaste installatie kopie van een beheerde schijf (een bron `Microsoft.Compute/images`van het type) hebt, kunt u deze sectie overs Laan.

Voeg eerst een `sourceImageVhdUri` para meter toe, wat de URI is van de gegeneraliseerde blob in azure Storage die de aangepaste installatie kopie bevat die moet worden geïmplementeerd.


```diff
     },
     "adminPassword": {
       "type": "securestring"
+    },
+    "sourceImageVhdUri": {
+      "type": "string",
+      "metadata": {
+        "description": "The source of the generalized blob containing the custom image"
+      }
     }
   },
   "variables": {},
```

Voeg vervolgens een bron van het type `Microsoft.Compute/images`, die de installatie kopie van de beheerde schijf is, toe op basis van `sourceImageVhdUri`de algemene blob die zich op de URI bevindt. Deze installatie kopie moet zich in dezelfde regio bevinden als de schaalset die deze gebruikt. Geef in de eigenschappen van de installatie kopie het type besturings systeem op, de locatie van de BLOB ( `sourceImageVhdUri` van de para meter) en het type opslag account:

```diff
   "resources": [
     {
+      "type": "Microsoft.Compute/images",
+      "apiVersion": "2019-03-01",
+      "name": "myCustomImage",
+      "location": "[resourceGroup().location]",
+      "properties": {
+        "storageProfile": {
+          "osDisk": {
+            "osType": "Linux",
+            "osState": "Generalized",
+            "blobUri": "[parameters('sourceImageVhdUri')]",
+            "storageAccountType": "Standard_LRS"
+          }
+        }
+      }
+    },
+    {
       "type": "Microsoft.Network/virtualNetworks",
       "name": "myVnet",
       "location": "[resourceGroup().location]",

```

Voeg in de resource voor de schaalset `dependsOn` een-component toe die verwijst naar de aangepaste afbeelding om te controleren of de afbeelding wordt gemaakt voordat de schaalset op die installatie kopie probeert te implementeren:

```diff
       "location": "[resourceGroup().location]",
       "apiVersion": "2019-03-01-preview",
       "dependsOn": [
-        "Microsoft.Network/virtualNetworks/myVnet"
+        "Microsoft.Network/virtualNetworks/myVnet",
+        "Microsoft.Compute/images/myCustomImage"
       ],
       "sku": {
         "name": "Standard_A1",

```

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Eigenschappen van schaal sets wijzigen voor het gebruik van de beheerde schijf installatie kopie

In de `imageReference` schaalset `storageProfile`, in plaats van de uitgever, aanbieding, SKU en versie van een platform installatie kopie op te geven, geeft `id` u de `Microsoft.Compute/images` bron op:

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

In dit voor beeld gebruikt u `resourceId` de functie om de resource-id op te halen van de afbeelding die in dezelfde sjabloon is gemaakt. Als u de installatie kopie van de beheerde schijf al eerder hebt gemaakt, moet u in plaats daarvan de ID van die afbeelding opgeven. Deze ID moet de volgende indeling hebben: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
