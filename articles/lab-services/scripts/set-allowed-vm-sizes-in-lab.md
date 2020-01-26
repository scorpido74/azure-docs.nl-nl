---
title: 'Power shell-script: toegestane VM-grootten instellen in Azure Lab Services | Microsoft Docs'
description: In deze artikelen vindt u een voor beeld van een Power shell-script waarmee toegestane VM-grootten (Virtual Machine) in Azure Lab Services worden ingesteld.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: a1b0e9a4aed475f04ec8dcffa9bc95b7c7c713e1
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760445"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Power shell gebruiken om toegestane VM-grootten in Azure Lab Services in te stellen

Met dit Power shell-voorbeeld script worden toegestane groottes van virtuele machines (VM) in Azure Lab Services ingesteld.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Vereisten
* **Een Lab**. Het script vereist dat u een bestaand Lab hebt. 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| Find-AzResource | Zoekt naar bronnen op basis van opgegeven para meters. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Haalt resources op. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Wijzigt een resource. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Een resource maken. |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Lab Services Power shell-voorbeeld scripts vindt u in de [Azure Lab Services Power shell](../samples-powershell.md)-voor beelden.
