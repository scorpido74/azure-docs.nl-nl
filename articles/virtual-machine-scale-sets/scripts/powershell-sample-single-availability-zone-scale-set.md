---
title: Azure PowerShell-voor beelden-schaalset met één zone
description: Dit script maakt een virtuele-machineschaalset met Windows Server 2016 in een enkele Beschikbaarheidszone.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9ef39306b18a03e194b94ae24860151462a870f6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75350793"
---
# <a name="create-a-single-zone-virtual-machine-scale-set-with-powershell"></a>Een virtuele-machineschaalset met één zone maken met PowerShell
Dit script maakt een virtuele-machineschaalset met Windows Server 2016 in een enkele Beschikbaarheidszone. Nadat het script is uitgevoerd, hebt u via RDP toegang tot de virtuele machine.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/create-single-availability-zone/create-single-availability-zone.ps1 "Create single-zone scale set")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie
Gebruik de volgende opdracht om de resourcegroep, de schaalset en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Uitleg van het script
In dit script worden de volgende opdrachten gebruikt om de implementatie te maken. Elk item in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzVmss](/powershell/module/az.compute/new-azvmss) | Hiermee maakt u de virtuele-machineschaalset en alle ondersteunende resources, met inbegrip van virtueel netwerk, load balancer en NAT-regels. |
| [Get-AzVmss](/powershell/module/az.compute/get-azvmss) | Hiermee wordt informatie over de virtuele-machineschaalset opgehaald. |
| [Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) | Hiermee voegt u een VM-extensie voor Custom Script toe om een eenvoudige webtoepassing te installeren. |
| [Update-AzVmss](/powershell/module/az.compute/update-azvmss) | Hiermee werkt u het model van de virtuele-machineschaalset bij om de VM-extensie toe te passen. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Hiermee haalt u informatie op over het toegewezen openbare IP-adres dat door de load balancer wordt gebruikt. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. |

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

U kunt extra PowerShell-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor virtuele-machineschaalsets](../powershell-samples.md).
