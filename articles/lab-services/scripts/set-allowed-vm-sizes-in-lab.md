---
title: 'PowerShell-script: toegestane VM-formaten instellen in Azure Lab Services | Microsoft Documenten'
description: Deze artikelen bevatten een voorbeeld van PowerShell-script waarin toegestane vm-formaten (virtual machine) zijn ingesteld in Azure Lab Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760445"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>PowerShell gebruiken om toegestane VM-formaten in Azure Lab Services in te stellen

Met dit voorbeeld powershell-scriptsets zijn vm-formaten (virtual machine) toegestaan in Azure Lab Services.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Vereisten
* **Een lab.** Het script vereist dat je een bestaand lab hebt. 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| Find-AzResource | Hiermee wordt gezocht naar resources op basis van opgegeven parameters. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Krijgt middelen. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Hiermee wijzigt u een bron. |
| [Nieuw-AzResource](/powershell/module/az.resources/new-azresource) | Een resource maken. |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Lab Services PowerShell-scriptvoorbeelden zijn te vinden in de [Azure Lab Services PowerShell-voorbeelden.](../samples-powershell.md)
