---
title: Azure PowerShell-end-to-end-versleuteling op uw VM-host inschakelen
description: End-to-end-versleuteling voor uw virtuele Azure-machines inschakelen met versleuteling op de host.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 6a352ecc2d2b02f03e2b55f7c5896ac905077921
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814789"
---
# <a name="use-the-azure-powershell-module-to-enable-end-to-end-encryption-using-encryption-at-host"></a>De module Azure PowerShell gebruiken om end-to-end-versleuteling in te scha kelen met behulp van versleuteling op de host

Wanneer u versleuteling inschakelt op de host, worden gegevens die op de VM-host zijn opgeslagen, versleuteld op rest en stromen die zijn versleuteld naar de opslag service. Zie [versleuteling bij host-end-to-end-versleuteling voor uw VM-gegevens](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)voor conceptuele informatie over versleuteling op de host, evenals andere beheerde schijf versleutelings typen.

## <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>Ondersteunde regio’s

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>Ondersteunde VM-grootten

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

Mogelijk vindt u ook de VM-grootten via een programma. Zie de sectie [ondersteunde VM-grootten zoeken](#finding-supported-vm-sizes) voor meer informatie over het ophalen van deze Program ma's.

## <a name="prerequisites"></a>Vereisten

Als u versleuteling wilt gebruiken op de host voor uw Vm's of virtuele-machine schaal sets, moet u de functie inschakelen voor uw abonnement. Stuur een e-mail naar encryptionAtHost@microsoft .com met uw abonnement-id's om de functie in te schakelen voor uw abonnementen.

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Een Azure Key Vault en DiskEncryptionSet maken

Zodra de functie is ingeschakeld, moet u een Azure Key Vault en een DiskEncryptionSet instellen, als u dat nog niet hebt gedaan.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

## <a name="enable-encryption-at-host-for-disks-attached-to-vm-and-virtual-machine-scale-sets"></a>Versleuteling inschakelen op de host voor schijven die zijn gekoppeld aan VM-en virtuele-machine schaal sets

U kunt versleuteling inschakelen op de host door een nieuwe eigenschap EncryptionAtHost onder securityProfile van Vm's of virtuele-machine schaal sets in te stellen met behulp van de API-versie **2020-06-01** en hoger.

`"securityProfile": { "encryptionAtHost": "true" }`

## <a name="example-scripts"></a>Voorbeeld scripts

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-customer-managed-keys"></a>Versleuteling op de host inschakelen voor schijven die zijn gekoppeld aan een virtuele machine met door de klant beheerde sleutels

Maak een virtuele machine met Managed disks met behulp van de resource-URI van de DiskEncryptionSet die u eerder hebt gemaakt.

Vervang,,,,, `<yourPassword>` `<yourVMName>` `<yourVMSize>` `<yourDESName>` `<yoursubscriptionID>` `<yourResourceGroupName>` en `<yourRegion>` Voer het script uit.

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithCMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-diskEncryptionSetId "/subscriptions/<yoursubscriptionID>/resourceGroups/<yourResourceGroupName>/providers/Microsoft.Compute/diskEncryptionSets/<yourDESName>" `
-region "<yourRegion>"
```

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-platform-managed-keys"></a>Versleuteling op de host inschakelen voor schijven die zijn gekoppeld aan een virtuele machine met door het platform beheerde sleutels

Vervang `<yourPassword>` ,,, `<yourVMName>` `<yourVMSize>` `<yourResourceGroupName>` en `<yourRegion>` Voer het script uit.

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithPMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-region "<yourRegion>"
```

## <a name="finding-supported-vm-sizes"></a>Ondersteunde VM-grootten zoeken

Verouderde VM-grootten worden niet ondersteund. U kunt de lijst met ondersteunde VM-grootten vinden door een van de volgende opties:

De [resource-sku's-API](/rest/api/compute/resourceskus/list) wordt aangeroepen en er wordt gecontroleerd of de `EncryptionAtHostSupported` mogelijkheid is ingesteld op **waar**.

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

U kunt ook de Power shell [-cmdlet Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) aanroepen.

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>Volgende stappen

Nu u deze resources hebt gemaakt en geconfigureerd, kunt u deze gebruiken om uw beheerde schijven te beveiligen. De volgende koppeling bevat voorbeeld scripts, elk met een eigen scenario, dat u kunt gebruiken om uw beheerde schijven te beveiligen.

[Azure Resource Manager-voorbeeldsjablonen](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
