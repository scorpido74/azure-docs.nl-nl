---
title: Web Application Firewall beleid voor geo-filtering configureren voor de Azure front-deur service
description: In deze zelf studie leert u hoe u een beleid voor geofiltering kunt maken en het beleid kunt koppelen aan de bestaande front leider-frontend-host
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: eb97a2d848441a153db47b41644a6226e9d75782
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83747746"
---
# <a name="set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Een WAF-beleid voor geo-filtering instellen voor uw voor deur

In deze zelfstudie leert u hoe u Azure PowerShell gebruikt om een voorbeeldbeleid voor geofilters te maken en het beleid koppelt aan uw bestaande front-endhost van uw Front Door. Met dit voor beeld-beleid voor geo-filtering worden aanvragen van alle andere landen/regio's geblokkeerd, met uitzonde ring van Verenigde Staten.

Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het instellen van een beleid voor geofiltering, stelt u uw Power shell-omgeving in en maakt u een voor deur profiel.
### <a name="set-up-your-powershell-environment"></a>Uw PowerShell-omgeving instellen
Azure PowerShell voorziet in een set van cmdlets die gebruikmaken van het [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-model om uw Azure-resources te beheren. 

U kunt [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) op uw lokale computer installeren en in elke PowerShell-sessie gebruiken. Volg de instructies op de pagina om u aan te melden met uw Azure-referenties en de AZ Power shell-module te installeren.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Verbinding maken met Azure met een interactief dialoog venster voor aanmelden

```
Install-Module -Name Az
Connect-AzAccount
```
Zorg ervoor dat de huidige versie van PowerShellGet is geïnstalleerd. Voer de onderstaande opdracht uit en open PowerShell opnieuw.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>De module AZ.-ingang installeren 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Een voor deur profiel maken

Maak een voor deur profiel door de instructies te volgen die worden beschreven in [Quick Start: een front deur-profiel maken](../../frontdoor/quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Voor waarde voor geografische filtering definiëren

Maak een voor waarde voor het vergelijken van een voor beeld waarbij aanvragen die niet afkomstig zijn van "US" worden geselecteerd met [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) in para meters bij het maken van een matching voorwaarde. De land-/regiocodes van twee letters naar land/regio-toewijzing zijn te vinden in [wat geo-filtering is op een domein voor Azure front-deur?](waf-front-door-geo-filtering.md).

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Overeenkomstvoorwaarde voor geofilter toevoegen aan een regel met Actie en Prioriteit

Maak een CustomRule `nonUSBlockRule` -object op basis van de voor waarde match, een actie en een prioriteit met behulp van [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  Een CustomRule kan meerdere overeenkomstvoorwaarden hebben.  In dit voorbeeld is Actie ingesteld op Blokkeren en is Prioriteit ingesteld op 1, de hoogste prioriteit.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Regels toevoegen aan een beleid

Zoek de naam van de resource groep die het voorste deur profiel bevat met behulp van `Get-AzResourceGroup` . Maak vervolgens een `geoPolicy` beleids object dat `nonUSBlockRule` gebruikmaakt van [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) in de opgegeven resource groep die het voorste deur profiel bevat. U moet een unieke naam opgeven voor het geo-beleid. 

In het volgende voor beeld wordt de naam van de resource groep *myResourceGroupFD1* met de veronderstelling dat u het voorste deur profiel hebt gemaakt met behulp van de instructies in de [Quick Start: een front deur](../../frontdoor/quickstart-create-front-door.md) -artikel maken. Vervang in het onderstaande voor beeld de beleids naam *geoPolicyAllowUSOnly* door een unieke beleids naam.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>WAF-beleid koppelen aan een front-end frontend-host

Koppel het object WAF-beleid aan de bestaande host front deur frontend en werk front-deur eigenschappen bij. 

Als u dit wilt doen, haalt u eerst uw voorste deur object op met [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Stel vervolgens de front-end WebApplicationFirewallPolicyLink-eigenschap in op de resourceId van de `geoPolicy` using [set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> U hoeft slechts één keer op de eigenschap WebApplicationFirewallPolicyLink in te stellen om een WAF-beleid te koppelen aan een front-end frontend-host. Volgende beleids updates worden automatisch toegepast op de frontend-host.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Web Application firewall](../overview.md).
- Lees hoe u [een Front Door maakt](../../frontdoor/quickstart-create-front-door.md).
