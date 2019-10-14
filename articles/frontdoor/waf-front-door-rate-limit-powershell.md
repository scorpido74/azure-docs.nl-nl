---
title: Een regel voor het instellen van een Web Application Firewall frequentie configureren voor de voor deur-Azure PowerShell
description: Meer informatie over het configureren van een frequentie limiet regel voor een bestaand eind punt van de voor deur.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: c4cd906148f0f83ab4d66a9daaa606d7b9c183cd
ms.sourcegitcommit: 9858ab651a520c26f0ed18215e650efbf1fc5de9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2019
ms.locfileid: "72303591"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Een limiet regel voor Web Application Firewall frequentie configureren met behulp van Azure PowerShell
De frequentie limiet regel voor Azure Web Application Firewall (WAF) voor Azure front-deur bepaalt het aantal aanvragen dat is toegestaan vanaf één client-IP tijdens een duur van één minuut.
In dit artikel wordt beschreven hoe u een regel voor frequentie limiet voor WAF configureert waarmee het aantal aanvragen dat is toegestaan van één enkele client naar een webtoepassing die */promo* bevat in de URL met behulp van Azure PowerShell, wordt beheerd.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten
Voordat u begint met het instellen van een beleid voor frequentie limieten, stelt u uw Power shell-omgeving in en maakt u een voor deur profiel.
### <a name="set-up-your-powershell-environment"></a>Uw PowerShell-omgeving instellen
Azure PowerShell voorziet in een set van cmdlets die gebruikmaken van het [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-model om uw Azure-resources te beheren. 

U kunt [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) op uw lokale computer installeren en in elke PowerShell-sessie gebruiken. Volg de instructies op de pagina om u aan te melden met uw Azure-referenties en de AZ Power shell-module te installeren.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Verbinding maken met Azure met een interactief dialoog venster voor aanmelden
```
Connect-AzAccount

```
Voordat u de Front Door-module installeert, moet u controleren of u de nieuwste versie van PowerShellGet hebt geïnstalleerd. Voer de onderstaande opdracht uit en open PowerShell opnieuw.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>De module AZ.-ingang installeren 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Een voor deur profiel maken
Maak een voor deur profiel door de instructies te volgen die worden beschreven in [Quick Start: een front deur-profiel maken](quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>URL-matching voorwaarden definiëren
Definieer een URL-match voorwaarde (URL bevat/promo) met behulp van [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
Het volgende voor beeld komt overeen met */promo* als de waarde van de variabele *RequestUri* :

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Een regel voor aangepaste frequentie limiet maken
Stel een frequentie limiet in met behulp van [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). In het volgende voor beeld is de limiet ingesteld op 1000. Aanvragen van elke client naar de promotie pagina van meer dan 1000 gedurende één minuut worden geblokkeerd tot de volgende minuut wordt gestart.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Een beveiligings beleid configureren

Zoek de naam van de resource groep die het voorste deur profiel bevat met `Get-AzureRmResourceGroup`. Configureer vervolgens een beveiligings beleid met een aangepaste frequentie limiet regel met [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) in de opgegeven resource groep die het voorste deur profiel bevat.

In het onderstaande voor beeld wordt de naam van de resource groep *myResourceGroupFD1* met de veronderstelling dat u het voorste deur profiel hebt gemaakt met behulp van de instructies in de [Snelstartgids: een voor deur maken](quickstart-create-front-door.md) met behulp [van New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Beleid koppelen aan een front-end-host voor de voor deur
Koppel het object voor het beveiligings beleid aan een bestaande front-endwebserver front-end-host en werk front-deur eigenschappen bij. Haal eerst het voorste deur object op met de opdracht [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) .
Stel vervolgens de front-end *WebApplicationFirewallPolicyLink* -eigenschap in op de *resourceId* van het $ratePolicy dat in de vorige stap is gemaakt met behulp van de opdracht [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) . 

In het onderstaande voor beeld wordt de naam van de resource groep *myResourceGroupFD1* met de veronderstelling dat u het voorste deur profiel hebt gemaakt met behulp van de instructies in de [Quick Start: een front deur](quickstart-create-front-door.md) -artikel maken. Vervang ook in het onderstaande voor beeld $frontDoorName door de naam van uw voorste deur profiel. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> U hoeft slechts één keer op de eigenschap *WebApplicationFirewallPolicyLink* in te stellen om een beveiligings beleid te koppelen aan de front-end van de voor deur. Volgende beleids updates worden automatisch toegepast op de front-end.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [voor deur](front-door-overview.md) 


