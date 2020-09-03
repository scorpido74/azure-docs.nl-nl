---
title: 'PowerShell: Een web-app handmatig schalen'
description: Ontdek hoe u Azure PowerShell kunt gebruiken om implementatie en beheer van App Service kunt automatiseren. In dit voorbeeld ziet u hoe u een app handmatig kunt schalen.
author: msangapu-msft
tags: azure-service-management
ms.assetid: de5d4285-9c7d-4735-a695-288264047375
ms.topic: sample
ms.date: 03/20/2017
ms.author: msangapu
ms.custom: mvc, seodec18, devx-track-azurepowershell
ms.openlocfilehash: c9595d2fa4479d84b166874f9f13e43dc494f32a
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89077769"
---
# <a name="scale-a-web-app-manually-using-powershell"></a>Een web-app handmatig schalen met behulp van PowerShell

In dit scenario leert u hoe u een resourcegroep, een app service-plan en een web-app maakt. Vervolgens wordt het App Service-abonnement opgeschaald van één exemplaar naar meerdere exemplaren.

Installeer zo nodig de Azure PowerShell volgens de instructies in de [Azure PowerShell handleiding](/powershell/azure/) en voer vervolgens `Connect-AzAccount` uit om verbinding te maken met Azure.

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/scale-manual/scale-manual.ps1 "Scale a web app manually")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Nadat het voorbeeldscript is uitgevoerd, kan de volgende opdracht worden gebruikt om de resourcegroep, web-app en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name $ResourceGroupName -Force
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Hiermee maakt u een App Service-plan. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Hiermee maakt u een webtoepassing. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Hiermee wijzigt u de configuratie van een web-app. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

Meer voorbeelden voor Azure Powershell voor Azure App Service Web Apps vindt u in de [voorbeelden van Azure PowerShell](../samples-powershell.md).
