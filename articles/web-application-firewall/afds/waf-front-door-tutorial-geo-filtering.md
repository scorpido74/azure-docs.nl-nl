---
title: Firewallbeleid voor geofilters configureren voor Azure Front Door-service
description: In deze zelfstudie leert u hoe u een geofilterbeleid maakt en het beleid koppelt aan uw bestaande front-door frontendhost
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: abcef61d478eccb4e979b60eb845ac8d398a49f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79135867"
---
# <a name="set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Een WAF-beleid voor geofiltering instellen voor uw voordeur

In deze zelfstudie leert u hoe u Azure PowerShell gebruikt om een voorbeeldbeleid voor geofilters te maken en het beleid koppelt aan uw bestaande front-endhost van uw Front Door. Met dit voorbeeldbeleid voor geofiltering worden aanvragen van alle andere landen/regio's, behalve de Verenigde Staten, geblokkeerd.

Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het instellen van een geofilterbeleid, stelt u uw PowerShell-omgeving in en maakt u een voordeurprofiel.
### <a name="set-up-your-powershell-environment"></a>Uw PowerShell-omgeving instellen
Azure PowerShell voorziet in een set van cmdlets die gebruikmaken van het [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-model om uw Azure-resources te beheren. 

U kunt [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) op uw lokale computer installeren en in elke PowerShell-sessie gebruiken. Volg de instructies op de pagina om u aan te melden met uw Azure-referenties en installeer de Az PowerShell-module.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Verbinding maken met Azure met een interactief dialoogvenster voor aanmelden

```
Install-Module -Name Az
Connect-AzAccount
```
Zorg ervoor dat u de huidige versie van PowerShellGet hebt geïnstalleerd. Voer de onderstaande opdracht uit en open PowerShell opnieuw.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>Az.FrontDoor-module installeren 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Een voordeurprofiel maken

Maak een voordeurprofiel door de instructies te volgen die in [Quickstart zijn beschreven: Maak een voordeurprofiel.](../../frontdoor/quickstart-create-front-door.md)

## <a name="define-geo-filtering-match-condition"></a>De voorwaarde voor geofilteringsovereenkomst definiëren

Maak een voorbeeldovereenkomstvoorwaarde waarmee aanvragen die niet afkomstig zijn van 'VS' worden geselecteerd met [Nieuw-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) op parameters bij het maken van een wedstrijdvoorwaarde. Twee letterlandcodes naar landtoewijzing worden geleverd in [Wat is geofiltering op een domein voor Azure Front Door?](waf-front-door-geo-filtering.md).

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
 
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Overeenkomstvoorwaarde voor geofilter toevoegen aan een regel met Actie en Prioriteit

Maak een object `nonUSBlockRule` CustomRule op basis van de wedstrijdvoorwaarde, een actie en een prioriteit met [Nieuw-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  Een CustomRule kan meerdere overeenkomstvoorwaarden hebben.  In dit voorbeeld is Actie ingesteld op Blokkeren en is Prioriteit ingesteld op 1, de hoogste prioriteit.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```

## <a name="add-rules-to-a-policy"></a>Regels toevoegen aan een beleid

Zoek de naam van de resourcegroep die `Get-AzResourceGroup`het frontdoorprofiel bevat met behulp van . Maak vervolgens `geoPolicy` een beleidsobject `nonUSBlockRule` met [nieuw-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) in de opgegeven resourcegroep die het frontdoorprofiel bevat. U moet een unieke naam opgeven voor het geobeleid. 

In het volgende voorbeeld wordt de naam van de resourcegroep *myResourceGroupFD1* gebruikt met de veronderstelling dat u het frontdoorprofiel hebt gemaakt met instructies in het [quickstart-artikel: Een voordeurartikel maken.](../../frontdoor/quickstart-create-front-door.md) Vervang in het onderstaande voorbeeld de beleidsnaam *geoPolicyAllowUSOnly* door een unieke beleidsnaam.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>WAF-beleid koppelen aan een frontdoor frontend host

Koppel het WAF-beleidsobject aan de bestaande frontdoorfrontendhost en werk frontdoor-eigenschappen bij. 

Haal hiervoor eerst het voordeurobject op met [Get-AzFrontDoor.](/powershell/module/az.frontdoor/get-azfrontdoor) 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Stel vervolgens de eigenschap Frontend WebApplicationFirewallPolicyLink in `geoPolicy`op de resourceId van de [set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> U hoeft de eigenschap WebApplicationFirewallPolicyLink slechts één keer in te stellen om een WAF-beleid te koppelen aan een frontend-host voor de voordeur. Volgende beleidsupdates worden automatisch toegepast op de frontendhost.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure-firewall voor webtoepassingen](../overview.md).
- Lees hoe u [een Front Door maakt](../../frontdoor/quickstart-create-front-door.md).
