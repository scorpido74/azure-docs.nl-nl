---
title: 'Power shell: een externe gebruiker toevoegen aan een lab in Azure DevTest Labs'
description: Met dit Power shell-script wordt een externe gebruiker toegevoegd aan een lab in Azure DevTest Labs.
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
ms.openlocfilehash: da5cc4a3809b1867e8aa2c3b21e88c2f30ae3d4d
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169669"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Power shell gebruiken om een externe gebruiker toe te voegen aan een lab in Azure DevTest Labs

Met dit Power shell-voorbeeld script wordt een externe gebruiker toegevoegd aan een lab in Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Vereisten
* **Een Lab**. Het script vereist dat u een bestaand Lab hebt. 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzADUser](/powershell/module/az.resources/get-azaduser) | Het gebruikers object wordt opnieuw geprobeerd vanuit Azure Active Directory. |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | Hiermee wordt de opgegeven rol toegewezen aan de opgegeven Principal, bij het opgegeven bereik. |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Lab Services Power shell-voorbeeld scripts vindt u in de [Azure Lab Services Power shell](../samples-powershell.md)-voor beelden.
