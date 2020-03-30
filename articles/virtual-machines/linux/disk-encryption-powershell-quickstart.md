---
title: Een Linux-vm maken en versleutelen met Azure Powershell
description: In deze quickstart leert u hoe u Azure Powershell gebruiken om een Virtuele Linux-machine te maken en te versleutelen
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: a2cb8919ac0752c42f22e064d6201c7120fbc9b6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78970539"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-in-azure-with-azure-powershell"></a>Snelstart: een Linux-vm maken en versleutelen in Azure met Azure PowerShell

De Azure PowerShell-module wordt gebruikt voor het maken en beheren van Azure-resources vanaf de PowerShell-opdrachtregel of in scripts. Met deze quickstart u zien hoe u de Azure PowerShell-module gebruiken om een Virtuele Linux-machine (VM) te maken, een Key Vault te maken voor de opslag van versleutelingssleutels en de VM te versleutelen. Deze quickstart maakt gebruik van de Ubuntu 16.04 LTS marketplace afbeelding van Canonical en een VM Standard_D2S_V3 formaat. 

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een Azure virtuele machine met [Nieuw-AzVM,](/powershell/module/az.compute/new-azvm)waarbij het VM-configuratieobject dat u hierboven hebt gemaakt, wordt doorgespeeld.

```powershell-interactive
$cred = = Get-Credential

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:16.04-LTS:latest -Size Standard_D2S_V3
```

Het duurt een paar minuten voor uw virtuele machine is geïmplementeerd. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Een sleutelkluis maken die is geconfigureerd voor versleutelingssleutels

Azure-schijfversleuteling slaat de versleutelingssleutel op in een Azure Key Vault. Maak een key vault met [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Als u de Sleutelkluis wilt inschakelen om versleutelingssleutels op te slaan, gebruikt u de parameter -EnabledForDiskEncryption.

> [!Important]
> Elke sleutelkluis moet een naam hebben die uniek is in Azure. Vervang in de onderstaande voorbeelden <uw-unieke-keyvault-naam> door de naam die u kiest.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>De virtuele machine versleutelen

Versleutel uw VM met [Set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension vereist enkele waarden van uw Key Vault-object. U deze waarden verkrijgen door de unieke naam van uw sleutelkluis door te geven aan [Get-AzKeyvault.](/powershell/module/az.keyvault/get-azkeyvault)

```azurepowershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

Na enkele minuten geeft het proces het volgende terug:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

U het versleutelingsproces verifiëren door [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus)uit te voeren.

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Wanneer versleuteling is ingeschakeld, ziet u het volgende in de geretourneerde uitvoer:

```
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een virtuele machine gemaakt, een key vault gemaakt die is ingeschakeld voor versleutelingssleutels en de VM versleuteld.  Ga naar het volgende artikel voor meer informatie over Azure Disk Encryption voor Linux VM's.

> [!div class="nextstepaction"]
> [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)