---
title: 'PowerShell: code implementeren naar faseringssleuf'
description: Meer informatie over het gebruik van Azure PowerShell om de implementatie en het beheer van App Service te automatiseren. In dit voorbeeld ziet u hoe u code implementeert naar een faseringsomgeving.
tags: azure-service-management
ms.assetid: 27cf0680-c3a9-4a58-9f71-6dec09f6b874
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: 7e8ebe28fa7989997b5d3adfd14c582438a00b1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74684777"
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>Een web-app maken en code implementeren in een faseringsomgeving

Met dit voorbeeldscript maakt u een web-app in App Service met een extra implementatiesite genaamd "staging", en implementeert u vervolgens een voorbeeld-app in de site "staging".

Installeer zo nodig de Azure PowerShell volgens de instructies in de [Azure PowerShell handleiding](/powershell/azure/overview) en voer vervolgens `Connect-AzAccount` uit om verbinding te maken met Azure.

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.ps1?highlight=1 "Create a web app and deploy code to a staging environment")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Nadat het voorbeeldscript is uitgevoerd, kan de volgende opdracht worden gebruikt om de resourcegroep, web-app en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee wordt een resourcegroep gemaakt waarin alle resources worden opgeslagen. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Hiermee maakt u een App Service-plan. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Hiermee maakt u een webtoepassing. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Hiermee bewerkt u een App Service-plan om de prijscategorie te wijzigen. |
| [New-AzWebAppSlot](/powershell/module/az.websites/new-azwebappslot) | Hiermee maakt een implementatiesleuf voor een web-app. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Hiermee wijzigt u een resource in een resourcegroep. |
| [Switch-AzWebAppSlot](/powershell/module/az.websites/switch-azwebappslot) | Hiermee wisselt u een implementatiesleuf van een web-app naar productie. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

Meer voorbeelden voor Azure Powershell voor Azure App Service Web Apps vindt u in de [voorbeelden van Azure PowerShell](../samples-powershell.md).
