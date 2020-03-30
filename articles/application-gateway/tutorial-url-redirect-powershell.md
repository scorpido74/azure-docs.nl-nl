---
title: Op URL-pad gebaseerde omleiding met PowerShell - Azure Application Gateway
description: Informatie over het maken van een toepassingsgateway met op een URL-pad gebaseerd, omgeleid verkeer met behulp van Azure PowerShell.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 03/19/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: c577859f6e8a44dd3573537aecadba638a5f6fa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059389"
---
# <a name="create-an-application-gateway-with-url-path-based-redirection-using-azure-powershell"></a>Een toepassingsgateway maken met een omleiding op basis van een URL-pad met Azure PowerShell

U kunt Azure PowerShell gebruiken om [op een URL gebaseerde routeringsregels](application-gateway-url-route-overview.md) te configureren als u een [toepassingsgateway maakt](application-gateway-introduction.md). In dit artikel maakt u backendpools met [virtuele machineschaalsets](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md). Vervolgens maakt u URL-routeringsregels waardoor webverkeer wordt omgeleid naar de juiste back-endpool.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Netwerk instellen
> * Een toepassingsgateway maken
> * Listeners en routeringsregels toevoegen
> * Schaalsets voor virtuele machines voor back-endpools maken

Het volgende voorbeeld toont siteverkeer afkomstig van de poorten 8080 en 8081 en dat wordt doorgestuurd naar dezelfde back-endpools:

![Voorbeeld van URL-routering](./media/tutorial-url-redirect-powershell/scenario.png)

