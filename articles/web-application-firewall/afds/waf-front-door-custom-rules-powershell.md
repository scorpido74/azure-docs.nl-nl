---
title: WAF-aangepaste regels configureren & standaardregelset voor Azure-voordeur
description: Meer informatie over het configureren van een WAF-beleid bestaat uit zowel aangepaste als beheerde regels voor een bestaand Front Door-eindpunt.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 493ed1a31a23366a90b80d3ab510218c8dce0e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186640"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Een Web Application Firewall-beleid configureren met Azure PowerShell

Het WAF-beleid (Azure Web Application Firewall) definieert inspecties die nodig zijn wanneer een aanvraag bij De Voordeur binnenkomt.
In dit artikel ziet u hoe u een WAF-beleid configureert dat bestaat uit een aantal aangepaste regels en met Azure-beheerde standaardregelset ingeschakeld.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het instellen van een tarieflimietbeleid, stelt u uw PowerShell-omgeving in en maakt u een voordeurprofiel.

### <a name="set-up-your-powershell-environment"></a>Uw PowerShell-omgeving instellen

Azure PowerShell voorziet in een set van cmdlets die gebruikmaken van het [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-model om uw Azure-resources te beheren. 

U kunt [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) op uw lokale computer installeren en in elke PowerShell-sessie gebruiken. Volg de instructies op de pagina om u aan te melden met uw Azure-referenties en installeer de Az PowerShell-module.

#### <a name="sign-in-to-azure"></a>Aanmelden bij Azure

```
Connect-AzAccount

```
Voordat u de Front Door-module installeert, moet u controleren of u de nieuwste versie van PowerShellGet hebt geïnstalleerd. Voer de onderstaande opdracht uit en open PowerShell opnieuw.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Az.FrontDoor-module installeren 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Een voordeurprofiel maken

Een voordeurprofiel maken door de instructies te volgen die in [Quickstart zijn beschreven: Een voordeurprofiel maken](../../frontdoor/quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Aangepaste regel op basis van http-parameters

In het volgende voorbeeld ziet u hoe u een aangepaste regel configureert met twee overeenkomstvoorwaarden met [Nieuw-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). Aanvragen zijn afkomstig van een opgegeven site zoals gedefinieerd door de verwijzer en querytekenreeks bevat geen 'wachtwoord'. 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Aangepaste regel op basis van http-aanvraagmethode

Maak als volgt een regel die de methode "PUT" blokkeert met [New-AzFrontDoorWafCustomRuleObject:](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject)

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Een aangepaste regel maken op basis van groottebeperking

In het volgende voorbeeld wordt een regel gemaakt die aanvragen blokkeert met Url die langer is dan 100 tekens met Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Beheerde standaardregelset toevoegen

In het volgende voorbeeld wordt een beheerde standaardregelset gemaakt met Azure PowerShell:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Een beveiligingsbeleid configureren

Zoek de naam van de resourcegroep die `Get-AzResourceGroup`het frontdoorprofiel bevat met behulp van . Configureer vervolgens een beveiligingsbeleid met gemaakte regels in de vorige stappen met [Nieuw-AzFrontDoorWafBeleid](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) in de opgegeven resourcegroep die het frontdoorprofiel bevat.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Het beleid koppelen aan een front-door front-end host

Koppel het beveiligingsbeleid object aan een bestaande Front Door front-end host en werk front door eigenschappen bij. Haal eerst het object Voordeur op met [Get-AzFrontDoor.](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)
Stel vervolgens de eigenschap Front-end *WebApplicationFirewallPolicyLink* in op de *resourceId* van de '$myWAFPolicy$' die in de vorige stap is gemaakt met [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 

In het onderstaande voorbeeld wordt de naam van de resourcegroep *myResourceGroupFD1* gebruikt met de veronderstelling dat u het frontdoorprofiel hebt gemaakt met instructies in het [quickstart-artikel: Een voordeurartikel maken.](../../frontdoor/quickstart-create-front-door.md) Vervang in het onderstaande voorbeeld ook $frontDoorName door de naam van uw voordeurprofiel. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> U hoeft de eigenschap *WebApplicationFirewallPolicyLink* slechts één keer in te stellen om een beveiligingsbeleid te koppelen aan een frontdoorfront-end. Volgende beleidsupdates worden automatisch toegepast op de front-end.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Voordeur](../../frontdoor/front-door-overview.md) 
- Meer informatie over [WAF met Voordeur](afds-overview.md)
