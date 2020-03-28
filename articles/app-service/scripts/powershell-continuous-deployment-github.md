---
title: 'PowerShell: continu implementeren vanuit GitHub'
description: Meer informatie over het gebruik van Azure PowerShell om de implementatie en het beheer van App Service te automatiseren. In dit voorbeeld ziet u hoe u een app maakt met CI/CD van GitHub.
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: eee6ac9f9c469f9e1a9344ab4a30626c219d7836
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74685157"
---
# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Een web-app maken met continue implementatie vanuit GitHub

Met dit voorbeeldscript wordt een web-app in App-service gemaakt met de bijbehorende bronnen en wordt [de continue implementatie](../deploy-continuous-deployment.md) vanuit een GitHub-opslagplaats ingesteld. Zie [Een web-app maken en code implementeren vanuit GitHub](powershell-deploy-github.md) voor de implementatie van GitHub zonder continue implementatie.

Installeer zo nodig de Azure PowerShell volgens de instructies in de [Azure PowerShell handleiding](/powershell/azure/overview) en voer vervolgens `Connect-AzAccount` uit om verbinding te maken met Azure. Controleer ook of:

- De code van de toepassing zich bevindt in een openbare of particuliere GitHub-opslagplaats waarvan u eigenaar bent. Om automatische builds te krijgen, structureerjet u uw repository volgens de [tabel Uw repository voorbereiden.](../deploy-continuous-deployment.md#prepare-your-repository)
- Je hebt [een persoonlijk toegangstoken gemaakt in je GitHub-account.](https://help.github.com/en/articles/creating-a-personal-access-token-for-the-command-line)

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Create a web app with continuous deployment from GitHub")]

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
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Hiermee wijzigt u een resource in een resourcegroep. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

Meer voorbeelden voor Azure Powershell voor Azure App Service Web Apps vindt u in de [voorbeelden van Azure PowerShell](../samples-powershell.md).
