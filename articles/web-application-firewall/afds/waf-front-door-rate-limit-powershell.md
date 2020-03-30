---
title: WAF-limietregel configureren voor voordeur - Azure PowerShell
description: Meer informatie over het configureren van een tarieflimietregel voor een bestaand frontdoor-eindpunt.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: b034159c3d12927f6425b3dc3c5b5609af9b0b76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649361"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Een begrenzesregel voor webtoepassingsfirewall configureren met Azure PowerShell
De WAF-betariefregel (Azure Web Application Firewall) voor Azure Front Door regelt het aantal aanvragen dat tijdens een duur van één minuut van clients is toegestaan.
In dit artikel ziet u hoe u een WAF-limietregel configureert die het aantal aanvragen regelt dat van clients naar een webtoepassing is toegestaan en die */promo* in de URL bevat met Azure PowerShell.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten
Voordat u begint met het instellen van een tarieflimietbeleid, stelt u uw PowerShell-omgeving in en maakt u een voordeurprofiel.
### <a name="set-up-your-powershell-environment"></a>Uw PowerShell-omgeving instellen
Azure PowerShell voorziet in een set van cmdlets die gebruikmaken van het [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-model om uw Azure-resources te beheren. 

U kunt [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) op uw lokale computer installeren en in elke PowerShell-sessie gebruiken. Volg de instructies op de pagina om u aan te melden met uw Azure-referenties en installeer de Az PowerShell-module.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Verbinding maken met Azure met een interactief dialoogvenster voor aanmelden
```
Connect-AzAccount

```
Controleer voordat u de Front Door-module installeert of de huidige versie van PowerShellGet is geïnstalleerd. Voer de volgende opdracht uit en open PowerShell opnieuw.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Az.FrontDoor-module installeren 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Een voordeurprofiel maken
Een voordeurprofiel maken door de instructies te volgen die in [Quickstart zijn beschreven: Een voordeurprofiel maken](../../frontdoor/quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>Url-overeenkomstvoorwaarden definiëren
Definieer een URL-overeenkomstvoorwaarde (URL bevat /promo) met [Nieuw-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
In het volgende voorbeeld wordt */promo* als de waarde van de variabele *RequestUri gematcht:*

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Een aangepaste regel voor tarieflimiet maken
Stel een tarieflimiet in met [Nieuw-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). In het volgende voorbeeld wordt de limiet ingesteld op 1000. Verzoeken van een klant naar de promopagina van meer dan 1000 gedurende een minuut worden geblokkeerd totdat de volgende minuut begint.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Een beveiligingsbeleid configureren

Zoek de naam van de resourcegroep die `Get-AzureRmResourceGroup`het frontdoorprofiel bevat met behulp van . Configureer vervolgens een beveiligingsbeleid met een aangepaste tarieflimietregel met [Nieuw-AzFrontDoorWafBeleid](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) in de opgegeven resourcegroep die het frontdoorprofiel bevat.

In het onderstaande voorbeeld wordt de naam van de resourcegroep *myResourceGroupFD1* gebruikt met de veronderstelling dat u het frontdoorprofiel hebt gemaakt met instructies in het [quickstart-artikel: Een voordeurartikel maken.](../../frontdoor/quickstart-create-front-door.md)

 met behulp van [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Het beleid koppelen aan een front-door front-end host
Koppel het beveiligingsbeleid object aan een bestaande Front Door front-end host en werk front door eigenschappen bij. Haal eerst het voordeurobject op met de opdracht [Get-AzFrontDoor.](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)
Stel vervolgens de eigenschap Front-end *WebApplicationFirewallPolicyLink* in op de *resourceId* van de $ratePolicy die in de vorige stap is gemaakt met de opdracht [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 

In het onderstaande voorbeeld wordt de naam van de resourcegroep *myResourceGroupFD1* gebruikt met de veronderstelling dat u het frontdoorprofiel hebt gemaakt met instructies in het [quickstart-artikel: Een voordeurartikel maken.](../../frontdoor/quickstart-create-front-door.md) Vervang in het onderstaande voorbeeld ook $frontDoorName door de naam van uw voordeurprofiel. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> U hoeft de eigenschap *WebApplicationFirewallPolicyLink* slechts één keer in te stellen om een beveiligingsbeleid te koppelen aan een frontdoorfront-end. Volgende beleidsupdates worden automatisch toegepast op de front-end.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Voordeur.](../../frontdoor/front-door-overview.md) 


