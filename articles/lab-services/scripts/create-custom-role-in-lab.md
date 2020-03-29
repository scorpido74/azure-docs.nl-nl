---
title: PowerShell - Een aangepaste rol maken in een lab in Azure DevTest Labs
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
ms.openlocfilehash: 9605b3c63877335b8f180ae5fd2b06edc1601096
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760444"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>PowerShell gebruiken om een aangepaste rol te maken in een lab in Azure DevTest Labs

Met dit voorbeeld maakt PowerShell-script een aangepaste rol die u gebruiken in een lab in Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Vereisten
* **Een lab.** Het script vereist dat je een bestaand lab hebt. 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Hier worden de bewerkingen uitgevoerd voor een Azure-resourceprovider die kan worden gesecurerbaar met Azure RBAC. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Hier worden alle Azure RBAC-rollen weergegeven die beschikbaar zijn voor toewijzing. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Hiermee maakt u een aangepaste rol. |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Lab Services PowerShell-scriptvoorbeelden zijn te vinden in de [Azure Lab Services PowerShell-voorbeelden.](../samples-powershell.md)
