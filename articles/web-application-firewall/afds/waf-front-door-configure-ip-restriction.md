---
title: WAF-regel voor IP-beperking configureren voor Azure Front Door
description: Meer informatie over het configureren van een firewallregel voor webtoepassingen om IP-adressen voor een bestaand Azure Front Door-eindpunt te beperken.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 03/26/2020
ms.author: tyao
ms.openlocfilehash: 077f127648688b25d45b433fa2bc94ee011b3f2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336079"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Een IP-beperkingsregel configureren met een webtoepassingsfirewall voor Azure Front Door

In dit artikel ziet u hoe u IP-beperkingsregels configureert in een WAF (Web Application Firewall) voor Azure Front Door met behulp van de Azure-portal, Azure CLI, Azure PowerShell of een Azure Resource Manager-sjabloon.

Een op IP-adres gebaseerde toegangscontroleregel is een aangepaste WAF-regel waarmee u de toegang tot uw webtoepassingen beheren. Dit gebeurt door een lijst met IP-adressen of IP-adresbereiken op te geven in de CIDR-indeling (Classless Inter-Domain Routing).

Standaard is uw webtoepassing toegankelijk vanaf internet. Als u de toegang tot clients wilt beperken uit een lijst met bekende IP-adressen of IP-adresbereiken, u een IP-matchingregel maken die de lijst met IP-adressen bevat als overeenkomende waarden en de operator instelt op 'Niet' (ontkennen is waar) en de actie om te **blokkeren.** Nadat een IP-beperkingsregel is toegepast, ontvangen aanvragen die afkomstig zijn van adressen buiten deze toegestane lijst een 403 Verboden antwoord.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Een WAF-beleid configureren met de Azure-portal

### <a name="prerequisites"></a>Vereisten

