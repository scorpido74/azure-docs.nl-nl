---
title: 'Power shell: een Marketplace-installatie kopie toevoegen aan een lab in Azure DevTest Labs'
description: Met dit Power shell-script wordt een Marketplace-installatie kopie toegevoegd aan een lab in Azure DevTest Labs.
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
ms.openlocfilehash: d2f5d6b6b9500ccd90630e8920c09340658fb76f
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100177"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Power shell gebruiken om een Marketplace-installatie kopie toe te voegen aan een lab in Azure DevTest Labs

Met dit Power shell-voorbeeld script wordt een Marketplace-installatie kopie toegevoegd aan een lab in Azure DevTest Labs. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Vereisten
* **Een Lab**. Het script vereist dat u een bestaand Lab hebt. 

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt: 

| Opdracht | Opmerkingen |
|---|---|
| Zoeken-AzResource | Zoekt naar bronnen op basis van opgegeven para meters. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Haalt resources op. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Wijzigt een resource. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Een resource maken. |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/) voor meer informatie over Azure PowerShell.

Aanvullende Azure Lab Services Power shell-voorbeeld scripts vindt u in de [Azure Lab Services Power shell](../samples-powershell.md)-voor beelden.
