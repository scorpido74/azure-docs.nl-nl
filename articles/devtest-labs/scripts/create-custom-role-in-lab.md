---
title: 'Power shell: een aangepaste rol maken in een lab in Azure DevTest Labs'
description: Dit artikel bevat een Azure PowerShell script waarmee een externe gebruiker wordt toegevoegd aan een lab in Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 9b6e4d47babd17916e31ab2762ec87f6aa433da6
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/12/2020
ms.locfileid: "88136198"
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
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Een lijst met alle Azure-functies die beschikbaar zijn voor toewijzing. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Hiermee maakt u een aangepaste rol. |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Lab Services Power shell-voorbeeld scripts vindt u in de [Azure Lab Services Power shell](../samples-powershell.md)-voor beelden.
