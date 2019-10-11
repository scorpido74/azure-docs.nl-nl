---
title: Aangepaste v2-regels voor Web Application Firewall configureren met behulp van Azure PowerShell
description: Meer informatie over het configureren van aangepaste v2-regels voor Web Application Firewall met behulp van Azure PowerShell
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/18/2019
ms.author: victorh
ms.openlocfilehash: 9e50b47e22f5760c213cd0cafad82ecca592dec8
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263741"
---
# <a name="configure-web-application-firewall-v2-custom-rules-by-using-azure-powershell"></a>Aangepaste v2-regels voor Web Application Firewall configureren met behulp van Azure PowerShell

<!--- If you make any changes to the PowerShell in this article, also make the change in the corresponding Sample file: azure-docs-powershell-samples/application-gateway/waf-rules/waf-custom-rules.ps1 --->

Met aangepaste regels kunt u uw eigen regels maken, die worden geëvalueerd voor elke aanvraag die wordt door gegeven door de Web Application Firewall (WAF). Deze regels bevatten een hogere prioriteit dan de rest van de regels in de beheerde regel sets. Als u een volledige aanpassing wilt toestaan, hebben de aangepaste regels een actie (toestaan of blok keren), een match-voor waarde en een operator.

In dit artikel maakt u een Azure-toepassing gateway WAF v2 die gebruikmaakt van een aangepaste regel. De aangepaste regel blokkeert het verkeer als de aanvraag header de *evilbot*van de gebruikers agent bevat.

Zie [aangepaste Web Application firewall regels maken en gebruiken](create-custom-waf-rules.md)als u meer voor beelden van aangepaste regels wilt weer geven.

Als u de Azure PowerShell-code in dit artikel wilt uitvoeren in één doorlopend script dat u kunt kopiëren, plakken en uitvoeren, raadpleegt u [Azure-toepassing gateway Power shell](powershell-samples.md)-voor beelden.

## <a name="prerequisites"></a>Vereisten
* [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* U hebt een Azure PowerShell module nodig. Als u ervoor kiest om Azure PowerShell lokaal te installeren en te gebruiken, is voor dit script Azure PowerShell module versie 2.1.0 of hoger vereist. Ga als volgt te werk:

  1. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps).
  2. Voer `Connect-AzAccount` uit om een verbinding met Azure tot stand te brengen.

## <a name="example-script"></a>Voorbeeldscript

### <a name="set-up-variables"></a>Variabelen instellen

Voer de volgende code uit:

```azurepowershell
$rgname = "CustomRulesTest"

$location = "East US"

$appgwName = "WAFCustomRules"
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

Voer de volgende code uit:

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
```

### <a name="create-a-virtual-network"></a>Maak een virtueel netwerk

Voer de volgende code uit:

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"

$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"

$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-vip"></a>Een statisch openbaar VIP maken

Voer de volgende code uit:

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-a-pool-and-front-end-port"></a>Een pool en een front-end-poort maken

Voer de volgende code uit:

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet

$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip

$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com

$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 80
```

### <a name="create-a-listener-http-setting-rule-and-autoscale"></a>Een listener, HTTP-instelling, regel en automatisch schalen maken

Voer de volgende code uit:

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled

$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool

$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3

$sku = New-AzApplicationGatewaySku -Name WAF_v2 -Tier WAF_v2
```

### <a name="create-the-custom-rule-and-apply-it-to-waf-policy"></a>Maak de aangepaste regel en pas deze toe op WAF-beleid

Voer de volgende code uit:

```azurepowershell
$variable = New-AzApplicationGatewayFirewallMatchVariable -VariableName RequestHeaders -Selector User-Agent

$condition = New-AzApplicationGatewayFirewallCondition -MatchVariable $variable -Operator Contains -MatchValue "evilbot" -Transform Lowercase -NegationCondition $False  

$rule = New-AzApplicationGatewayFirewallCustomRule -Name blockEvilBot -Priority 2 -RuleType MatchRule -MatchCondition $condition -Action Block

$wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name wafPolicy -ResourceGroup $rgname -Location $location -CustomRule $rule

$wafConfig = New-AzApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="create-an-application-gateway"></a>Een Application Gateway maken

Voer de volgende code uit:

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname `
   -Location $location -BackendAddressPools $pool `
   -BackendHttpSettingsCollection  $poolSetting01 `
   -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
   -FrontendPorts $fp01 -HttpListeners $listener01 `
   -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig `
   -WebApplicationFirewallConfig $wafConfig `
   -FirewallPolicy $wafPolicy
```

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over Web Application Firewall](waf-overview.md)