Als u dat liever hebt, u deze procedure voltooien met [Azure CLI](tutorial-url-redirect-cli.md).

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de PowerShell lokaal te installeren en te gebruiken, vereist deze procedure de Azure PowerShell-module versie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om de versie te vinden. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Maak een Azure-brongroep met [Nieuwe AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Netwerkbronnen maken

Maak de subnetconfiguraties voor *myBackendSubnet* en *myAGSubnet* met [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Maak het virtuele netwerk *myVNet* met behulp van [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) met de subnetconfiguraties. En ten slotte, maak het openbare IP-adres genaamd *myAGPublicIPAddress* met behulp van [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Deze resources worden gebruikt om de netwerkverbinding naar de toepassingsgateway en de bijbehorende bronnen te leveren.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig

New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

In deze sectie kunt u resources maken die ondersteuning bieden voor de toepassingsgateway. Ten slotte maakt u deze. De resources die u maakt, zijn onder andere:

- *IP-configuraties en front-endpoort*: hiermee koppelt u het subnet dat u eerder hebt gemaakt aan de toepassingsgateway en wijst u een poort toe die u gebruikt om de gateway te openen.
- *Standaardpool*: alle toepassingsgateways moeten ten minste één back-endpool met servers hebben.
- *Standaard-listener en regel*: de standaard-listener luistert naar verkeer op de poort die is toegewezen en de standaardregel verzendt verkeer naar de standaardpool.

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP-configuraties en front-endpoort maken

*MyAGSubnet* koppelen dat u eerder hebt gemaakt met de toepassingsgateway met [behulp van New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Wijs *myAGPublicIPAddress* toe aan de toepassingsgateway met [nieuwe-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig). En dan u de HTTP-poort maken met behulp van [New-AzApplicationGatewayFrontendPort.](/powershell/module/az.network/new-azapplicationgatewayfrontendport)

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$subnet=$vnet.Subnets[0]

$pip = Get-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Name myAGPublicIPAddress

$gipconfig = New-AzApplicationGatewayIPConfiguration `
  -Name myAGIPConfig `
  -Subnet $subnet

$fipconfig = New-AzApplicationGatewayFrontendIPConfig `
  -Name myAGFrontendIPConfig `
  -PublicIPAddress $pip

$frontendport = New-AzApplicationGatewayFrontendPort `
  -Name myFrontendPort `
  -Port 80
```

### <a name="create-the-default-pool-and-settings"></a>De standaardpool en instellingen maken

Maak de standaard backendpool met de naam *appGatewayBackendPool* voor de toepassingsgateway met [Nieuw-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Configureer de instellingen voor de backendpool met [Nieuwe-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSettings `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Standaardlistener en -regel maken

Een listener is vereist om de toepassingsgateway in te schakelen om het verkeer op de juiste manier naar een back-endpool te routeren. In dit artikel maakt u meerdere listeners. De eerste basis-listener verwacht verkeer op de basis-URL. De andere listeners verwachten verkeer op specifieke URL's, zoals `http://52.168.55.24:8080/images/` of `http://52.168.55.24:8081/video/`.

Maak een listener met de naam *defaultListener* met [Nieuw-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) met de frontend-configuratie en frontend-poort die u eerder hebt gemaakt. Er is een regel vereist, zodat de listener weet welke back-endpool moet worden gebruikt voor binnenkomend verkeer. Maak een basisregel met de naam *Rule1* met [nieuwe azApplicationGateway-routeregel](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name defaultListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport

$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $defaultPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>De toepassingsgateway maken

Nu u de benodigde ondersteunende bronnen hebt gemaakt, geeft u parameters op voor de toepassingsgateway met de naam *myAppGateway* met behulp van [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku)en maakt u deze vervolgens met [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway).

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_Medium `
  -Tier Standard `
  -Capacity 2

New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $defaultPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="add-backend-pools-and-ports"></a>Back-endpools en back-endpoorten toevoegen

U backendpools toevoegen aan uw toepassingsgateway met [Add-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/add-azapplicationgatewaybackendaddresspool). In dit voorbeeld worden *imagesBackendPool* en *videoBackendPool* gemaakt. U voegt de frontendpoort voor de zwembaden toe met [Add-AzApplicationGatewayFrontendPort.](/powershell/module/az.network/add-azapplicationgatewayfrontendport) Vervolgens dient u de wijzigingen in de toepassingsgateway in met [set-AzApplicationGateway.](/powershell/module/az.network/set-azapplicationgateway)

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

Add-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

Add-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport `
  -Port 8080

Add-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name rport `
  -Port 8081

Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="add-listeners-and-rules"></a>Listeners en regels toevoegen

### <a name="add-listeners"></a>Listeners toevoegen

Voeg de listeners met de naam *backendListener* en *omgeleid Listener* toe die nodig zijn om verkeer te routeren met [Add-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPort = Get-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport

$redirectPort = Get-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name rport

$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw

Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort

Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name redirectedListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $redirectPort

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-default-url-path-map"></a>De standaardtoewijzing voor het URL-pad toevoegen

URL-padtoewijzingen zorgen ervoor dat specifieke URL's naar specifieke back-endpools worden omgeleid. U de URL-padkaarten met de naam *imagePathRule* en *videoPathRule* maken met [New-AzApplicationGatewayPathConfig](/powershell/module/az.network/new-azapplicationgatewaypathruleconfig) en [Add-AzApplicationGatewayUrlPathMapConfig](/powershell/module/az.network/add-azapplicationgatewayurlpathmapconfig).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $appgw `
  -Name myPoolSettings

$imagePool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name imagesBackendPool

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name videoBackendPool

$defaultPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool

$imagePathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name imagePathRule `
  -Paths "/images/*" `
  -BackendAddressPool $imagePool `
  -BackendHttpSettings $poolSettings

$videoPathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name videoPathRule `
  -Paths "/video/*" `
  -BackendAddressPool $videoPool `
  -BackendHttpSettings $poolSettings

Add-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap `
  -PathRules $imagePathRule, $videoPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-redirection-configuration"></a>Omleidingsconfiguratie toevoegen

U omleiding voor de listener configureren met [Add-AzApplicationRedirectConfiguration](/powershell/module/az.network/add-azapplicationgatewayredirectconfiguration).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendListener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$redirectConfig = Add-AzApplicationGatewayRedirectConfiguration `
  -ApplicationGateway $appgw `
  -Name redirectConfig `
  -RedirectType Found `
  -TargetListener $backendListener `
  -IncludePath $true `
  -IncludeQueryString $true

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-the-redirection-url-path-map"></a>Toewijzing voor het omleidings-URL-pad toevoegen

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzApplicationGatewayBackendHttpSettings `
  -ApplicationGateway $appgw `
  -Name myPoolSettings

$defaultPool = Get-AzApplicationGatewayBackendAddressPool `
  -ApplicationGateway $appgw `
  -Name appGatewayBackendPool

$redirectConfig = Get-AzApplicationGatewayRedirectConfiguration `
  -ApplicationGateway $appgw `
  -Name redirectConfig

$redirectPathRule = New-AzApplicationGatewayPathRuleConfig `
  -Name redirectPathRule `
  -Paths "/images/*" `
  -RedirectConfiguration $redirectConfig

Add-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name redirectpathmap `
  -PathRules $redirectPathRule `
  -DefaultBackendAddressPool $defaultPool `
  -DefaultBackendHttpSettings $poolSettings

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-routing-rules"></a>Routeringsregels toevoegen

De routeringsregels koppelen de URL-toewijzingen aan de listeners die u hebt gemaakt. U de regels met de naam *defaultRule* en *redirectedRule* toevoegen met [Add-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule).


```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendlistener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$redirectlistener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name redirectedListener

$urlPathMap = Get-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap

$redirectPathMap = Get-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name redirectpathmap

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name defaultRule `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name redirectedRule `
  -RuleType PathBasedRouting `
  -HttpListener $redirectlistener `
  -UrlPathMap $redirectPathMap

Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>Schaalsets voor virtuele machines maken

In dit voorbeeld maakt u drie virtuele-machineschaalsets die ondersteuning bieden voor de drie back-endpools die u hebt gemaakt. De schaalsets die u maakt, hebben de namen *myvmss1*, *myvmss2* en *myvmss3*. Elke schaalset bevat twee exemplaren van virtuele machines waarop u IIS installeert. U wijst de schaalset toe aan de back-endpool wanneer u de IP-instellingen configureert.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Name myVNet

$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool `
  -ApplicationGateway $appgw

$imagesPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name imagesBackendPool `
  -ApplicationGateway $appgw

$videoPool = Get-AzApplicationGatewayBackendAddressPool `
  -Name videoBackendPool `
  -ApplicationGateway $appgw

for ($i=1; $i -le 3; $i++)
{
  if ($i -eq 1)
  {
     $poolId = $backendPool.Id
  }
  if ($i -eq 2) 
  {
    $poolId = $imagesPool.Id
  }
  if ($i -eq 3)
  {
    $poolId = $videoPool.Id
  }

  $ipConfig = New-AzVmssIpConfig `
    -Name myVmssIPConfig$i `
    -SubnetId $vnet.Subnets[1].Id `
    -ApplicationGatewayBackendAddressPoolsId $poolId

  $vmssConfig = New-AzVmssConfig `
    -Location eastus `
    -SkuCapacity 2 `
    -SkuName Standard_DS2 `
    -UpgradePolicyMode Automatic

  Set-AzVmssStorageProfile $vmssConfig `
    -ImageReferencePublisher MicrosoftWindowsServer `
    -ImageReferenceOffer WindowsServer `
    -ImageReferenceSku 2016-Datacenter `
    -ImageReferenceVersion latest `
    -OsDiskCreateOption FromImage

  Set-AzVmssOsProfile $vmssConfig `
    -AdminUsername azureuser `
    -AdminPassword "Azure123456!" `
    -ComputerNamePrefix myvmss$i

  Add-AzVmssNetworkInterfaceConfiguration `
    -VirtualMachineScaleSet $vmssConfig `
    -Name myVmssNetConfig$i `
    -Primary $true `
    -IPConfiguration $ipConfig

  New-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmssConfig
}
```

### <a name="install-iis"></a>IIS installeren

```azurepowershell-interactive
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

for ($i=1; $i -le 3; $i++)
{
  $vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss$i

  Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

  Update-AzVmss `
    -ResourceGroupName myResourceGroupAG `
    -Name myvmss$i `
    -VirtualMachineScaleSet $vmss
}
```

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

U [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) gebruiken om het openbare IP-adres van de toepassingsgateway te krijgen. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. Zoals, `http://52.168.55.24`, `http://52.168.55.24:8080/images/test.htm` `http://52.168.55.24:8080/video/test.htm`, `http://52.168.55.24:8081/images/test.htm`of .

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Basis-URL testen in de toepassingsgateway](./media/tutorial-url-redirect-powershell/application-gateway-iistest.png)

Wijzig de URL in http://&lt;IP-adres&gt;: 8080/images/test.htm, waarbij u &lt;IP-adres&gt; vervangt door uw IP-adres. U ziet dan iets als het volgende voorbeeld:

![Afbeeldingen-URL in toepassingsgateway testen](./media/tutorial-url-redirect-powershell/application-gateway-iistest-images.png)

Wijzig de URL in http://&lt;IP-adres&gt;: 8080/video/test.htm, waarbij u &lt;IP-adres&gt; vervangt door uw IP-adres. U ziet dan iets als het volgende voorbeeld:

![Video-URL testen in de toepassingsgateway](./media/tutorial-url-redirect-powershell/application-gateway-iistest-video.png)

Wijzig nu de URL in http://&lt;IP-adres&gt;:8081/images/test.htm, waarbij u &lt;IP-adres&gt; vervangt door uw eigen IP-adres. U ziet dat het verkeer terug wordt omgeleid naar de back-endpool met afbeeldingen op http://&lt;IP-adres&gt;:8080/images.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de brongroep, de toepassingsgateway en alle gerelateerde resources wanneer deze niet meer nodig zijn met [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```
## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over wat u kunt doen met de toepassingsgateway](application-gateway-introduction.md)
