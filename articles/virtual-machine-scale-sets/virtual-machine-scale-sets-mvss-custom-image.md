---
title: Een aangepaste afbeelding in een azure-schaalsetsjabloon verwijzen
description: Meer informatie over het toevoegen van een aangepaste afbeelding aan een bestaande sjabloon voor azure-sjabloon voor het schalen van virtuele machines
author: mimckitt
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: mimckitt
ms.openlocfilehash: 3965090239949b5e1116ceebe427728e49ffafe4
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273692"
---
# <a name="add-a-custom-image-to-an-azure-scale-set-template"></a>Een aangepaste installatiekopie toevoegen aan een Azure-schaalsetsjabloon

In dit artikel ziet u hoe u de [sjabloon voor basisschaalsets kunt](virtual-machine-scale-sets-mvss-start.md) wijzigen om te implementeren vanuit aangepaste afbeelding.

## <a name="change-the-template-definition"></a>De sjabloondefinitie wijzigen
In een [vorig artikel](virtual-machine-scale-sets-mvss-start.md) hadden we een basissjabloon voor schaalsets gemaakt. We gebruiken die eerdere sjabloon nu en wijzigen deze om een sjabloon te maken die een schaalset implementeert vanuit een aangepaste afbeelding.  

### <a name="creating-a-managed-disk-image"></a>Een beheerde schijfafbeelding maken

Als u al een aangepaste beheerde schijfafbeelding hebt (een bron van type), `Microsoft.Compute/images`u deze sectie overslaan.

Voeg eerst `sourceImageVhdUri` een parameter toe, de URI aan de gegeneraliseerde blob in Azure Storage die de aangepaste afbeelding bevat die moet worden geïmplementeerd.


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

Voeg vervolgens een bron `Microsoft.Compute/images`van het type toe, de beheerde schijfafbeelding op basis van de gegeneraliseerde blob bij URI `sourceImageVhdUri`. Deze afbeelding moet zich in hetzelfde gebied bevinden als de schaalset die deze gebruikt. Geef in de eigenschappen van de afbeelding het ostype, `sourceImageVhdUri` de locatie van de blob (van de parameter) en het type opslagaccount op:

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

Voeg in de schaalsetbron een `dependsOn` clausule toe die verwijst naar de aangepaste afbeelding om ervoor te zorgen dat de afbeelding wordt gemaakt voordat de schaalset vanuit die afbeelding probeert te worden geïmplementeerd:

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

### <a name="changing-scale-set-properties-to-use-the-managed-disk-image"></a>Eigenschappen van schaalset wijzigen om de beheerde schijfafbeelding te gebruiken

Geef `imageReference` in de `storageProfile`van de schaalset in plaats van de uitgever de aanbieding, `id` sku `Microsoft.Compute/images` en versie van een platformafbeelding op te geven, de vermelding van de bron:

```json
         "virtualMachineProfile": {
           "storageProfile": {
             "imageReference": {
              "id": "[resourceId('Microsoft.Compute/images', 'myCustomImage')]"
             }
           },
           "osProfile": {
```

Gebruik in dit `resourceId` voorbeeld de functie om de resource-id van de afbeelding die in dezelfde sjabloon is gemaakt, op te halen. Als u de beheerde schijfafbeelding vooraf hebt gemaakt, moet u in plaats daarvan de id van die afbeelding verstrekken. Deze id moet van `/subscriptions/<subscription-id>resourceGroups/<resource-group-name>/providers/Microsoft.Compute/images/<image-name>`het formulier zijn: .


## <a name="next-steps"></a>Volgende stappen

[!INCLUDE [mvss-next-steps-include](../../includes/mvss-next-steps.md)]
