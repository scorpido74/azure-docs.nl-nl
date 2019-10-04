---
title: Een virtuele Linux-machine maken en versleutelen met Azure Power shell
description: In deze Quick Start leert u hoe u Azure Power shell kunt gebruiken om een virtuele Linux-machine te maken en te versleutelen
author: msmbaldwin
ms.author: mbaldwin
ms.service: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 14cdd1e15212141e8c631b589c9c470bbdfe38a9
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829905"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-in-azure-with-azure-powershell"></a>Quickstart: Een virtuele Linux-machine in azure maken en versleutelen met Azure PowerShell

De Azure PowerShell-module wordt gebruikt voor het maken en beheren van Azure-resources vanaf de PowerShell-opdrachtregel of in scripts. In deze Quick start ziet u hoe u de Azure PowerShell module kunt gebruiken om een virtuele Linux-machine (VM) te maken, een Key Vault te maken voor de opslag van versleutelings sleutels en de virtuele machine te versleutelen. Deze Snelstartgids maakt gebruik van de Ubuntu 16,04 LTS Marketplace-installatie kopie van canonieke en een VM Standard_D2S_V3-grootte. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd:

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een virtuele machine van Azure met [New-AzVM](/powershell/module/az.compute/new-azvm), waarmee het VM-configuratie object dat u hierboven hebt gemaakt, wordt door gegeven.

```powershell-interactive
$cred = = Get-Credential

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image Canonical:UbuntuServer:16.04-LTS:latest -Size Standard_D2S_V3
```

Het duurt een paar minuten voor uw virtuele machine is geïmplementeerd. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Een Key Vault maken die is geconfigureerd voor versleutelings sleutels

De versleutelings sleutel van Azure Disk Encryption wordt opgeslagen in een Azure Key Vault. Maak een Key Vault met [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Gebruik de para meter-EnabledForDiskEncryption om de Key Vault in te scha kelen om versleutelings sleutels op te slaan.

> [!Important]
> Elke sleutel kluis moet een naam hebben die uniek is binnen Azure. Vervang in de onderstaande voor beelden < uw-sleutel kluis naam > door de naam die u kiest.

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>De virtuele machine versleutelen

Versleutel uw virtuele machine met [set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension vereist sommige waarden van uw Key Vault-object. U kunt deze waarden verkrijgen door de unieke naam van uw sleutel kluis door te geven aan [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```azurepowershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -SkipVmBackup -VolumeType All
```

Na een paar minuten wordt het volgende door het proces geretourneerd:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

U kunt het versleutelings proces controleren door [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus)uit te voeren.

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Wanneer versleuteling is ingeschakeld, wordt het volgende weer gegeven in de geretourneerde uitvoer:

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

In deze Quick Start hebt u een virtuele machine gemaakt, een Key Vault gemaakt die is ingeschakeld voor versleutelings sleutels en de VM versleuteld.  Ga naar het volgende artikel voor meer informatie over Azure Disk Encryption voor Linux-Vm's.

> [!div class="nextstepaction"]
> [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)