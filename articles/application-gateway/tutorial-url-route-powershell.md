---
title: Webverkeer routeren op basis van de URL - Azure PowerShell
description: Lees hoe u Azure PowerShell kunt gebruiken om webverkeer op basis van de URL te routeren naar specifieke schaalbare pools met servers.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/31/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 8ca5c411db5644182f7e87f1ee1c63f3cbc4d2e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73200398"
---
# <a name="route-web-traffic-based-on-the-url-using-azure-powershell"></a>Webverkeer routeren op basis van de URL met Azure PowerShell

U kunt Azure PowerShell gebruiken om webverkeersroutering te configureren naar specifieke schaalbare serverpools op basis van de URL die wordt gebruikt voor toegang tot uw toepassing. In dit artikel maakt u een [Azure Application Gateway](application-gateway-introduction.md) met drie backendpools met virtuele [machineschaalsets.](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) Elk van de back-endpools heeft een specifiek doel, zoals algemene gegevens, afbeeldingen en video's.  Door verkeer te routeren om pools te scheiden, zorgt u ervoor dat uw klanten de informatie krijgen die ze nodig hebben wanneer ze deze nodig hebben.

Als u verkeersroutering wilt inschakelen, maakt u [routeringsregels](application-gateway-url-route-overview.md) die worden toegewezen aan listeners die op bepaalde poorten luisteren om ervoor te zorgen dat webverkeer binnenkomt op de juiste servers in de pools.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Netwerk instellen
> * Listeners, een toewijzing van URL-pad en regels maken
> * Schaalbare back-endpools maken

![Voorbeeld van URL-routering](./media/tutorial-url-route-powershell/scenario.png)

Als u dat liever hebt, u deze procedure voltooien met [Azure CLI](tutorial-url-route-cli.md) of de [Azure-portal.](create-url-route-portal.md)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de PowerShell lokaal te installeren en te gebruiken, vereist dit artikel de Azure PowerShell-module versie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om de versie te vinden. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Login-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

Vanwege de tijd die nodig is om resources te maken, kan het tot 90 minuten duren voordat deze procedure is voltooid.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep die alle bronnen voor uw toepassing bevat. 

Maak een Azure-brongroep met [Nieuwe AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).  

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```

## <a name="create-network-resources"></a>Netwerkbronnen maken

Of u nu een bestaand virtueel netwerk hebt of een nieuw netwerk maakt, u moet ervoor zorgen dat dit netwerk een subnet bevat dat alleen wordt gebruikt voor toepassingsgateways. In dit artikel maakt u een subnet voor de toepassingsgateway en een subnet voor de schaalsets. U maakt een openbaar IP-adres voor toegang tot de resources in de toepassingsgateway.

Maak de subnetconfiguraties *myAGSubnet* en *myBackendSubnet* met [new-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). Maak het virtuele netwerk *myVNet* met behulp van [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) met de subnetconfiguraties. En ten slotte, maak het openbare IP-adres genaamd *myAGPublicIPAddress* met behulp van [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress). Deze resources worden gebruikt om de netwerkverbinding naar de toepassingsgateway en de bijbehorende bronnen te leveren.

```azurepowershell-interactive
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.1.0/24

$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.2.0/24

$vnet = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $backendSubnetConfig, $agSubnetConfig
$pip = New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```

## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

In deze sectie kunt u resources maken die ondersteuning bieden voor de toepassingsgateway. Ten slotte maakt u deze. De resources die u maakt, zijn onder andere:

- *IP-configuraties en frontend-poort* - Associeert het subnet dat u eerder hebt gemaakt aan de toepassingsgateway en wijst een poort toe om deze te gebruiken om toegang te krijgen.
- *Standaardpool*: alle toepassingsgateways moeten ten minste één back-endpool met servers hebben.
- *Standaard-listener en regel*: de standaard-listener luistert naar verkeer op de poort die is toegewezen en de standaardregel verzendt verkeer naar de standaardpool.

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP-configuraties en front-endpoort maken

Associëren *myAGSubnet* u eerder gemaakt om de toepassing gateway met behulp [van New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration). Wijs *myAGPublicIPAddress* toe aan de toepassingsgateway met [nieuwe-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig).

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

Maak de standaard backendpool met de naam *appGatewayBackendPool* voor de toepassingsgateway met [Nieuw-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool). Configureer de instellingen voor de backendpool met [Nieuw-AzApplicationGatewayBackendHttpSetting](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting).

```azurepowershell-interactive
$defaultPool = New-AzApplicationGatewayBackendAddressPool `
  -Name appGatewayBackendPool

$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 120
```

### <a name="create-the-default-listener-and-rule"></a>Standaardlistener en -regel maken

Een listener is vereist om de toepassingsgateway in te schakelen om het verkeer op de juiste manier naar de back-endpool door te sturen. In dit artikel maakt u twee listeners. De eerste basis-listener die u maakt, luistert naar verkeer op de basis-URL. De tweede listener die u maakt, luistert naar verkeer op specifieke URL's.

Maak de standaardlistener met de naam *myDefaultListener* met [Nieuw-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) met de frontend-configuratie en frontend-poort die u eerder hebt gemaakt. 

