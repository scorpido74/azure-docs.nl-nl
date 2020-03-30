---
title: Schijven versleutelen voor Azure-schaalsets met Azure PowerShell
description: Meer informatie over het gebruik van Azure PowerShell om VM-exemplaren en gekoppelde schijven te versleutelen in een Windows-set voor virtuele machines
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mbaldwin
ms.openlocfilehash: bd7f92c104e06896f4b3c8bb2adef45983cf5d4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278987"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-azure-powershell"></a>Besturingssysteem en gekoppelde gegevensschijven versleutelen in een virtuele machineschaalset met Azure PowerShell

De Azure PowerShell-module wordt gebruikt voor het maken en beheren van Azure-resources vanaf de PowerShell-opdrachtregel of in scripts.  In dit artikel ziet u hoe u Azure PowerShell gebruiken om een schaalset voor virtuele machines te maken en te versleutelen. Zie [Azure Disk Encryption for Virtual Machine Scale Sets voor](disk-encryption-overview.md)meer informatie over het toepassen van Azure Disk Encryption op een virtuele machineschaalset.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Een Azure Key Vault maken die is ingeschakeld voor schijfversleuteling

Azure Key Vault kan sleutels, geheimen of wachtwoorden opslaan waarmee u ze veilig implementeren in uw toepassingen en services. Cryptografische sleutels worden opgeslagen in Azure Key Vault met behulp van softwarebeveiliging, of u uw sleutels importeren of genereren in Hardware Security Modules (HSM's) gecertificeerd volgens FIPS 140-2 niveau 2-standaarden. Deze cryptografische sleutels worden gebruikt om virtuele schijven die aan uw VM zijn gekoppeld, te versleutelen en te decoderen. U behoudt de controle over deze cryptografische sleutels en het gebruik ervan controleren.

Maak een key vault met [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault). Als u wilt dat de Sleutelkluis kan worden gebruikt voor schijfversleuteling, stelt u de parameter *EnabledForDiskEncryption* in. In het volgende voorbeeld worden ook variabelen gedefinieerd voor de naam van de resourcegroep, de naam van de sleutelkluis en de locatie. Geef uw eigen unieke Key Vault-naam op:

```azurepowershell-interactive
$rgName="myResourceGroup"
$vaultName="myuniquekeyvault"
$location = "EastUS"

New-AzResourceGroup -Name $rgName -Location $location
New-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName -Location $location -EnabledForDiskEncryption
```

### <a name="use-an-existing-key-vault"></a>Een bestaande key vault gebruiken

Deze stap is alleen vereist als u een bestaande Key Vault hebt die u wilt gebruiken met schijfversleuteling. Sla deze stap over als u in de vorige sectie een sleutelkluis hebt gemaakt.

U een bestaande Key Vault inschakelen in hetzelfde abonnement en dezelfde regio als de schaaldie is ingesteld voor schijfversleuteling met [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/Set-AzKeyVaultAccessPolicy). Definieer de naam van uw bestaande Sleutelkluis in de *$vaultName* variabele als volgt:


```azurepowershell-interactive
$vaultName="myexistingkeyvault"
Set-AzKeyVaultAccessPolicy -VaultName $vaultName -EnabledForDiskEncryption
```

## <a name="create-a-scale-set"></a>Een schaalset maken

Stel eerst een beheerdersnaam en -wachtwoord in voor de VM-exemplaren met behulp van [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Maak nu een virtuele-machineschaalset met behulp van [New-AzVmss](/powershell/module/az.compute/new-azvmss). Om het verkeer te distribueren naar de verschillende VM-exemplaren, wordt er ook een load balancer gemaakt. De load balancer bevat regels voor het distribueren van verkeer op TCP-poort 80, en voor het toestaan van extern bureaubladverkeer op TCP-poort 3389 en externe toegang via PowerShell op TCP-poort 5985:

```azurepowershell-interactive
$vmssName="myScaleSet"

New-AzVmss `
    -ResourceGroupName $rgName `
    -VMScaleSetName $vmssName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -PublicIpAddressName "myPublicIPAddress" `
    -LoadBalancerName "myLoadBalancer" `
    -UpgradePolicy "Automatic" `
    -Credential $cred
```

## <a name="enable-encryption"></a>Versleuteling inschakelen

Als u VM-exemplaren in een schaalset wilt versleutelen, krijgt u eerst informatie over de Key Vault URI en de resource-ID met [Get-AzKeyVault.](/powershell/module/az.keyvault/Get-AzKeyVault) Deze variabelen worden gebruikt om vervolgens het versleutelingsproces te starten met [Set-AzVmssDiskEncryptionExtension:](/powershell/module/az.compute/Set-AzVmssDiskEncryptionExtension)


```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

Wanneer u wordt gevraagd, typt u *y* om het schijfversleutelingsproces voort te zetten op de vm-exemplaren van de schaalset.

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Versleuteling inschakelen met KEK om de sleutel te verpakken

U ook een sleutelversleutelingssleutel gebruiken voor extra beveiliging bij het versleutelen van de schaalset van virtuele machines.

```azurepowershell-interactive
$diskEncryptionKeyVaultUrl=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).VaultUri
$keyVaultResourceId=(Get-AzKeyVault -ResourceGroupName $rgName -Name $vaultName).ResourceId
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $vaultName -Name $keyEncryptionKeyName).Key.kid;

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $rgName -VMScaleSetName $vmssName `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $keyVaultResourceId -VolumeType "All"
```

> [!NOTE]
>  De syntaxis voor de waarde van de parameter schijfversleuteling-keyvault is de volledige id-tekenreeks:</br>
/abonnementen/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]</br></br>
> De syntaxis voor de waarde van de parameter key-encryption-key is de volledige URI naar de KEK zoals in:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Versleutelingsvoortgang controleren

Als u de status van schijfversleuteling wilt controleren, gebruikt u [Get-AzVmssDiskEncryption:](/powershell/module/az.compute/Get-AzVmssDiskEncryption)


```azurepowershell-interactive
Get-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

Wanneer VM-exemplaren worden versleuteld, rapporteert de *code EncryptionSummary* *ProvisioningState/succeeded* zoals weergegeven in de volgende voorbeelduitvoer:

```powershell
ResourceGroupName            : myResourceGroup
VmScaleSetName               : myScaleSet
EncryptionSettings           :
  KeyVaultURL                : https://myuniquekeyvault.vault.azure.net/
  KeyEncryptionKeyURL        :
  KeyVaultResourceId         : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/myuniquekeyvault
  KekVaultResourceId         :
  KeyEncryptionAlgorithm     :
  VolumeType                 : All
  EncryptionOperation        : EnableEncryption
EncryptionSummary[0]         :
  Code                       : ProvisioningState/succeeded
  Count                      : 2
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="disable-encryption"></a>Versleuteling uitschakelen

Als u geen versleutelde schijven van VM-exemplaren meer wilt gebruiken, u de versleuteling met [Disable-AzVmssDiskEncryption](/powershell/module/az.compute/Disable-AzVmssDiskEncryption) als volgt uitschakelen:


```azurepowershell-interactive
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $vmssName
```

## <a name="next-steps"></a>Volgende stappen

- In dit artikel hebt u Azure PowerShell gebruikt om een schaalset voor virtuele machines te versleutelen. U ook de azure [CLI-](disk-encryption-cli.md) of [Azure Resource Manager-sjablonen](disk-encryption-azure-resource-manager.md)gebruiken.
- Als u Azure Disk Encryption wilt laten toepassen nadat een andere extensie is ingericht, u [extensiesequencing](virtual-machine-scale-sets-extension-sequencing.md)gebruiken.
