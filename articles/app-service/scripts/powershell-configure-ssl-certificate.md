---
title: 'Power shell: SSL uploaden en binden'
description: Meer informatie over het gebruik van Azure PowerShell voor het automatiseren van de implementatie en het beheer van App Service. Dit voor beeld laat zien hoe u een aangepast SSL-certificaat verbindt met een app.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: seodec18
ms.openlocfilehash: 4e5e1835927b135037d416ebaca84157c851d361
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685600"
---
# <a name="bind-a-custom-ssl-certificate-to-a-web-app-using-powershell"></a>Een aangepast SSL-certificaat koppelen aan een web-app met PowerShell

Met dit voorbeeldscript wordt een web-app in App Service gemaakt met de bijbehorende resources, waarna het SSL-certificaat van een aangepaste domeinnaam daaraan wordt gekoppeld. 

Installeer zo nodig de Azure PowerShell volgens de instructies in de [Azure PowerShell handleiding](/powershell/azure/overview) en voer vervolgens `Connect-AzAccount` uit om verbinding te maken met Azure. Controleer ook of:

- Een verbinding met Azure is gemaakt met behulp van de opdracht `az login`.
- U hebt toegang tot de pagina voor DNS-configuratie van uw domeinregistrar.
- U hebt een geldig .PFX-bestand en het bijbehorende wachtwoord voor het SSL-certificaat dat u wilt uploaden en koppelen.

## <a name="sample-script"></a>Voorbeeldscript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Nadat het voorbeeldscript is uitgevoerd, kan de volgende opdracht worden gebruikt om de resourcegroep, web-app en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is gekoppeld aan de specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Hiermee maakt u een App Service-abonnement. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Hiermee maakt u een webtoepassing. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Hiermee bewerkt u een App Service-plan om de prijscategorie te wijzigen. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Hiermee wijzigt u de configuratie van een web-app. |
| [New-AzWebAppSSLBinding](/powershell/module/az.websites/new-azwebappsslbinding) | Hiermee maakt u een SSL-certificaatbinding voor een web-app. |

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/overview).

Meer voorbeelden voor Azure Powershell voor Azure App Service Web Apps vindt u in de [voorbeelden van Azure PowerShell](../samples-powershell.md).
