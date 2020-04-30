---
title: Een virtuele Windows-machine maken en versleutelen met behulp van Azure PowerShell
description: In deze Quick Start leert u hoe u Azure PowerShell kunt gebruiken om een virtuele Windows-machine te maken en te versleutelen.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: 6f564a9a4f13136bbe7e28a3600ca71892c82439
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82081588"
---
# <a name="quickstart-create-and-encrypt-a-windows-virtual-machine-in-azure-with-powershell"></a>Snelstartgids: een virtuele Windows-machine maken en versleutelen in azure met Power shell

De Azure PowerShell-module wordt gebruikt voor het maken en beheren van Azure-resources vanaf de PowerShell-opdrachtregel of in scripts. In deze Quick start ziet u hoe u de Azure PowerShell-module kunt gebruiken om een virtuele Windows-machine (VM) te maken, een Key Vault te maken voor de opslag van versleutelings sleutels en de virtuele machine te versleutelen. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.


## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd:

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een virtuele Azure-machine met [New-AzVM](/powershell/module/az.compute/new-azvm). U moet referenties opgeven voor de cmdlet. 

```powershell
$cred = Get-Credential 

New-AzVM -Name MyVm -Credential $cred -ResourceGroupName MyResourceGroup -Image win2016datacenter -Size Standard_D2S_V3
```

Het duurt een paar minuten voor uw virtuele machine is geïmplementeerd. 

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Een Key Vault maken die is geconfigureerd voor versleutelings sleutels

De versleutelings sleutel van Azure Disk Encryption wordt opgeslagen in een Azure Key Vault. Maak een Key Vault met [New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault). Gebruik de para meter-EnabledForDiskEncryption om de Key Vault in te scha kelen om versleutelings sleutels op te slaan.

> [!Important]
> Elk Key Vault moet een unieke naam hebben. In het volgende voor beeld wordt een Key Vault gemaakt met de naam *myKV*, maar u moet iets anders een naam hebben.

```powershell
New-AzKeyvault -name MyKV -ResourceGroupName myResourceGroup -Location EastUS -EnabledForDiskEncryption
```

## <a name="encrypt-the-virtual-machine"></a>De virtuele machine versleutelen

Versleutel uw virtuele machine met [set-AzVmDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension). 

Set-AzVmDiskEncryptionExtension vereist sommige waarden van uw Key Vault-object. U kunt deze waarden verkrijgen door de unieke naam van uw sleutel kluis door te geven aan [Get-AzKeyvault](/powershell/module/az.keyvault/get-azkeyvault).

```powershell
$KeyVault = Get-AzKeyVault -VaultName MyKV -ResourceGroupName MyResourceGroup

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName MyVM -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId
```

Na een paar minuten wordt het volgende door het proces geretourneerd:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK
```

U kunt het versleutelings proces controleren door [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/Get-AzVMDiskEncryptionStatus)uit te voeren.

```powershell
Get-AzVmDiskEncryptionStatus -VMName MyVM -ResourceGroupName MyResourceGroup
```

Wanneer versleuteling is ingeschakeld, wordt het volgende weer gegeven in de geretourneerde uitvoer:

```
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NoDiskFound
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded
```

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt:

```powershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een virtuele machine gemaakt, een Key Vault gemaakt die is ingeschakeld voor versleutelings sleutels en de VM versleuteld.  Ga naar het volgende artikel voor meer informatie over de vereisten voor Azure Disk Encryption voor IaaS-VM's.

> [!div class="nextstepaction"]
> [Overzicht van Azure Disk Encryption](disk-encryption-overview.md)