---
title: Beheerde resource groep ophalen & grootte van Vm's wijzigen-Azure PowerShell
description: Biedt Azure PowerShell voorbeeld script waarmee een beheerde resource groep voor een door Azure beheerde toepassing wordt opgehaald. De grootte van de virtuele machines wordt door het script aangepast.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 695023f4700370cbe5e9b345f513e38d1cf1fc0c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75650182"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>Resources in een beheerde resourcegroep opvragen en de grootte van virtuele machines wijzigen met Azure PowerShell

Met dit script worden resources opgehaald uit een beheerde resourcegroep en wordt vervolgens de grootte van de virtuele machines in die resourcegroep aangepast.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om de beheerde toepassing te implementeren. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzManagedApplication](https://docs.microsoft.com/powershell/module/az.resources/get-azmanagedapplication) | Hiermee vraagt u een lijst met de beheerde toepassingen op. Geef de naam op van de resourcegroep waarop u de resultaten wilt focussen. |
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Hiermee vraagt u een lijst met resources op. Geef de resourcegroep en het resourcetype op waarop u het resultaat wilt focussen. |
| [Update-AzVM](https://docs.microsoft.com/powershell/module/az.compute/update-azvm) | Hiermee werkt u de grootte van een virtuele machine bij. |


## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van door Azure beheerde toepassingen](../overview.md) voor algemene informatie over beheerde toepassingen.
* Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie over PowerShell.