Er is een regel vereist, zodat de listener weet welke back-endpool moet worden gebruikt voor binnenkomend verkeer. Maak een basisregel met de naam *Rule1* met [nieuwe azApplicationGateway-routeregel](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myDefaultListener `
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
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2

$appgw = New-AzApplicationGateway `
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

Het kan tot 30 minuten duren voordat de toepassingsgateway is gemaakt. Wacht totdat de implementatie is voltooid voordat u doorgaat met de volgende sectie. 

Op dit punt hebt u een toepassingsgateway die luistert naar verkeer op poort 80 en dat verkeer naar een standaardservergroep verzendt.

### <a name="add-image-and-video-backend-pools-and-port"></a>Back-endpools en -poort voor afbeeldingen en video toevoegen

Backend pools met de naam *imagesBackendPool* en *videoBackendPool* toevoegen aan uw toepassingsgateway[Add-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/add-azapplicationgatewaybackendaddresspool). Voeg de frontendpoort voor de zwembaden toe met [Add-AzApplicationGatewayFrontendPort](/powershell/module/az.network/add-azapplicationgatewayfrontendport). Verzend de wijzigingen in de toepassingsgateway met [Set-AzApplicationGateway](/powershell/module/az.network/set-azapplicationgateway).

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

Set-AzApplicationGateway -ApplicationGateway $appgw
```

Het bijwerken van de toepassingsgateway kan ook tot 20 minuten duren.

### <a name="add-backend-listener"></a>Back-endlistener toevoegen

Voeg de backendlistener met de naam *backendListener* toe die nodig is om verkeer te routeren met [Add-AzApplicationGatewayHttpListener](/powershell/module/az.network/add-azapplicationgatewayhttplistener).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendPort = Get-AzApplicationGatewayFrontendPort `
  -ApplicationGateway $appgw `
  -Name bport

$fipconfig = Get-AzApplicationGatewayFrontendIPConfig `
  -ApplicationGateway $appgw

Add-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $backendPort

Set-AzApplicationGateway -ApplicationGateway $appgw
```

### <a name="add-url-path-map"></a>Toewijzing van URL-pad toevoegen

Toewijzingen van URL-paden zorgen ervoor dat de URL's die naar uw toepassing worden verzonden, worden gerouteerd naar specifieke back-endpools. URL-padkaarten met de naam *imagePathRule* en *videoPathRule* maken met [Nieuw-AzApplicationGatewayPathConfig](/powershell/module/az.network/new-azapplicationgatewaypathruleconfig) en [Add-AzApplicationGatewayUrlPathMapConfig](/powershell/module/az.network/add-azapplicationgatewayurlpathmapconfig).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$poolSettings = Get-AzApplicationGatewayBackendHttpSetting `
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

### <a name="add-routing-rule"></a>Routeringsregel toevoegen

Met de routeringsregel koppelt u de URL-toewijzing aan de listener die u hebt gemaakt. Voeg de regel met de naam *regeltoevoegen2* toe met [Add-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/add-azapplicationgatewayrequestroutingrule).

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName myResourceGroupAG `
  -Name myAppGateway

$backendlistener = Get-AzApplicationGatewayHttpListener `
  -ApplicationGateway $appgw `
  -Name backendListener

$urlPathMap = Get-AzApplicationGatewayUrlPathMapConfig `
  -ApplicationGateway $appgw `
  -Name urlpathmap

Add-AzApplicationGatewayRequestRoutingRule `
  -ApplicationGateway $appgw `
  -Name rule2 `
  -RuleType PathBasedRouting `
  -HttpListener $backendlistener `
  -UrlPathMap $urlPathMap

Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="create-virtual-machine-scale-sets"></a>Schaalsets voor virtuele machines maken

In dit voorbeeld maakt u drie virtuele-machineschaalsets die ondersteuning bieden voor de drie back-endpools die u hebt gemaakt. De schaalsets die u maakt, hebben de namen *myvmss1*, *myvmss2* en *myvmss3*. U wijst de schaalset toe aan de back-endpool wanneer u de IP-instellingen configureert.

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

Elke schaalset bevat twee exemplaren van virtuele machines waarop u IIS installeert.  Er wordt een voorbeeldpagina gemaakt om te testen of de toepassingsgateway werkt.

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

Gebruik [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress) om het openbare IP-adres van de toepassingsgateway te krijgen. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. Zoals, `http://52.168.55.24`, `http://52.168.55.24:8080/images/test.htm`of `http://52.168.55.24:8080/video/test.htm`.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Basis-URL testen in de toepassingsgateway](./media/tutorial-url-route-powershell/application-gateway-iistest.png)

Wijzig de URL&lt;naar&gt;http:// ip-adres:8080/images/test.htm, &lt;ter vervanging&gt;van uw IP-adres voor ip-adres en u ziet iets als het volgende voorbeeld:

![Afbeeldingen-URL in toepassingsgateway testen](./media/tutorial-url-route-powershell/application-gateway-iistest-images.png)

Wijzig de URL&lt;in&gt;http:// ip-adres:8080/video/test.htm, &lt;ter vervanging&gt;van uw IP-adres voor ip-adres en u ziet iets als het volgende voorbeeld:

![Video-URL testen in de toepassingsgateway](./media/tutorial-url-route-powershell/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de brongroep, de toepassingsgateway en alle gerelateerde resources wanneer deze niet meer nodig zijn met [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Volgende stappen

[Webverkeer omleiden op basis van de URL](./tutorial-url-redirect-powershell.md)
