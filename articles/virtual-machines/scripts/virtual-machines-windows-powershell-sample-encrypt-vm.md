---
title: Azure PowerShell-voorbeeldscript - Een Windows-VM versleutelen
description: Azure PowerShell-voorbeeldscript - Een Windows-VM versleutelen
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.openlocfilehash: fb6faec5bcaec3bc4bfb2e15a2a07fdd532e0519
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81460780"
---
# <a name="encrypt-a-windows-virtual-machine-with-azure-powershell"></a>Een virtuele machine voor Windows versleutelen met Azure PowerShell

Met dit script maakt u een beveiligde Azure-sleutelkluis, versleutelingssleutels, een service-principal voor Azure Active Directory en een virtuele Windows-machine (VM). De virtuele machine wordt vervolgens versleuteld met behulp van de versleutelingssleutel uit Key Vault en de referenties van de service-principal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/encrypt-vm/encrypt-windows-vm.ps1 "Encrypt VM disks")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep, VM, en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om de implementatie te maken. Elk item in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault) | Hiermee maakt u een Azure-sleutelkluis voor het opslaan van beveiligde gegevens, zoals versleutelingssleutels. |
| [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/add-azkeyvaultkey) | Hiermee maakt u een versleutelingssleutel in Key Vault. |
| [New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal) | Hiermee maakt u een service-principal voor Azure Active Directory om de toegang tot versleutelingssleutels veilig te verifiëren en beheren. |
| [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Hiermee stelt u de machtigingen in voor de sleutelkluis om de service-principal toegang te bieden tot versleutelingssleutels. |
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Hiermee maakt u de virtuele machine en verbindt u deze met de netwerkkaart, het virtuele netwerk, het subnet en de netwerkbeveiligingsgroep. Deze opdracht opent ook poort 80 en stelt de beheerdersreferenties in. |
| [Get-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/get-azkeyvault) | Haalt de gevraagde informatie op uit de Sleutelkluis |
| [Set-AzVMDiskEncryptionExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdiskencryptionextension) | Hiermee schakelt u versleuteling in op een virtuele machine met behulp van de referenties voor de service-principal en de versleutelingssleutel. |
| [Get-AzVmDiskEncryptionStatus](https://docs.microsoft.com/powershell/module/az.compute/get-azvmdiskencryptionstatus) | Hiermee vraagt u de status op van het versleutelingsproces voor de virtuele machine. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

U kunt extra PowerShell-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Windows-VM's](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
