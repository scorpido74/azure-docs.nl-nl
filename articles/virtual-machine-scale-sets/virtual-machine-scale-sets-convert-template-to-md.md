---
title: Een sjabloon voor schaalsets converteren voor gebruik van beheerde schijven
description: Converteer een sjabloon voor de sjabloon voor de schan de schade machineschaal van Azure Resource Manager naar een sjabloon voor beheerde schijfschaalset.
keywords: schaalsets voor virtuele machines
author: mayanknayar
tags: azure-resource-manager
ms.assetid: bc8c377a-8c3f-45b8-8b2d-acc2d6d0b1e8
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 5/18/2017
ms.author: manayar
ms.openlocfilehash: 4ab5c48c6673a2353c70fe808d09aa15675e0424
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278132"
---
# <a name="convert-a-scale-set-template-to-a-managed-disk-scale-set-template"></a>Een sjabloon voor een schaalset converteren naar een sjabloon voor beheerde schijfschaalset

Klanten met een Resource Manager-sjabloon voor het maken van een schaalset die geen beheerde schijf gebruikt, kunnen deze wijzigen om beheerde schijf te gebruiken. In dit artikel ziet u hoe u beheerde schijven gebruikt, met als voorbeeld een pull-aanvraag van de [Azure Quickstart-sjablonen](https://github.com/Azure/azure-quickstart-templates), een door de community gestuurde repo voor voorbeeldresourcebeheersjablonen. De volledige pull verzoek kan [https://github.com/Azure/azure-quickstart-templates/pull/2998](https://github.com/Azure/azure-quickstart-templates/pull/2998)hier worden gezien: , en de relevante delen van de diff zijn hieronder, samen met uitleg:

## <a name="making-the-os-disks-managed"></a>De OS-schijven beheerd maken

In de volgende diff worden verschillende variabelen met betrekking tot opslagaccount- en schijfeigenschappen verwijderd. Opslagaccounttype is niet langer nodig (Standard_LRS is de standaardinstelling), maar u het desgewenst opgeven. Alleen Standard_LRS en Premium_LRS worden ondersteund met beheerde schijf. Nieuw achtervoegsel voor opslagaccount, unieke tekenreeksarray en sa-telling werden in de oude sjabloon gebruikt om namen van opslagaccounten te genereren. Deze variabelen zijn niet langer nodig in de nieuwe sjabloon, omdat beheerde schijf automatisch opslagaccounts maakt namens de klant. Op dezelfde manier zijn de naam vhd-container en de naam van de OS-schijf niet langer nodig, omdat de beheerde schijf automatisch de onderliggende opslagblobcontainers en -schijven noemt.

```diff
   "variables": {
-    "storageAccountType": "Standard_LRS",
     "namingInfix": "[toLower(substring(concat(parameters('vmssName'), uniqueString(resourceGroup().id)), 0, 9))]",
     "longNamingInfix": "[toLower(parameters('vmssName'))]",
-    "newStorageAccountSuffix": "[concat(variables('namingInfix'), 'sa')]",
-    "uniqueStringArray": [
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '0')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '1')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '2')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '3')))]",
-      "[concat(uniqueString(concat(resourceGroup().id, variables('newStorageAccountSuffix'), '4')))]"
-    ],
-    "saCount": "[length(variables('uniqueStringArray'))]",
-    "vhdContainerName": "[concat(variables('namingInfix'), 'vhd')]",
-    "osDiskName": "[concat(variables('namingInfix'), 'osdisk')]",
     "addressPrefix": "10.0.0.0/16",
     "subnetPrefix": "10.0.0.0/24",
     "virtualNetworkName": "[concat(variables('namingInfix'), 'vnet')]",
```


In de volgende diff wordt de COMPUTE API bijgewerkt naar versie 2016-04-30-preview, de vroegst vereiste versie voor beheerde schijfondersteuning met schaalsets. U onbeheerde schijven gebruiken in de nieuwe API-versie met de oude syntaxis indien gewenst. Als u alleen de compute API-versie bijwerkt en niets anders wijzigt, moet de sjabloon blijven werken zoals voorheen.

```diff
@@ -86,7 +74,7 @@
       "version": "latest"
     },
     "imageReference": "[variables('osType')]",
-    "computeApiVersion": "2016-03-30",
+    "computeApiVersion": "2016-04-30-preview",
     "networkApiVersion": "2016-03-30",
     "storageApiVersion": "2015-06-15"
   },
```

In de volgende diff wordt de bron van het opslagaccount volledig uit de array resources verwijderd. De resource is niet langer nodig omdat de beheerde schijf ze automatisch maakt.

```diff
@@ -113,19 +101,6 @@
       }
     },
-    {
-      "type": "Microsoft.Storage/storageAccounts",
-      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",
-      "location": "[resourceGroup().location]",
-      "apiVersion": "[variables('storageApiVersion')]",
-      "copy": {
-        "name": "storageLoop",
-        "count": "[variables('saCount')]"
-      },
-      "properties": {
-        "accountType": "[variables('storageAccountType')]"
-      }
-    },
     {
       "type": "Microsoft.Network/publicIPAddresses",
       "name": "[variables('publicIPAddressName')]",
       "location": "[resourceGroup().location]",
```

In de volgende diff kunnen we zien dat we het verwijderen van de afhankelijke clausule die verwijst van de schaal ingesteld op de lus die was het creëren van opslagaccounts. In de oude sjabloon werd ervoor gezorgd dat de opslagaccounts zijn gemaakt voordat de schaalset werd gemaakt, maar deze clausule is niet langer nodig met beheerde schijf. De eigenschap vhd-containers wordt ook verwijderd, samen met de eigenschap OS-schijfnaam, omdat deze eigenschappen automatisch onder de motorkap worden afgehandeld door beheerde schijf. Je zou `"managedDisk": { "storageAccountType": "Premium_LRS" }` kunnen toevoegen in de "osDisk" configuratie als je wilde premium OS schijven. Alleen VM's met een hoofdletter of kleine 's' in de VM sku kunnen premium schijven gebruiken.

```diff
@@ -183,7 +158,6 @@
       "location": "[resourceGroup().location]",
       "apiVersion": "[variables('computeApiVersion')]",
       "dependsOn": [
-        "storageLoop",
         "[concat('Microsoft.Network/loadBalancers/', variables('loadBalancerName'))]",
         "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
       ],
@@ -200,16 +174,8 @@
         "virtualMachineProfile": {
           "storageProfile": {
             "osDisk": {
-              "vhdContainers": [
-                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",
-                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"
-              ],
-              "name": "[variables('osDiskName')]",
             },
             "imageReference": "[variables('imageReference')]"
           },

```

Er is geen expliciete eigenschap in de configuratie van de schaalset voor het al dan niet beheerde schijf gebruik. De schaalset weet welke te gebruiken op basis van de eigenschappen die aanwezig zijn in het opslagprofiel. Het is dus belangrijk bij het wijzigen van de sjabloon om ervoor te zorgen dat de juiste eigenschappen zich in het opslagprofiel van de schaalset bevinden.


## <a name="data-disks"></a>Gegevensschijven

Met de bovenstaande wijzigingen gebruikt de schaalset beheerde schijven voor de osschijf, maar hoe zit het met gegevensschijven? Als u gegevensschijven wilt toevoegen, voegt u de eigenschap "dataDisks" toe onder 'storageProfile' op hetzelfde niveau als 'osDisk'. De waarde van de eigenschap is een JSON-lijst met objecten, die elk eigenschappen "lun" hebben (die uniek moeten zijn per gegevensschijf op een VM), "createOption" ("leeg" is momenteel de enige ondersteunde optie) en "diskSizeGB" (de grootte van de schijf in gigabytes; moet groter zijn dan 0 en minder dan 1024) zoals in het volgende voorbeeld:

```
"dataDisks": [
  {
    "lun": "1",
    "createOption": "empty",
    "diskSizeGB": "1023"
  }
]
```

Als u `n` schijven in deze array opgeeft, `n` krijgt elke vm in de schaalset gegevensschijven. Houd er echter rekening mee dat deze gegevensschijven ruwe apparaten zijn. Ze zijn niet geformatteerd. Het is aan de klant om de schijven te koppelen, te partitioneren en op te maken voordat u ze gebruikt. Optioneel u in `"managedDisk": { "storageAccountType": "Premium_LRS" }` elk object met gegevensschijf ook opgeven dat het een premium gegevensschijf moet zijn. Alleen VM's met een hoofdletter of kleine 's' in de VM sku kunnen premium schijven gebruiken.

Zie [dit artikel](./virtual-machine-scale-sets-attached-disks.md)voor meer informatie over het gebruik van gegevensschijven met schaalsets.


## <a name="next-steps"></a>Volgende stappen
Zoek bijvoorbeeld naar Resource Manager-sjablonen met schaalsets in de [Azure Quickstart Templates GitHub repo.](https://github.com/Azure/azure-quickstart-templates)

Voor algemene informatie, kijk op de [belangrijkste bestemmingspagina voor schaalsets](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

