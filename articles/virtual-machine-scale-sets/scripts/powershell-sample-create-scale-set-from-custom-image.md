---
title: Azure PowerShell-voor beelden-een aangepaste VM-installatie kopie gebruiken
description: Met dit script maakt u een virtuele-machineschaalset die gebruikmaakt van een aangepaste VM-installatiekopie als bron voor VM-exemplaren.
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
ms.date: 03/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: aba720f210eb429544e1828934b8245c706547f0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75350871"
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

U kunt extra PowerShell-scriptvoorbeelden voor virtuele machines vinden in de [Azure-documentatie voor virtuele-machineschaalsets](../powershell-samples.md).
