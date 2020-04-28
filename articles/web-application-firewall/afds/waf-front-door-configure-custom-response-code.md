---
title: Een aangepast antwoord configureren voor WAF met behulp van de voor deur van Azure
description: Meer informatie over het configureren van een aangepaste respons code en een bericht wanneer WAF (Web Application firewall) een aanvraag blokkeert.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 215d4058937ad5fded6bef7a36e873b52a1b5ae9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74185350"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Een aangepast antwoord configureren voor de firewall van Azure Web Application

Wanneer Azure Web Application firewall (WAF) met Azure front deur een aanvraag als gevolg van een overeenkomende regel blokkeert, wordt standaard een 403-status code geretourneerd met **de aanvraag is een geblokkeerd** bericht. In dit artikel wordt beschreven hoe u een aangepaste antwoord status code en een antwoord bericht configureert wanneer een aanvraag wordt geblokkeerd door WAF.

## <a name="set-up-your-powershell-environment"></a>Uw PowerShell-omgeving instellen
Azure PowerShell voorziet in een set van cmdlets die gebruikmaken van het [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-model om uw Azure-resources te beheren. 

U kunt [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) op uw lokale computer installeren en in elke PowerShell-sessie gebruiken. Volg de instructies op de pagina om u aan te melden met uw Azure-referenties en de AZ Power shell-module te installeren.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Verbinding maken met Azure met een interactief dialoog venster voor aanmelden
```
Connect-AzAccount
Install-Module -Name Az
```
Zorg ervoor dat de huidige versie van PowerShellGet is geïnstalleerd. Voer de onderstaande opdracht uit en open PowerShell opnieuw.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>De module AZ.-ingang installeren 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. In dit voor beeld maakt u een resource groep met behulp van [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Een nieuw WAF-beleid maken met een aangepast antwoord 

Hieronder ziet u een voor beeld van het maken van een nieuw WAF-beleid met een aangepaste antwoord status code ingesteld op 405 en bericht aan **u geblokkeerd.** met behulp van [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Aangepaste antwoord code of instellingen voor de antwoord tekst wijzigen van een bestaand WAF-beleid met behulp van [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Web Application Firewall met Azure front-deur](../afds/afds-overview.md)