---
title: Logboekanalysewerkruimte maken - Azure PowerShell
description: Voorbeeld van Azure PowerShell-script - Een Log Analytics-werkruimte maken
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: 62b02de5d1c08f6047052d71e3be420cceb1c5c0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80054633"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Een Log Analytics-werkruimte maken met PowerShell

Met dit script kunt u snel aan de slag met een Azure Log Analytics-werkruimte. Deze is vereist als u gegevens wilt verzamelen, analyseren of er actie op wilt ondernemen.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om een nieuwe Log Analytics-werkruimte voor uw abonnement te maken. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [Get-azoperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Hiermee wordt informatie opgehaald over een bestaande werkruimte. |
| [Nieuwe-azoperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Hiermee wordt een werkruimte gemaakt in de opgegeven resourcegroep en locatie. |


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

