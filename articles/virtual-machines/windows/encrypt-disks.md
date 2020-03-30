---
title: Schijven versleutelen op een Windows-vm in Azure
description: Virtuele schijven versleutelen op een Windows-vm voor verbeterde beveiliging met Azure PowerShell
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
ms.openlocfilehash: 096fe779c077424b01df3ead5965cb799866a03b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74033532"
---
# <a name="encrypt-virtual-disks-on-a-windows-vm"></a>Virtuele schijven versleutelen op een Windows-vm
Voor verbeterde vm-beveiliging en compliance (virtual machine) kunnen virtuele schijven in Azure worden versleuteld. Schijven worden versleuteld met behulp van cryptografische sleutels die zijn beveiligd in een Azure Key Vault. U beheert deze cryptografische sleutels en het gebruik ervan controleren. In dit artikel wordt beschreven hoe u virtuele schijven op een Windows-vm versleutelen met Azure PowerShell. U ook [linux virtuele machines versleutelen.](../linux/disk-encryption-overview.md)

 

## <a name="overview-of-disk-encryption"></a>Overzicht van schijfversleuteling
Virtuele schijven op Windows VM's worden in rust versleuteld met BitLocker. Er zijn geen kosten verbonden aan het versleutelen van virtuele schijven in Azure. Cryptografische sleutels worden opgeslagen in een Azure Key Vault met behulp van softwarebeveiliging, of u uw sleutels importeren of genereren in Hardware Security Modules (HSM's) gecertificeerd volgens FIPS 140-2 niveau 2-standaarden. Cryptografische sleutels worden gebruikt om virtuele schijven die aan uw VM zijn gekoppeld, te versleutelen en te decoderen. U houdt de controle over deze cryptografische sleutels en het gebruik ervan controleren. 

Het proces voor het versleutelen van een VM is als volgt:

1. Maak een cryptografische sleutel in een Azure Key Vault.
1. Configureer de cryptografische sleutel om bruikbaar te zijn voor het versleutelen van schijven.
1. Schijfversleuteling inschakelen voor uw virtuele schijven.
1. De vereiste cryptografische sleutels worden aangevraagd bij Azure Key Vault.
1. De virtuele schijven worden versleuteld met behulp van de meegeleverde cryptografische sleutel.


## <a name="requirements-and-limitations"></a>Vereisten en beperkingen

Ondersteunde scenario's en vereisten voor schijfversleuteling:

* Versleuteling inschakelen op nieuwe Windows VM's van Azure Marketplace-afbeeldingen of aangepaste VHD-afbeeldingen.
* Versleuteling inschakelen op bestaande Windows VM's in Azure.
* Versleuteling inschakelen op Windows VM's die zijn geconfigureerd met behulp van opslagruimten.
* Versleuteling uitschakelen op besturingssysteem- en gegevensstations voor Windows-VM's.
* Vm's van standaardlagen, zoals VM's uit de A-, D-, DS-, G- en GS-serie.

    > [!NOTE]
    > Alle resources (inclusief het Key Vault-, Opslagaccount en VM) moeten zich in dezelfde Azure-regio en hetzelfde Azure-abonnement bevinden.

Schijfversleuteling wordt momenteel niet ondersteund in de volgende scenario's:

* Basic tier VM's.
* VM's die zijn gemaakt met behulp van het implementatiemodel Classic.
* Het bijwerken van de cryptografische sleutels op een reeds versleutelde VM.
* Integratie met on-premises Key Management Service.


## <a name="create-an-azure-key-vault-and-keys"></a>Een Azure Key Vault en toetsen maken
Controleer voordat u begint of de nieuwste versie van de Azure PowerShell-module is geïnstalleerd. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor meer informatie. Vervang in de volgende opdrachtvoorbeelden alle voorbeeldparameters door uw eigen namen, locatie en belangrijke waarden, zoals *myResourceGroup,* *myKeyVault,* *myVM,* enzovoort.

De eerste stap is het maken van een Azure Key Vault om uw cryptografische sleutels op te slaan. Azure Key Vaults kunnen sleutels, geheimen of wachtwoorden opslaan waarmee u ze veilig implementeren in uw toepassingen en services. Voor versleuteling van virtuele schijven maakt u een key vault om een cryptografische sleutel op te slaan die wordt gebruikt om uw virtuele schijven te versleutelen of te decoderen. 

Schakel de Azure Key Vault-provider in uw Azure-abonnement in met [Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)en maak vervolgens een brongroep met [Nieuw-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een naam van de resourcegroep *myResourceGroup* op de locatie *Oost-VS* gemaakt:

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "East US"

Register-AzResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzResourceGroup -Location $location -Name $rgName
```

De Azure Key Vault met de cryptografische sleutels en bijbehorende rekenbronnen zoals opslag en de VM zelf moet zich allemaal in dezelfde regio bevinden. Maak een Azure Key Vault met [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) en schakel de Key Vault in voor gebruik met schijfversleuteling. Geef als volgt een unieke Key Vault-naam op voor *keyVaultName:*

```azurepowershell-interactive
$keyVaultName = "myKeyVault$(Get-Random)"
New-AzKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

U cryptografische sleutels opslaan met behulp van software of HSM-beveiliging (Hardware Security Model).  Een standaard Key Vault slaat alleen softwarebeveiligde sleutels op. Het gebruik van een HSM vereist een premium Key Vault tegen een meerprijs. Als u een premium sleutelkluis wilt maken, voegt u in de vorige stap de parameter *'Premium' van* de Sku toe. In het volgende voorbeeld worden softwarebeveiligde sleutels gebruikt omdat we een standaard Key Vault hebben gemaakt. 

Voor beide beveiligingsmodellen moet het Azure-platform toegang krijgen om de cryptografische sleutels aan te vragen wanneer de VM opstart om de virtuele schijven te decoderen. Maak een cryptografische sleutel in uw sleutelkluis met [Add-AzureKeyVaultKey.](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey) In het volgende voorbeeld wordt een sleutel met de naam *myKey gesleuteld:*

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken
Als u het versleutelingsproces wilt testen, maakt u een VM met [Nieuw-AzVm](https://docs.microsoft.com/powershell/module/az.compute/new-azvm). In het volgende voorbeeld wordt een VM met de naam *myVM gemaakt* met behulp van een *Afbeelding van windows server 2016-datacenter.* Wanneer u om referenties wordt gevraagd, voert u de gebruikersnaam en het wachtwoord in die voor uw vm moeten worden gebruikt:

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
Versleutel uw VM met [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) met de Azure Key Vault-sleutel. In het volgende voorbeeld worden alle belangrijke gegevens opgehaald en wordt de VM met de naam *myVM versleuteld:*

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

Accepteer de prompt om door te gaan met de VM-versleuteling. De VM wordt tijdens het proces opnieuw opgestart. Zodra het versleutelingsproces is voltooid en de VM opnieuw is opgestart, controleert u de versleutelingsstatus met [Get-AzVmDiskEncryptionStatus:](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus)

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
* Zie [Een sleutelkluis instellen voor virtuele machines voor](key-vault-setup.md)meer informatie over het beheren van een Azure Key Vault.
* Zie [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md)voor meer informatie over schijfversleuteling, zoals het voorbereiden van een versleutelde aangepaste vm om te uploaden naar Azure.
