---
title: PowerShell - Een marketplace-afbeelding toevoegen aan een lab in Azure DevTest Labs
description: Dit PowerShell-script voegt een marketplace-afbeelding toe aan een lab in Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 3d699a22ed06022c6d9df12ade7f202fb304648c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166450"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>PowerShell gebruiken om een marketplace-afbeelding toe te voegen aan een lab in Azure DevTest Labs

Met dit voorbeeld voegt PowerShell-script een marketplace-afbeelding toe aan een lab in Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Vereisten
* **Een lab.** Het script vereist dat je een bestaand lab hebt. 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

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
