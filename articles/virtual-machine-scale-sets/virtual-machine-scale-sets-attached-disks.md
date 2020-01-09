---
title: Gegevens schijven die zijn gekoppeld aan Azure Virtual Machine Scale Sets
description: Meer informatie over het gebruik van gekoppelde gegevens schijven met schaal sets voor virtuele machines via overzichten van specifieke use cases.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 76ac7fd7-2e05-4762-88ca-3b499e87906e
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 4/25/2017
ms.author: manayar
ms.openlocfilehash: 6b6c752c1b86061f43af912405e610d466f17e37
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465003"
---
# <a name="azure-virtual-machine-scale-sets-and-attached-data-disks"></a>Virtuele-machineschaalsets in Azure en gekoppelde gegevensschijven
Voor het uitbreiden van uw beschikbare opslag bieden [virtuele-machineschaalsets](/azure/virtual-machine-scale-sets/) in Azure ondersteuning voor VM-instanties met gekoppelde gegevensschijven. U kunt gegevensschijven koppelen wanneer de schaalset wordt gemaakt of aan een bestaande schaalset.

> [!NOTE]
> Wanneer u een schaalset maakt met gekoppelde gegevensschijven, dient u de schijven nog steeds te koppelen en formatteren van binnen een VM om ze te gebruiken (net als bij zelfstandige virtuele Azure-machines). Een handige manier om dit proces te voltooien, is om een aangepaste scriptextensie te gebruiken die een script oproept om alle gegevensschijven op de VM te partitioneren en formatteren. Zie [Azure CLI](tutorial-use-disks-cli.md#prepare-the-data-disks) [Azure PowerShell](tutorial-use-disks-powershell.md#prepare-the-data-disks)voor voor beelden hiervan.


## <a name="create-and-manage-disks-in-a-scale-set"></a>Schijven in een schaalset maken en beheren
Zie een van de volgende zelfstudies voor gedetailleerde informatie over het maken van een schaalset met gekoppelde gegevensschijven, het voorbereiden en formatteren of toevoegen en verwijderen van gegevensschijven:

- [Azure-CLI](tutorial-use-disks-cli.md)
- [Azure PowerShell](tutorial-use-disks-powershell.md)

De rest van dit artikel bevat een overzicht van de specifieke gebruiksvoorbeelden zoals Service Fabric-clusters die gegevensschijven vereisen of het koppelen van bestaande gegevensschijven met inhoud aan een schaalset.


## <a name="create-a-service-fabric-cluster-with-attached-data-disks"></a>Een Service Fabric-cluster maken met gekoppelde gegevensschijven
Elk [knooppunttype](../service-fabric/service-fabric-cluster-nodetypes.md) in een [Service Fabric](/azure/service-fabric)-cluster dat wordt uitgevoerd in Azure, wordt ondersteund door een virtuele-machineschaalset. U kunt met behulp van een Azure Resource Manager-sjabloon gegevensschijven koppelen aan de schaalsets die gezamenlijk het Service Fabric-cluster vormen. U kunt een [bestaande sjabloon](https://github.com/Azure-Samples/service-fabric-cluster-templates) als uitgangspunt nemen. Neem in de sjabloon een sectie _dataDisks_ op in het _storageProfile_ van de _Microsoft.Compute/virtualMachineScaleSets_-resource(s) en implementeer de sjabloon. In het volgende voorbeeld wordt een gegevensschijf van 128 GB gekoppeld:

```json
"dataDisks": [
    {
    "diskSizeGB": 128,
    "lun": 0,
    "createOption": "Empty"
    }
]
```

U kunt automatisch partitioneren, formatteren en de gegevensschijven koppelen wanneer het cluster is geïmplementeerd. Voeg een extensie voor aangepaste scripts toe aan het _extensionProfile_ van de _virtualMachineProfile_ van de schaalset(s).

Als u de gegevensschijven automatisch wilt voorbereiden in een Windows-cluster, voegt u het volgende toe:

```json
{
    "name": "customScript",
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.8",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.ps1"
        ],
        "commandToExecute": "powershell -ExecutionPolicy Unrestricted -File prepare_vm_disks.ps1"
        }
    }
}
```
Als u de gegevensschijven automatisch wilt voorbereiden in een Linux-cluster, voegt u het volgende toe:
```json
{
    "name": "lapextension",
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
        "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"
        ],
        "commandToExecute": "bash prepare_vm_disks.sh"
        }
    }
}
```


## <a name="adding-pre-populated-data-disks-to-an-existing-scale-set"></a>Vooraf gevulde gegevensschijven toevoegen aan een bestaande schaalset
Gegevensschijven die zijn opgegeven in het schaalsetmodel, zijn altijd leeg. U kunt echter een bestaande gegevensschijf koppelen aan een specifieke virtuele machine in een schaalset. Deze functie is in de preview-versie. Voorbeelden vindt u op [GitHub](https://github.com/Azure/vm-scale-sets/tree/master/preview/disk). Als u gegevens wilt doorvoeren naar alle virtuele machines in de schaalset, kunt u de gegevensschijf dupliceren en deze koppelen aan elke virtuele machine in de schaalset. U kunt ook een aangepaste installatiekopie maken die de gegevens bevat en de schaalset inrichten vanaf deze aangepaste installatiekopie. Of u kunt Azure Files of een vergelijkbaar product voor gegevensopslag gebruiken.


## <a name="additional-notes"></a>Aanvullende opmerkingen
Er is ondersteuning voor Azure Managed Disks en aan schaalsets gekoppelde gegevensschijven beschikbaar in API [_2016-04-30-preview_](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/compute/resource-manager/Microsoft.Compute/preview/2016-04-30-preview/compute.json) of hoger van de Microsoft.Compute API.

De ondersteuning in Azure Portal voor gekoppelde gegevensschijven in schaalsets is oorspronkelijk beperkt. Afhankelijk van uw vereisten kunt u Azure-sjablonen, CLI, PowerShell, SDK's en REST API gebruiken voor het beheren van gekoppelde schijven.


