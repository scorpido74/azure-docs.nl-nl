---
title: PowerShell - Aangepaste afbeelding maken van VHD-bestand in Azure Lab Services
description: Met dit PowerShell-script wordt een aangepaste afbeelding gemaakt van een VHD-bestand in Azure Lab Services.
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
ms.openlocfilehash: 38383462a665ced1ccb6c6a2f062fab0492eee9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169988"
---
# <a name="use-powershell-to-create-a-custom-image-from-a-vhd-file-in-azure-lab-services"></a>PowerShell gebruiken om een aangepaste afbeelding te maken van een VHD-bestand in Azure Lab Services

Met dit voorbeeld maakt PowerShell-script een aangepaste afbeelding van een VHD-bestand in Azure Lab Services

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Vereisten
* **Een lab.** Het script vereist dat je een bestaand lab hebt. 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-image-from-vhd/create-custom-image-from-vhd.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Krijgt middelen. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Hiermee worden de toegangssleutels voor Azure opslagaccounts weergegeven. |
| [Implementatie van nieuwe azresourcegroepen](/powershell/module/az.resources/new-azresourcegroupdeployment) | Hiermee voegt u een Azure-implementatie toe aan een resourcegroep. |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Lab Services PowerShell-scriptvoorbeelden zijn te vinden in de [Azure Lab Services PowerShell-voorbeelden.](../samples-powershell.md)
