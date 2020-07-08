---
title: 'Power shell: een aangepaste rol maken in een lab in Azure DevTest Labs'
description: Dit artikel bevat een Azure PowerShell script waarmee een externe gebruiker wordt toegevoegd aan een lab in Azure DevTest Labs.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2020
ms.author: spelluru
ms.openlocfilehash: a7822d6ea46b7efc21f43e944d0a96f609df389a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84897409"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Power shell gebruiken voor het maken van een aangepaste rol in een lab in Azure DevTest Labs

Met dit Power shell-voorbeeld script maakt u een aangepaste rol voor gebruik in een lab in Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Vereisten
* **Een Lab**. Het script vereist dat u een bestaand Lab hebt. 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Hiermee worden de bewerkingen opgehaald voor een Azure-resource provider die kan worden beveiligd met behulp van Azure RBAC. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Een lijst met alle Azure RBAC-rollen die beschikbaar zijn voor toewijzing. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Hiermee maakt u een aangepaste rol. |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Lab Services Power shell-voorbeeld scripts vindt u in de [Azure Lab Services Power shell](../samples-powershell.md)-voor beelden.
