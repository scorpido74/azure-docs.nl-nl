---
title: Een IP-beperkings regel configureren met een firewall regel voor webtoepassingen voor de Azure front-deur service
description: Meer informatie over het configureren van een firewall regel voor webtoepassingen om IP-adressen te beperken voor een bestaand Azure front deur-service-eind punt.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: d0a32c4d59d580cb82ad6174c57ebbf677ac75b3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517161"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Een IP-beperkings regel configureren met een Web Application Firewall voor de Azure front-deur service
In dit artikel wordt beschreven hoe u IP-beperkings regels configureert in een Web Application firewall (WAF) voor de Azure front-deur service met behulp van de Azure CLI-, Azure PowerShell-of Azure Resource Manager-sjabloon.

Een op IP-adres gebaseerde toegangs beheer regel is een aangepaste WAF-regel waarmee u de toegang tot uw webtoepassingen kunt beheren. Dit doet u door een lijst met IP-adressen of IP-adresbereiken op te geven in de CIDR-notatie (Classless Inter-Domain Routing).

Uw webtoepassing is standaard toegankelijk via internet. Als u de toegang tot clients wilt beperken in een lijst met bekende IP-adressen of IP-adresbereiken, kunt u een IP-overeenkomende regel maken die de lijst met IP-adressen als overeenkomende waarden bevat en de operator is ingesteld op ' not ' (negatie is True) en de actie die moet worden **geblokkeerd**. Nadat een IP-beperkings regel is toegepast, ontvangen aanvragen die afkomstig zijn van adressen buiten deze lijst met toegestane antwoorden een 403 verboden antwoord.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Een WAF-beleid configureren met de Azure CLI

### <a name="prerequisites"></a>Vereisten
Voordat u begint met het configureren van een IP-beperkings beleid, moet u uw CLI-omgeving instellen en een Azure front-deur service profiel maken.

#### <a name="set-up-the-azure-cli-environment"></a>De Azure CLI-omgeving instellen
1. Installeer de [Azure cli](/cli/azure/install-azure-cli)of gebruik Azure Cloud shell. Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. In deze shell is de Azure CLI vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Selecteer de knop **try it** in de CLI-opdrachten die volgen en meld u vervolgens aan bij uw Azure-account in de Cloud shell-sessie die wordt geopend. Nadat de sessie is gestart, voert u `az extension add --name front-door` in om de service-extensie van de Azure-front-deur toe te voegen.
 2. Als u de CLI lokaal gebruikt in bash, meldt u zich aan bij Azure met behulp van `az login`.

