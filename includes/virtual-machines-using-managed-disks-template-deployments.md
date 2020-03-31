---
title: bestand opnemen
description: bestand opnemen
services: storage
author: jboeshart
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: jaboes
ms.custom: include file
ms.openlocfilehash: 126b488d2bb59e2904bee646301240efe6fe71a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76037945"
---
Dit document loopt door de verschillen tussen beheerde en onbeheerde schijven wanneer u Azure Resource Manager-sjablonen gebruikt om virtuele machines in te richten. Met de voorbeelden u bestaande sjablonen die onbeheerde schijven gebruiken, bijwerken naar beheerde schijven. Ter referentie gebruiken we de sjabloon [101-vm-simple-windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) als leidraad. U de sjabloon zien met zowel [beheerde schijven](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) als een eerdere versie met behulp van [onbeheerde schijven](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) als u ze direct wilt vergelijken.

## <a name="unmanaged-disks-template-formatting"></a>Sjabloonopmaak voor niet-beheerde schijven

Laten we om te beginnen eens kijken hoe onbeheerde schijven worden geïmplementeerd. Bij het maken van onbeheerde schijven hebt u een opslagaccount nodig om de VHD-bestanden vast te houden. U een nieuw opslagaccount maken of een account gebruiken dat al bestaat. In dit artikel ziet u hoe u een nieuw opslagaccount maakt. Maak een opslagaccountbron in het bronnenblok zoals hieronder weergegeven.

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2018-07-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
}
```

Voeg binnen het object virtuele machine een afhankelijkheid toe van het opslagaccount om ervoor te zorgen dat het is gemaakt vóór de virtuele machine. Geef `storageProfile` in de sectie de volledige URI van de VHD-locatie op, die verwijst naar het opslagaccount en die nodig is voor de OS-schijf en eventuele gegevensschijven.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Sjabloonopmaak voor beheerde schijven

Met Azure Managed Disks wordt de schijf een bron op het hoogste niveau en hoeft er geen opslagaccount meer te worden gemaakt door de gebruiker. Beheerde schijven werden `2016-04-30-preview` voor het eerst blootgesteld in de API-versie, ze zijn beschikbaar in alle volgende API-versies en zijn nu het standaardschijftype. De volgende secties lopen door de standaardinstellingen en details hoe u uw schijven verder aanpassen.

> [!NOTE]
> Het wordt aanbevolen om een API-versie later te gebruiken dan `2016-04-30-preview` omdat er wijzigingen waren tussen `2016-04-30-preview` en `2017-03-30`.
>
>

### <a name="default-managed-disk-settings"></a>Standaardbeheerde schijfinstellingen

Als u een VM met beheerde schijven wilt maken, hoeft u de bron voor het opslagaccount niet langer te maken. Als u verwijst naar het onderstaande voorbeeld van de sjabloon, zijn er enkele verschillen met de vorige voorbeelden van onmangede schijven om op te merken:

- Het `apiVersion` is een versie die beheerde schijven ondersteunt.
- `osDisk`en `dataDisks` niet langer verwijzen naar een specifieke URI voor de VHD.
- Bij het implementeren zonder extra eigenschappen op te geven, gebruikt de schijf een opslagtype op basis van de grootte van de VM. Als u bijvoorbeeld een VM-formaat gebruikt dat premiumopslag ondersteunt (formaten met 's' in hun naam, zoals Standard_D2s_v3), worden standaard premiumschijven geconfigureerd. U dit wijzigen door de sku-instelling van de schijf te gebruiken om een opslagtype op te geven.
- Als er geen naam voor de schijf `<VMName>_OsDisk_1_<randomstring>` is opgegeven, `<VMName>_disk<#>_<randomstring>` duurt het formaat voor de OS-schijf en voor elke gegevensschijf.
  - Als een vm wordt gemaakt op basis van een aangepaste afbeelding, worden de standaardinstellingen voor het type opslagaccount en de schijfnaam opgehaald uit de schijfeigenschappen die zijn gedefinieerd in de aangepaste afbeeldingsbron. Deze kunnen worden overschreven door waarden hiervoor op te geven in de sjabloon.
