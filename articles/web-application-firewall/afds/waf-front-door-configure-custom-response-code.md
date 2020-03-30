---
title: Een aangepaste respons configureren voor WAF met Azure Front Door
description: Meer informatie over het configureren van een aangepaste antwoordcode en -bericht wanneer Web Application Firewall (WAF) een aanvraag blokkeert.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 215d4058937ad5fded6bef7a36e873b52a1b5ae9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185350"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Een aangepast antwoord configureren voor Azure Web Application Firewall

Wanneer Azure Web Application Firewall (WAF) met Azure Front Door een aanvraag blokkeert vanwege een overeenkomende regel, wordt standaard een statuscode van 403 geretourneerd met **Het verzoek wordt geblokkeerd** bericht. In dit artikel wordt beschreven hoe u een aangepaste reactiestatuscode en -antwoordbericht configureert wanneer een verzoek wordt geblokkeerd door WAF.

## <a name="set-up-your-powershell-environment"></a>Uw PowerShell-omgeving instellen
Azure PowerShell voorziet in een set van cmdlets die gebruikmaken van het [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-model om uw Azure-resources te beheren. 

U kunt [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) op uw lokale computer installeren en in elke PowerShell-sessie gebruiken. Volg de instructies op de pagina om u aan te melden met uw Azure-referenties en installeer de Az PowerShell-module.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Verbinding maken met Azure met een interactief dialoogvenster voor aanmelden
```
Connect-AzAccount
Install-Module -Name Az
```
Zorg ervoor dat u de huidige versie van PowerShellGet hebt geïnstalleerd. Voer de onderstaande opdracht uit en open PowerShell opnieuw.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Az.FrontDoor-module installeren 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. In dit voorbeeld maakt u een resourcegroep met [Nieuw-AzResourceGroep](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>Een nieuw WAF-beleid maken met aangepaste respons 

Hieronder vindt u een voorbeeld van het maken van een nieuw WAF-beleid met aangepaste reactiestatuscode ingesteld op 405 en een bericht aan **U wordt geblokkeerd.** met behulp van [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

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

Wijzig aangepaste antwoordcode of antwoordbody-instellingen van een bestaand WAF-beleid met behulp van [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

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
- Meer informatie over [Web Application Firewall met Azure Front Door](../afds/afds-overview.md)