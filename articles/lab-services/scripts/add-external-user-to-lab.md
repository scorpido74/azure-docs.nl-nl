---
title: PowerShell - Een externe gebruiker toevoegen aan een lab in Azure DevTest Labs
description: In dit artikel wordt een Azure PowerShell-script bevat dat een externe gebruiker toevoegt aan een lab in Azure DevTest Labs.
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
ms.openlocfilehash: e9efe5476da051d905feff3d6803cb60ea0c4beb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760484"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>PowerShell gebruiken om een externe gebruiker toe te voegen aan een lab in Azure DevTest Labs

Met dit voorbeeld voegt PowerShell-script een externe gebruiker toe aan een lab in Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Vereisten
* **Een lab.** Het script vereist dat je een bestaand lab hebt. 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzADUser](/powershell/module/az.resources/get-azaduser) | Probeert het gebruikersobject opnieuw uit de active directory van Azure. |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | Wijst de opgegeven rol toe aan de opgegeven principal, bij het opgegeven bereik. |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Lab Services PowerShell-scriptvoorbeelden zijn te vinden in de [Azure Lab Services PowerShell-voorbeelden.](../samples-powershell.md)
