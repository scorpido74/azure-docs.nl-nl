---
title: Routeverkeer voor HA-toepassingen - Azure PowerShell - Traffic Manager
description: Azure PowerShell-scriptvoorbeeld - Routeverkeer voor hoge beschikbaarheid van toepassingen
services: traffic-manager
documentationcenter: traffic-manager
author: rohinkoul
manager: kumudD
editor: ''
tags: azure-infrastructure
ms.assetid: ''
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 04/26/2018
ms.author: rohink
ms.openlocfilehash: 3f448683dbb210ab196e27cfd98a376429d03aec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934733"
---
# <a name="route-traffic-for-high-availability-of-applications-using-azure-powershell"></a>Routeverkeer voor hoge beschikbaarheid van toepassingen met Azure PowerShell

Met dit script wordt een resourcegroep, twee app-serviceplannen, twee web-apps, een profiel van verkeersbeheeren en twee eindpunten voor verkeersbeheergemaakt. Traffic Manager leidt het verkeer naar de toepassing in één regio als primaire regio en naar het secundaire gebied wanneer de toepassing in de primaire regio niet beschikbaar is. Voordat u het script uitvoert, moet u de waarden MyWebApp, MyWebAppL1 en MyWebAppL2 wijzigen in unieke waarden in Azure. Nadat u het script hebt uitgevoerd, hebt u toegang tot de app in het primaire gebied met de URL-mywebapp.trafficmanager.net.

Installeer zo nodig de Azure PowerShell volgens de instructies in de [Azure PowerShell handleiding](/powershell/azure) en voer vervolgens `Connect-AzAccount` uit om verbinding te maken met Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Route traffic for high availability")]


Gebruik de volgende opdracht om de resourcegroep, VM, en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup1
Remove-AzResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>Uitleg van het script

Dit script gebruikt de volgende opdrachten voor het maken van een resourcegroep, web-app, Traffic Manager-profiel en alle gerelateerde resources. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Hiermee maakt u een App Service-plan. Dit is als een serverfarm voor uw Azure-web-app. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Hiermee maakt u een Azure-web-app in het App-serviceplan. |
| [Set-AzResource](/powershell/module/az.resources/new-azresource) | Hiermee maakt u een Azure-web-app in het App-serviceplan. |
| [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Hiermee maakt u een Azure Traffic Manager-profiel. |
| [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Hiermee voegt u een eindpunt toe aan een Azure Traffic Manager-profiel. |

## <a name="next-steps"></a>Volgende stappen

Zie [Documentatie over Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) voor meer informatie over Azure PowerShell.

Aanvullende PowerShell-voorbeeldscripts voor netwerken vindt u in de [Documentatie met een overzicht van Azure-netwerken](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
