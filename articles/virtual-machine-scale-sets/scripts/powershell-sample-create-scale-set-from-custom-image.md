---
title: Voorbeelden van Azure PowerShell - Een aangepaste VM-installatiekopie gebruiken
description: Met dit script maakt u een virtuele-machineschaalset die gebruikmaakt van een aangepaste VM-installatiekopie als bron voor VM-exemplaren.
author: axayjo
ms.author: akjosh
ms.topic: sample
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.date: 03/27/2018
ms.reviewer: cynthn
ms.custom: akjosh
ms.openlocfilehash: bdd36a2c58bddce5bea0ba43e5d21da9ef89a14d
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83699471"
---
# <a name="create-a-virtual-machine-scale-set-from-a-custom-vm-image-with-powershell"></a>Een virtuele-machineschaalset maken op basis van een aangepaste VM-installatiekopie met PowerShell
Met dit script maakt u een virtuele-machineschaalset die gebruikmaakt van een aangepaste VM-installatiekopie als bron voor VM-exemplaren.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/use-custom-vm-image/use-custom-vm-image.ps1 "Create a virtual machine scale set with a custom VM image")]

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
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. |

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).
