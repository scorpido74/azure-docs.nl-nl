---
title: Azure PowerShell-voorbeeldscript - IIS met DSC
description: Azure PowerShell-voorbeeldscript - IIS met DSC
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/12/2017
ms.author: cynthn
ms.openlocfilehash: 3b8ab8c14935554e74c562346e0bd88eb5e3ad49
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037182"
---
# <a name="create-an-iis-vm-with-powershell"></a>Een IIS-VM maken met PowerShell

Met dit script maakt u een virtuele machine in Azure waarop Windows Server 2016 wordt uitgevoerd, waarna de Azure Virtual Machine DSC Extension wordt gebruikt om IIS te installeren. Nadat het script is uitgevoerd, is de standaardwebsite van IIS bereikbaar via het openbare IP-adres van de virtuele machine.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-vm-dsc/create-windows-vm-iis-dsc.ps1 "Create VM IIS DSC")]

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
| [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) | Hiermee maakt u de virtuele machine en verbindt u deze met de netwerkkaart, het virtuele netwerk, het subnet en de netwerkbeveiligingsgroep. Deze opdracht opent ook poort 80 en stelt de beheerdersreferenties in. |
| [Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) | Voeg een VM-extensie toe aan de virtuele machine. In dit voorbeeld wordt de DSC-extensie gebruikt om IIS te installeren. |
|[Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

U kunt extra PowerShell-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor Windows-VM's](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
