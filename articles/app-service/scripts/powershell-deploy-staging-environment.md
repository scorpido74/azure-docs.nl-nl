---
title: 'Azure PowerShell-voorbeeldscript: een app maken en code implementeren op de staging-site | Microsoft Docs'
description: Azure PowerShell-voorbeeldscript - Een web-app maken en code implementeren in een faseringsomgeving
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 27cf0680-c3a9-4a58-9f71-6dec09f6b874
ms.service: app-service-web
ms.workload: web
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 7d65a5977ff8db8d31fe7a46d4f99569a8c2270a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087600"
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

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Hiermee maakt u een App Service-plan. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Hiermee maakt u een webtoepassing. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Hiermee bewerkt u een App Service-plan om de prijscategorie te wijzigen. |
| [New-AzWebAppSlot](/powershell/module/az.websites/new-azwebappslot) | Hiermee maakt een implementatiesleuf voor een web-app. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Hiermee wijzigt u een resource in een resourcegroep. |
| [Switch-AzWebAppSlot](/powershell/module/az.websites/switch-azwebappslot) | Hiermee wisselt u een implementatiesleuf van een web-app naar productie. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

Meer voorbeelden voor Azure Powershell voor Azure App Service Web Apps vindt u in de [voorbeelden van Azure PowerShell](../samples-powershell.md).