Maak een Azure Front Door-profiel door de instructies te volgen die in [Quickstart zijn beschreven: Een voordeur maken voor een zeer beschikbare wereldwijde webtoepassing.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Een WAF-beleid maken

1. Selecteer in de Azure-portal **Een resource maken,** typ **webtoepassingsfirewall** in het zoekvak en selecteer **Vervolgens WebApplication Firewall (WAF)**.
2. Selecteer **Maken**.
3. Gebruik op de pagina **Een WAF-beleid maken** de volgende waarden om het tabblad **Basisbeginselen** te voltooien:
   
   |Instelling  |Waarde  |
   |---------|---------|
   |Beleid voor     |Global WAF (Voordeur)|
   |Abonnement     |Selecteer uw abonnement|
   |Resourcegroep     |Selecteer de resourcegroep waar uw voordeur zich bevindt.|
   |Beleidsnaam     |Een naam voor uw beleid typen|
   |Beleidsstatus     |Ingeschakeld|

   **Volgende selecteren: beleidsinstellingen**

1. Selecteer op het tabblad **Beleidsinstellingen** de optie **Preventie**. Voor de **block response body,** type *Je bent geblokkeerd!* zodat u zien dat uw aangepaste regel van kracht is.
2. Selecteer **Volgende: Beheerde regels**.
3. Selecteer **Volgende: Aangepaste regels**.
4. Selecteer **Aangepaste regel toevoegen**.
5. Gebruik **op** de pagina Aangepaste regel toevoegen de volgende testwaarden om een aangepaste regel te maken:

   |Instelling  |Waarde  |
   |---------|---------|
   |Aangepaste regelnaam     |FdWafCustRegel|
   |Status     |Ingeschakeld|
   |Regeltype     |Match|
   |Prioriteit    |100|
   |Type overeenkomen     |IP-adres|
   |Overeenkomen met variabele|RemoteAddr (RemoteAddr)|
   |Bewerking|Bevat niet|
   |IP-adres of -bereik|10.10.10.0/24|
   |Dan|Verkeer weigeren|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Aangepaste regel":::

   Selecteer **Toevoegen**.
6. Selecteer **Volgende: Koppeling**.
7. Selecteer **Frontendhost toevoegen**.
8. Selecteer **voor Frontend-host**uw frontendhost en selecteer **Toevoegen**.
9. Selecteer **Controleren + maken**.
10. Nadat uw beleidsvalidatie is doorgegeven, selecteert u **Maken**.

### <a name="test-your-waf-policy"></a>Test uw WAF-beleid

1. Nadat de implementatie van uw WAF-beleid is voltooid, bladert u naar de naam van de front-deurhost.
2. Je moet je aangepaste blokbericht zien.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="WAF-regeltest":::

   > [!NOTE]
   > Een privé-IP-adres is opzettelijk gebruikt in de aangepaste regel om te garanderen dat de regel zou worden geactiveerd. Maak in een daadwerkelijke implementatie *toestaan* en *weigeren* regels met BEHULP van IP-adressen voor uw specifieke situatie.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Een WAF-beleid configureren met de Azure CLI

### <a name="prerequisites"></a>Vereisten
Voordat u begint met het configureren van een IP-beperkingsbeleid, stelt u uw CLI-omgeving in en maakt u een Azure Front Door-profiel.

#### <a name="set-up-the-azure-cli-environment"></a>De Azure CLI-omgeving instellen
1. Installeer de [Azure CLI](/cli/azure/install-azure-cli)of gebruik Azure Cloud Shell. Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. In deze shell is de Azure CLI vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Selecteer de knop **Probeer het** in de CLI-opdrachten die volgen en meld u aan bij uw Azure-account in de Cloud Shell-sessie die wordt geopend. Nadat de sessie `az extension add --name front-door` is gestart, voert u de azure front door-extensie toe.
 2. Als u de CLI lokaal in Bash gebruikt, meldt `az login`u zich aan bij Azure met behulp van .

#### <a name="create-an-azure-front-door-profile"></a>Een Azure Front Door-profiel maken
Maak een Azure Front Door-profiel door de instructies te volgen die in [Quickstart zijn beschreven: Een voordeur maken voor een zeer beschikbare wereldwijde webtoepassing.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Een WAF-beleid maken

Maak een WAF-beleid met behulp van de [az-netwerk voordeur waf-beleid maken](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) commando. Vervang in het volgende voorbeeld de beleidsnaam *IPAllowPolicyExampleCLI* door een unieke beleidsnaam.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Een aangepaste IP-toegangsbeheerregel toevoegen

Gebruik de aangepaste opdracht voor het maken van de [custom-rule van het AZ-netwerk voor het waf-beleid](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) om een aangepaste IP-toegangscontroleregel toe te voegen voor het WAF-beleid dat u zojuist hebt gemaakt.

In de volgende voorbeelden:
-  Vervang *IPAllowPolicyExampleCLI* door uw unieke beleid dat eerder is gemaakt.
-  Vervang *ip-adres-range-1,* *ip-adres-range-2* door uw eigen assortiment.

Maak eerst een IP-regel voor toestaan voor het beleid dat is gemaakt met de vorige stap. 
> [!NOTE]
> **--uitstel** is vereist omdat een regel een overeenkomende voorwaarde moet hebben die in de volgende stap moet worden toegevoegd.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Voeg vervolgens de overeenkomstvoorwaarde toe aan de regel:

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>De id van een WAF-beleid zoeken 
Zoek een WAF-beleids-ID met behulp van de [az-netwerk voordeur waf-policy show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) commando. Vervang *IPAllowPolicyExampleCLI* in het volgende voorbeeld door uw unieke beleid dat u eerder hebt gemaakt.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Een WAF-beleid koppelen aan een Azure Front Door-front-endhost

Stel de Azure Front Door *WebApplicationFirewallPolicyLink* ID in op de beleids-id met de opdracht voordeurupdate van [het AZ-netwerk.](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) Vervang *IPAllowPolicyExampleCLI* door uw unieke beleid dat u eerder hebt gemaakt.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
In dit voorbeeld wordt het WAF-beleid toegepast op **FrontendEndpoints[0]**. U het WAF-beleid koppelen aan een van uw front-ends.
> [!Note]
> U hoeft de eigenschap **WebApplicationFirewallPolicyLink** slechts één keer in te stellen om een WAF-beleid te koppelen aan een Azure Front Door-front-end. Volgende beleidsupdates worden automatisch toegepast op de front-end.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Een WAF-beleid configureren met Azure PowerShell

### <a name="prerequisites"></a>Vereisten
Voordat u begint met het configureren van een IP-beperkingsbeleid, stelt u uw PowerShell-omgeving in en maakt u een Azure Front Door-profiel.

#### <a name="set-up-your-powershell-environment"></a>Uw PowerShell-omgeving instellen
Azure PowerShell biedt een set cmdlets die het [Azure Resource Manager-model](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) gebruiken voor het beheer van Azure-resources.

U kunt [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) op uw lokale computer installeren en in elke PowerShell-sessie gebruiken. Volg de instructies op de pagina om u aan te melden bij PowerShell met behulp van uw Azure-referenties en installeer vervolgens de Az-module.

1. Maak verbinding met Azure met de volgende opdracht en gebruik vervolgens een interactief dialoogvenster om u aan te melden.
    ```
    Connect-AzAccount
    ```
 2. Voordat u een Azure Front Door-module installeert, moet u ervoor zorgen dat de huidige versie van de PowerShellGet-module is geïnstalleerd. Voer de volgende opdracht uit en open PowerShell opnieuw.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installeer de Az.FrontDoor-module met behulp van de volgende opdracht. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Een Azure Front Door-profiel maken
Maak een Azure Front Door-profiel door de instructies te volgen die in [Quickstart zijn beschreven: Een voordeur maken voor een zeer beschikbare wereldwijde webtoepassing.](../../frontdoor/quickstart-create-front-door.md)

### <a name="define-an-ip-match-condition"></a>Een IP-overeenkomstvoorwaarde definiëren
Gebruik de opdracht [Nieuw-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) om een IP-matchvoorwaarde te definiëren.
In het volgende voorbeeld vervangt u *ip-adres-bereik-1,* *ip-adres-bereik-2* door uw eigen bereik.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Een aangepaste IP-regel maken

Gebruik de opdracht [Nieuw-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) om een actie te definiëren en een prioriteit in te stellen. In het volgende voorbeeld worden aanvragen die niet van ip-client-IP's die overeenkomen met de lijst geblokkeerd.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Een WAF-beleid configureren
Zoek de naam van de brongroep die het `Get-AzResourceGroup`Azure Front Door-profiel bevat met behulp van . Configureer vervolgens een WAF-beleid met de [IP-regel met nieuw-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Een WAF-beleid koppelen aan een Azure Front Door-front-endhost

Koppel een WAF-beleidsobject aan een bestaande front-endhost en werk Azure Front Door-eigenschappen bij. Haal eerst het azure front door-object op met [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Stel vervolgens de eigenschap **WebApplicationFirewallPolicyLink** in op de bron-id van *$IPAllowPolicyExamplePS*, die in de vorige stap is gemaakt, met de opdracht [AzFrontDoor instellen.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> In dit voorbeeld wordt het WAF-beleid toegepast op **FrontendEndpoints[0]**. U een WAF-beleid koppelen aan een van uw front-ends. U hoeft de eigenschap **WebApplicationFirewallPolicyLink** slechts één keer in te stellen om een WAF-beleid te koppelen aan een Azure Front Door-front-end. Volgende beleidsupdates worden automatisch toegepast op de front-end.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Een WAF-beleid configureren met een sjabloon Resourcemanager
Als u de sjabloon wilt weergeven waarmee een Azure Front Door-beleid en een WAF-beleid met aangepaste IP-beperkingsregels zijn gemaakt, gaat u naar [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een Azure Front Door-profiel](../../frontdoor/quickstart-create-front-door.md).