- Azure-schijfversleuteling is standaard uitgeschakeld.
- Standaard is schijfcache lezen/schrijven voor de OS-schijf en Geen voor gegevensschijven.
- In het onderstaande voorbeeld is er nog steeds een afhankelijkheid van opslagaccount, hoewel dit alleen is voor de opslag van diagnostiek en niet nodig is voor schijfopslag.

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="using-a-top-level-managed-disk-resource"></a>Een beheerde schijfbron op het hoogste niveau gebruiken

Als alternatief voor het opgeven van de schijfconfiguratie in het virtuele machineobject, u een schijfbron op het hoogste niveau maken en deze koppelen als onderdeel van het maken van de virtuele machine. U bijvoorbeeld een schijfbron als volgt maken om als gegevensschijf te gebruiken.

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2018-06-01",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

Verwijs in het VM-object naar het schijfobject dat moet worden gekoppeld. Als u de resource-id opgeeft `managedDisk` van de beheerde schijf die in de eigenschap is gemaakt, kan de bevestiging van de schijf worden gemaakt terwijl de vm wordt gemaakt. De `apiVersion` voor de VM-bron is ingesteld op `2017-03-30`. Er wordt een afhankelijkheid van de schijfbron toegevoegd om ervoor te zorgen dat deze is gemaakt voordat de VM wordt gemaakt. 

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2018-10-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Beheerde beschikbaarheidssets maken met VM's met beheerde schijven

Als u beheerde beschikbaarheidssets met VM's wilt maken met beheerde schijven, voegt u het `sku` object toe aan de bron voor beschikbaarheidsset en stelt u de `name` eigenschap in op `Aligned`. Deze eigenschap zorgt ervoor dat de schijven voor elke VM voldoende van elkaar zijn geïsoleerd om enkele storingspunten te voorkomen. Houd er `apiVersion` ook rekening mee dat de `2018-10-01`bron voor de beschikbaarheidsset is ingesteld op .

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2018-10-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 3,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

### <a name="standard-ssd-disks"></a>Standaard SSD-schijven

Hieronder staan de parameters die nodig zijn in de sjabloon Resourcemanager om standaard SSD-schijven te maken:

* *apiVersion* voor Microsoft.Compute moet `2018-04-01` worden ingesteld als (of later)
* *ManagedDisk.storageAccountType* opgeven als`StandardSSD_LRS`

In het volgende voorbeeld wordt de sectie *properties.storageProfile.osDisk* voor een VM weergegeven die standaard SSD-schijven gebruikt:

```json
"osDisk": {
    "osType": "Windows",
    "name": "myOsDisk",
    "caching": "ReadWrite",
    "createOption": "FromImage",
    "managedDisk": {
        "storageAccountType": "StandardSSD_LRS"
    }
}
```

Zie [Een vm maken op basis van een Windows-afbeelding met standaard SSD-gegevensschijven](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)voor een volledig voorbeeld van het maken van een standaard SSD-schijf met een sjabloon.

### <a name="additional-scenarios-and-customizations"></a>Aanvullende scenario's en aanpassingen

Als u alle informatie wilt vinden over de REST API-specificaties, raadpleegt u de [BEHEERDE REST API-documentatie voor de schijf.](/rest/api/manageddisks/disks/disks-create-or-update) U vindt er aanvullende scenario's, evenals standaard- en acceptabele waarden die via sjabloonimplementaties naar de API kunnen worden verzonden. 

## <a name="next-steps"></a>Volgende stappen

* Ga voor volledige sjablonen die beheerde schijven gebruiken naar de volgende Azure Quickstart Repo-koppelingen.
    * [Windows VM met beheerde schijf](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux VM met beheerde schijf](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* Ga naar het document [Overzicht van Azure Managed Disks](../articles/virtual-machines/windows/managed-disks-overview.md) voor meer informatie over beheerde schijven.
* Bekijk de sjabloonreferentiedocumentatie voor bronnen voor virtuele machines door naar het [referentiedocument voor microsoft.compute/virtualMachines-sjabloon te](/azure/templates/microsoft.compute/virtualmachines) gaan.
* Controleer de sjabloonreferentiedocumentatie voor schijfbronnen door naar het [sjabloonreferentiedocument Microsoft.Compute/disks te](/azure/templates/microsoft.compute/disks) gaan.
* Ga naar het document [Gegevensschijven gebruiken met schaalsets voor beheerde](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks) schijven in Azure- eenvoudigkoppelingssets.