#### <a name="create-an-azure-front-door-service-profile"></a>Een Azure front-deur service-profiel maken
Maak een Azure front-deur service profiel door de instructies te volgen die worden beschreven in [Quick Start: een front deur maken voor een Maxi maal beschik bare, wereld wijde webtoepassing](../../frontdoor/quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Een WAF-beleid maken

Maak een WAF-beleid met behulp van de opdracht [AZ Network front-deur WAF-Policy Create](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) . Vervang in het volgende voor beeld de beleids naam *IPAllowPolicyExampleCLI* door een unieke beleids naam.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Een aangepaste regel voor IP-toegangs beheer toevoegen

Gebruik de opdracht [AZ Network front-deur WAF-Policy Custom-Rule Create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) om een aangepaste IP-toegangs beheer regel toe te voegen voor het WAF-beleid dat u zojuist hebt gemaakt.

In de volgende voor beelden:
-  Vervang *IPAllowPolicyExampleCLI* door uw unieke beleid dat u eerder hebt gemaakt.
-  Vervang *IP-adres bereik-1*, *IP-adres-Range-2* door uw eigen bereik.

Maak eerst een regel voor IP-invoer voor het beleid dat u in de vorige stap hebt gemaakt. Opmerking **--defer** is vereist omdat een regel een match-voor waarde moet hebben om in de volgende stap te worden toegevoegd.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Voeg vervolgens de voor waarde match toe aan de regel:

```azurecli
az network front-door waf-policy rule match-condition add\
--match-variable RemoteAddr \
--operator IPMatch
--values "ip-address-range-1" "ip-address-range-2"
--negate true\
--name IPAllowListRule\
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>De ID van een WAF-beleid zoeken 
Zoek naar de ID van een WAF-beleid met behulp van de opdracht [AZ Network front-deur WAF-Policy show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) . Vervang *IPAllowPolicyExampleCLI* in het volgende voor beeld met uw unieke beleid dat u eerder hebt gemaakt.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Een WAF-beleid koppelen aan een Azure front-endwebserver-service-front-end-host

Stel de *WebApplicationFirewallPolicyLink* -id van de Azure front-deur service in op de beleids-id met behulp van de opdracht [AZ Network front-deur update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) . Vervang *IPAllowPolicyExampleCLI* door uw unieke beleid dat u eerder hebt gemaakt.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
In dit voor beeld wordt het WAF-beleid toegepast op **FrontendEndpoints [0]** . U kunt het WAF-beleid koppelen aan uw front-ends.
> [!Note]
> U moet de eigenschap **WebApplicationFirewallPolicyLink** slechts eenmaal instellen om een WAF-beleid te koppelen aan een front-end van de Azure front deur-service. Volgende beleids updates worden automatisch toegepast op de front-end.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Een WAF-beleid configureren met Azure PowerShell

### <a name="prerequisites"></a>Vereisten
Voordat u begint met het configureren van een IP-beperkings beleid, moet u uw Power shell-omgeving instellen en een Azure front-deur service profiel maken.

#### <a name="set-up-your-powershell-environment"></a>Uw PowerShell-omgeving instellen
Azure PowerShell biedt een set cmdlets die gebruikmaken van het [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) model voor het beheer van Azure-resources.

U kunt [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) op uw lokale computer installeren en in elke PowerShell-sessie gebruiken. Volg de instructies op de pagina om u aan te melden bij Power shell met behulp van uw Azure-referenties en installeer vervolgens de AZ-module.

1. Maak verbinding met Azure met behulp van de volgende opdracht en gebruik vervolgens een interactief dialoog venster om u aan te melden.
    ```
    Connect-AzAccount
    ```
 2. Zorg ervoor dat u de huidige versie van de PowerShellGet-module hebt geïnstalleerd voordat u een Azure front-deur service module installeert. Voer de volgende opdracht uit en open Power shell opnieuw.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installeer de module AZ.-ingang met behulp van de volgende opdracht. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Een Azure front-deur service-profiel maken
Maak een Azure front-deur service profiel door de instructies te volgen die worden beschreven in [Quick Start: een front deur maken voor een Maxi maal beschik bare, wereld wijde webtoepassing](../../frontdoor/quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Een IP-match voorwaarde definiëren
Gebruik de opdracht [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) om een overeenkomende IP-voor waarde te definiëren.
Vervang in het volgende voor beeld *IP-adres-bereik-1*, *IP-adres-Range-2* door uw eigen bereik.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Een aangepaste regel voor het toestaan van IP-adressen maken

Gebruik de opdracht [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) om een actie te definiëren en een prioriteit in te stellen. In het volgende voor beeld worden aanvragen die niet afkomstig zijn van client Ip's die overeenkomen met de lijst, geblokkeerd.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Een WAF-beleid configureren
Zoek de naam van de resource groep die het Azure front-deur service profiel bevat met behulp van `Get-AzResourceGroup`. Configureer vervolgens een WAF-beleid met de IP-regel met behulp van [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Een WAF-beleid koppelen aan een Azure front-endwebserver-service-front-end-host

Koppel een WAF-beleids object aan een bestaande front-end-host en werk de eigenschappen van de Azure front-deur service bij. Haal eerst het Azure front-deur service object op met behulp van [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Stel vervolgens de eigenschap **WebApplicationFirewallPolicyLink** in op de resource-ID van *$IPAllowPolicyExamplePS*, die u in de vorige stap hebt gemaakt, met behulp van de [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) opdracht.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> In dit voor beeld wordt het WAF-beleid toegepast op **FrontendEndpoints [0]** . U kunt een WAF-beleid koppelen aan uw front-ends. U moet de eigenschap **WebApplicationFirewallPolicyLink** slechts eenmaal instellen om een WAF-beleid te koppelen aan een front-end van de Azure front deur-service. Volgende beleids updates worden automatisch toegepast op de front-end.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Een WAF-beleid configureren met een resource manager-sjabloon
Als u de sjabloon wilt weer geven waarmee een Azure front deur-service beleid en een WAF-beleid met aangepaste IP-beperkings regels worden gemaakt, gaat u naar [github](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een Azure front-deur service profiel](../../frontdoor/quickstart-create-front-door.md).
