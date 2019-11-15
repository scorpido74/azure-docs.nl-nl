---
title: Peer twee virtuele netwerken-Azure PowerShell script voorbeeld
description: Azure PowerShell-voorbeeldscript - twee virtuele netwerken peeren
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 03/20/2018
ms.author: kumud
ms.openlocfilehash: 4061997aa2efbae250b30fc58cef06b1249c2b8f
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091291"
---
# <a name="peer-two-virtual-networks-script-sample"></a>Voorbeeldscript voor peering van twee virtuele netwerken

Met dit voorbeeldscript worden twee virtuele netwerken in dezelfde regio gemaakt en verbonden via het Azure-netwerk. Nadat het script is uitgevoerd, maakt u een peering tussen twee virtuele netwerken.

U kunt het script uitvoeren vanuit de Azure [Cloud Shell](https://shell.azure.com/powershell) of vanuit een lokale installatie van PowerShell. Als u PowerShell lokaal gebruikt, vereist dit script de versie 5.4.1 of hoger van de Az PowerShell-module. Voer `Get-Module -ListAvailable Az` uit om na te gaan welke versie er is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/virtual-network/peer-two-virtual-networks/peer-two-virtual-networks.ps1 "Peer two networks")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Voer de volgende opdracht uit om de resourcegroep, VM en alle gerelateerde resources te verwijderen:

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het maken van een resourcegroep, een virtuele machine en alle gerelateerde resources. Elke opdracht in de volgende tabel is een koppeling naar specifieke documentatie over de opdracht:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)| Hiermee maakt u een virtueel Azure-netwerk en -subnet. |
| [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering) | Hiermee maakt u een peering tussen twee virtuele netwerken.  |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep met inbegrip van alle geneste resources. |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/azure/overview) voor meer informatie over Azure PowerShell.

Meer PowerShell-voorbeeldscripts voor virtuele netwerken vindt u in [PowerShell-voorbeelden voor virtuele netwerken](../powershell-samples.md).