---
title: Schijven versleutelen op een Windows-VM in azure | Microsoft Docs
description: Virtuele schijven op een Windows-VM versleutelen voor een betere beveiliging met behulp van Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/30/2018
ms.author: cynthn
ms.openlocfilehash: 6a92ee6fe53b1676c493c54510dd0f6c4b4b5dc9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079574"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Virtuele schijven op een Windows-VM versleutelen
Virtuele schijven in azure kunnen worden versleuteld voor verbeterde beveiliging en naleving van de virtuele machine (VM). Schijven worden versleuteld met behulp van cryptografische sleutels die zijn beveiligd in een Azure Key Vault. U kunt deze cryptografische sleutels beheren en het gebruik ervan controleren. In dit artikel wordt beschreven hoe u virtuele schijven op een Windows-VM versleutelt met behulp van Azure PowerShell. U kunt ook [een virtuele Linux-machine versleutelen met behulp van de Azure cli](../linux/encrypt-disks.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="overview-of-disk-encryption"></a>Overzicht van schijf versleuteling
Virtuele schijven op Windows-Vm's worden op rest versleuteld met behulp van BitLocker. Er worden geen kosten in rekening gebracht voor het versleutelen van virtuele schijven in Azure. Cryptografische sleutels worden opgeslagen in een Azure Key Vault met behulp van software beveiliging, of u kunt uw sleutels in Hardware Security modules (Hsm's) die zijn gecertificeerd voor FIPS 140-2 level 2-standaarden, importeren of genereren. Cryptografische sleutels worden gebruikt voor het versleutelen en ontsleutelen van virtuele schijven die zijn gekoppeld aan uw VM. U kunt de controle over deze cryptografische sleutels houden en het gebruik ervan controleren. 

Het proces voor het versleutelen van een virtuele machine is als volgt:

1. Een cryptografische sleutel maken in een Azure Key Vault.
1. Configureer de cryptografische sleutel voor het versleutelen van schijven.
1. Schakel schijf versleuteling in voor uw virtuele schijven.
1. De vereiste cryptografische sleutels zijn aangevraagd bij Azure Key Vault.
1. De virtuele schijven worden versleuteld met behulp van de beschik bare cryptografische sleutel.


## <a name="requirements-and-limitations"></a>Vereisten en beperkingen

Ondersteunde scenario's en vereisten voor schijf versleuteling:

* Versleuteling inschakelen voor nieuwe Windows-Vm's vanuit Azure Marketplace-installatie kopieën of aangepaste VHD-installatie kopieën.
* Versleuteling inschakelen op bestaande Windows-Vm's in Azure.
* Het inschakelen van versleuteling op Windows-Vm's die zijn geconfigureerd met behulp van opslag ruimten.
* Versleuteling uitschakelen voor besturings systeem en gegevens stations voor Windows-Vm's.
* Virtuele machines uit de Standard-laag, zoals een, D, DS, G en GS-serie.

    > [!NOTE]
    > Alle resources (met inbegrip van de Key Vault, het opslag account en de virtuele machine) moeten zich in dezelfde Azure-regio en hetzelfde abonnement bevinden.

Schijf versleuteling wordt momenteel niet ondersteund in de volgende scenario's:

* Virtuele machines van de Basic-laag.
* Vm's gemaakt met behulp van het klassieke implementatie model.
* De cryptografische sleutels worden bijgewerkt op een al versleutelde virtuele machine.
* Integratie met on-premises Key Management-service.


## <a name="create-an-azure-key-vault-and-keys"></a>Een Azure Key Vault en sleutels maken
Controleer voordat u begint of de meest recente versie van de module Azure PowerShell is geïnstalleerd. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor meer informatie. Vervang in de volgende opdracht voorbeelden alle voorbeeld parameters door uw eigen namen, locatie en sleutel waarden, zoals *myResourceGroup*, *myKeyVault*, *myVM*enzovoort.

De eerste stap bestaat uit het maken van een Azure Key Vault voor het opslaan van uw cryptografische sleutels. Met Azure Key kluizen kunnen sleutels, geheimen of wacht woorden worden opgeslagen waarmee u deze veilig kunt implementeren in uw toepassingen en services. Voor versleuteling van de virtuele schijf maakt u een Key Vault voor het opslaan van een cryptografische sleutel die wordt gebruikt voor het versleutelen of ontsleutelen van uw virtuele schijven. 

Schakel de Azure Key Vault provider in uw Azure-abonnement in met [REGI ster-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)en maak een resource groep met [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). In het volgende voor beeld wordt de naam van een resource groep *myResourceGroup* op de locatie *VS-Oost* :

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

Het Azure Key Vault het houden van de cryptografische sleutels en gekoppelde reken resources zoals opslag en de virtuele machine zelf moeten zich in dezelfde regio bevinden. Maak een Azure Key Vault met [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) en schakel de Key Vault in voor gebruik met schijf versleuteling. Geef als volgt een unieke Key Vault naam op voor de sleutel *kluis* :

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

U kunt cryptografische sleutels opslaan met behulp van de software of HSM-beveiliging (Hardware Security model).  Met een standaard Key Vault worden alleen door software beschermde sleutels opgeslagen. Voor het gebruik van een HSM is extra kosten vereist voor een Premium-Key Vault. Als u een Premium-Key Vault wilt maken, voegt u in de vorige stap de para meter *-SKU ' Premium '* toe. In het volgende voor beeld worden door software beschermde sleutels gebruikt sinds er een standaard Key Vault is gemaakt. 

Voor beide beveiligings modellen moet aan het Azure-platform toegang worden verleend om de cryptografische sleutels aan te vragen wanneer de virtuele schijven worden ontsleuteld door de VM. Maak een cryptografische sleutel in uw Key Vault met [add-AzureKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey). In het volgende voor beeld wordt een sleutel met de naam *MyKey*gemaakt:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken
Als u het versleutelings proces wilt testen, maakt u een virtuele machine met [New-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). In het volgende voor beeld wordt een VM gemaakt met de naam *myVM* met behulp van een *Windows Server 2016 Data Center* -installatie kopie. Wanneer u om referenties wordt gevraagd, voert u de gebruikers naam en het wacht woord in die voor uw virtuele machine moeten worden gebruikt:

```azurepowershell-interactive
$cred = Get-Credential

New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```


## <a name="encrypt-a-virtual-machine"></a>Een virtuele machine versleutelen
Versleutel uw virtuele machine met [set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) met behulp van de sleutel Azure Key Vault. In het volgende voor beeld worden alle sleutel gegevens opgehaald en wordt vervolgens de virtuele machine met de naam *myVM*versleuteld:

```azurepowershell-interactive
$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName "myVM" `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

Accepteer de prompt om door te gaan met de VM-versleuteling. De virtuele machine wordt opnieuw opgestart tijdens het proces. Nadat het versleutelings proces is voltooid en de VM opnieuw is opgestart, controleert u de versleutelings status met [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus):

```azurepowershell-interactive
Get-AzVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName "myVM"
```

De uitvoer lijkt op die in het volgende voorbeeld:

```azurepowershell-interactive
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>Volgende stappen
* Zie [een Key Vault instellen voor virtuele machines](key-vault-setup.md)voor meer informatie over het beheren van een Azure Key Vault.
* Zie [Azure Disk Encryption](../../security/azure-security-disk-encryption.md)voor meer informatie over schijf versleuteling, zoals het voorbereiden van een versleutelde aangepaste VM voor het uploaden naar Azure.
