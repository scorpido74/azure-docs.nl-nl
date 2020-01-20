---
title: Verwijzen naar een aangepaste installatie kopie in een sjabloon van een Azure-schaalset
description: Meer informatie over het toevoegen van een aangepaste installatie kopie aan een bestaande Azure virtual machine Scale set-sjabloon
author: mayanknayar
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: manayar
ms.openlocfilehash: fd1a567af1c35cf6b659995e998b11a61a526508
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275590"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Een aangepaste installatie kopie toevoegen aan een sjabloon van Azure-schaal sets

In dit artikel wordt beschreven hoe u de [basisschaalset-sjabloon](virtual-machine-scale-sets-mvss-start.md) kunt aanpassen om te implementeren vanuit een aangepaste installatie kopie.

## <a name="change-the-template-definition"></a>De sjabloon definitie wijzigen
In een [vorig artikel](virtual-machine-scale-sets-mvss-start.md) moesten we een basisschaalset-sjabloon maken. We gaan nu die eerdere sjabloon gebruiken en deze wijzigen om een sjabloon te maken waarmee een schaalset vanuit een aangepaste installatie kopie wordt geïmplementeerd.  

### <a name="creating-a-managed-disk-image"></a>Een beheerde schijf installatie kopie maken

Als u al een aangepaste installatie kopie van een beheerde schijf (een resource van het type `Microsoft.Compute/images`) hebt, kunt u deze sectie overs Laan.

Voeg eerst een `sourceImageVhdUri` para meter toe. Dit is de URI naar de gegeneraliseerde Blob in Azure Storage die de aangepaste installatie kopie bevat die moet worden geïmplementeerd.


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

Voeg vervolgens een bron van het type `Microsoft.Compute/images`toe. Dit is de beheerde schijf installatie kopie op basis van de gegeneraliseerde Blob op de URI `sourceImageVhdUri`. Deze installatie kopie moet zich in dezelfde regio bevinden als de schaalset die deze gebruikt. Geef in de eigenschappen van de installatie kopie het type besturings systeem op, de locatie van de BLOB (van de para meter `sourceImageVhdUri`) en het type opslag account:

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

Voeg in de resource voor de schaalset een `dependsOn`-component toe die verwijst naar de aangepaste installatie kopie om ervoor te zorgen dat de installatie kopie wordt gemaakt voordat de schaalset op die installatie kopie probeert te implementeren:

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

In de `imageReference` van de schaalset `storageProfile`, in plaats van de uitgever, aanbieding, SKU en versie van een platform installatie kopie op te geven, geeft u de `id` van de `Microsoft.Compute/images`-resource op:

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

In dit voor beeld gebruikt u de functie `resourceId` om de resource-ID op te halen van de afbeelding die in dezelfde sjabloon is gemaakt. Als u de installatie kopie van de beheerde schijf al eerder hebt gemaakt, moet u in plaats daarvan de ID van die afbeelding opgeven. Deze ID moet de volgende indeling hebben: `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`.


## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
