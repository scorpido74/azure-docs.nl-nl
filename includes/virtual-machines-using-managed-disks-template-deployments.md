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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76037945"
---
In dit document wordt uitgelegd wat de verschillen zijn tussen beheerde en onbeheerde schijven bij het gebruik van Azure Resource Manager sjablonen voor het inrichten van virtuele machines. De voor beelden helpen u bij het bijwerken van bestaande sjablonen die gebruikmaken van niet-beheerde schijven naar beheerde schijven. Ter referentie gebruiken we de sjabloon [101-VM-Simple-Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) als richt lijn. U kunt de sjabloon weer geven met behulp van zowel [beheerde schijven](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows/azuredeploy.json) als een eerdere versie met behulp van [onbeheerde schijven](https://github.com/Azure/azure-quickstart-templates/tree/93b5f72a9857ea9ea43e87d2373bf1b4f724c6aa/101-vm-simple-windows/azuredeploy.json) als u deze direct wilt vergelijken.

## <a name="unmanaged-disks-template-formatting"></a>Niet-beheerde schijven sjabloon opmaak

We gaan nu kijken hoe onbeheerde schijven worden geïmplementeerd. Bij het maken van niet-beheerde schijven hebt u een opslag account nodig voor het opslaan van de VHD-bestanden. U kunt een nieuw opslag account maken of er een gebruiken dat al bestaat. In dit artikel wordt beschreven hoe u een nieuw opslag account maakt. Maak een opslag account resource in het blok resources, zoals hieronder wordt weer gegeven.

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

Voeg binnen het virtuele-machine object een afhankelijkheid toe aan het opslag account om ervoor te zorgen dat deze wordt gemaakt vóór de virtuele machine. Geef in `storageProfile` de sectie de volledige URI op van de VHD-locatie, die verwijst naar het opslag account en is vereist voor de besturingssysteem schijf en alle gegevens schijven.

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

## <a name="managed-disks-template-formatting"></a>Sjabloon opmaak voor Managed disks

Met Azure Managed Disks wordt de schijf een resource op het hoogste niveau en hoeft er geen opslag account meer te worden gemaakt door de gebruiker. Managed disks zijn voor het `2016-04-30-preview` eerst beschikbaar gemaakt in de API-versie en zijn nu ook het standaard schijf type. In de volgende secties worden de standaard instellingen en details beschreven voor het aanpassen van uw schijven.

> [!NOTE]
> Het is raadzaam om een API-versie later te `2016-04-30-preview` gebruiken dan wanneer er sprake `2016-04-30-preview` is `2017-03-30`van een laatste wijziging tussen en.
>
>

### <a name="default-managed-disk-settings"></a>Standaard instellingen voor beheerde schijven

Als u een virtuele machine met Managed disks wilt maken, hoeft u de bron van het opslag account niet meer te maken. Als u verwijst naar het volgende voor beeld van een sjabloon, zijn er enkele verschillen ten opzichte van de voor beelden van de vorige unmanged-schijf:

- De `apiVersion` is een versie die beheerde schijven ondersteunt.
- `osDisk`en `dataDisks` verwijzen niet meer naar een specifieke URI voor de VHD.
- Wanneer u implementeert zonder extra eigenschappen op te geven, gebruikt de schijf een opslag type op basis van de grootte van de virtuele machine. Als u bijvoorbeeld een VM-grootte gebruikt die Premium-opslag (grootten met ' s ' in hun naam ondersteunt, zoals Standard_D2s_v3), worden standaard Premium-schijven geconfigureerd. U kunt dit wijzigen door de SKU-instelling van de schijf te gebruiken om een opslag type op te geven.
- Als er geen naam voor de schijf is opgegeven, wordt de indeling van `<VMName>_OsDisk_1_<randomstring>` de besturingssysteem schijf en `<VMName>_disk<#>_<randomstring>` voor elke gegevens schijf gebruikt.
  - Als er een virtuele machine wordt gemaakt op basis van een aangepaste installatie kopie, worden de standaard instellingen voor het type opslag account en de schijf naam opgehaald uit de schijf eigenschappen die zijn gedefinieerd in de aangepaste installatie kopie bron. Deze kunnen worden overschreven door waarden voor deze in de sjabloon op te geven.
- Azure Disk Encryption is standaard uitgeschakeld.
- Schijf cache is standaard lezen/schrijven voor de besturingssysteem schijf en geen voor gegevens schijven.
- In het voor beeld hieronder bevindt zich nog steeds een opslag account afhankelijkheid, hoewel dit alleen geldt voor opslag van diagnostische gegevens en niet nodig is voor schijf opslag.

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

### <a name="using-a-top-level-managed-disk-resource"></a>Een beheerde schijf resource op het hoogste niveau gebruiken

Als alternatief voor het opgeven van de schijf configuratie in het virtuele-machine object, kunt u een schijf bron op het hoogste niveau maken en deze koppelen als onderdeel van het maken van de virtuele machine. U kunt bijvoorbeeld als volgt een schijf bron maken om te gebruiken als een gegevens schijf.

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

In het VM-object verwijst u naar het schijf object dat moet worden gekoppeld. Het opgeven van de bron-ID van de beheerde schijf `managedDisk` die is gemaakt in de eigenschap, staat de bijlage van de schijf toe als de virtuele machine wordt gemaakt. De `apiVersion` voor de VM-resource is ingesteld `2017-03-30`op. Er wordt een afhankelijkheid van de schijf resource toegevoegd om ervoor te zorgen dat deze is gemaakt voordat de VM wordt gemaakt. 

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

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Beheerde beschikbaarheids sets maken met Vm's met behulp van beheerde schijven

Als u beheerde beschikbaarheids sets wilt maken met virtuele machines met behulp `sku` van beheerde schijven, voegt u het object toe `name` aan de `Aligned`resource van de beschikbaarheidsset en stelt u de eigenschap in op. Met deze eigenschap zorgt u ervoor dat de schijven voor elke VM voldoende van elkaar zijn geïsoleerd om afzonderlijke storings punten te voor komen. Houd er ook rekening `apiVersion` mee dat de voor de resource van de `2018-10-01`beschikbaarheidsset is ingesteld op.

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

### <a name="standard-ssd-disks"></a>Standard-SSD schijven

Hieronder vindt u de para meters die nodig zijn in het Resource Manager-sjabloon om Standard-SSD schijven te maken:

* *apiVersion* voor micro soft. Compute moet worden `2018-04-01` ingesteld als (of hoger)
* Geef *managedDisk. storageAccountType* op als`StandardSSD_LRS`

In het volgende voor beeld ziet u de sectie *Properties. storageProfile. osDisk* voor een virtuele machine die gebruikmaakt van Standard-SSD schijven:

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

Zie [een virtuele machine maken op basis van een Windows-installatie kopie met Standard-SSD-gegevens schijven](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/)voor een volledig sjabloon voor beeld van het maken van een Standard-SSD schijf met een sjabloon.

### <a name="additional-scenarios-and-customizations"></a>Aanvullende scenario's en aanpassingen

Raadpleeg de documentatie voor het [maken van een beheerde schijf rest API](/rest/api/manageddisks/disks/disks-create-or-update)voor volledige informatie over de rest API specificaties. Er vindt u aanvullende scenario's, evenals standaard en acceptabele waarden die kunnen worden verzonden naar de API via sjabloon implementaties. 

## <a name="next-steps"></a>Volgende stappen

* Ga naar de volgende opslag plaats-koppelingen voor Azure Quick start voor volledige sjablonen die gebruikmaken van beheerde schijven.
    * [Windows-VM met beheerde schijf](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux-VM met beheerde schijf](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
* Ga naar het document [overzicht van Azure Managed disks](../articles/virtual-machines/windows/managed-disks-overview.md) voor meer informatie over Managed disks.
* Raadpleeg de documentatie over de sjabloon voor virtuele machines voor meer informatie. Ga naar het document [micro soft. Compute/informatie voor sjablonen](/azure/templates/microsoft.compute/virtualmachines) .
* Raadpleeg de documentatie over sjablonen voor schijf bronnen door naar het document [micro soft. Compute/disks-sjabloon](/azure/templates/microsoft.compute/disks) te gaan.
* Ga voor meer informatie over het gebruik van beheerde schijven in virtuele-machine schaal sets van Azure naar het document [gegevens schijven met schaal sets gebruiken](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-attached-disks) .
