---
title: 'Quick Start: direct webverkeer met Power shell'
titleSuffix: Azure Application Gateway
description: Meer informatie over het gebruik van Azure PowerShell voor het maken van een Azure-toepassing gateway waarmee webverkeer wordt doorgestuurd naar virtuele machines in een back-end-groep.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 04/15/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 3e1ca14d967b0e88ea7eb559fd9962a3824ff9b0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81406215"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Snelstartgids: webverkeer omleiden met Azure-toepassing gateway met behulp van Azure PowerShell

In deze Quick Start gebruikt u Azure PowerShell om een toepassings gateway te maken. Vervolgens test u de app om te controleren of deze correct werkt. 

De toepassings gateway stuurt webverkeer van toepassingen naar specifieke bronnen in een back-end-pool. U wijst listeners toe aan poorten, maakt regels en voegt resources toe aan een back-end-groep. Voor het gemak maakt dit artikel gebruik van een eenvoudige configuratie met een openbaar front-end-IP, een basis-listener voor het hosten van één site op de toepassings gateway, een basis regel voor aanvraag Routering en twee virtuele machines in de back-end-pool.

U kunt deze Snelstartgids ook volt ooien met behulp van [Azure cli](quick-create-cli.md) of de [Azure Portal](quick-create-portal.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Azure PowerShell versie 1.0.0 of hoger](/powershell/azure/install-az-ps) (als u Azure PowerShell lokaal uitvoert).

## <a name="connect-to-azure"></a>Verbinding maken met Azure

Voer uit `Connect-AzAccount`om verbinding te maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. U kunt een bestaande resource groep gebruiken of een nieuwe maken.

Als u een nieuwe resource groep wilt maken, `New-AzResourceGroup` gebruikt u de cmdlet: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Netwerkbronnen maken

Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt.  Het subnet van de toepassingsgateway kan alleen bestaan uit toepassingsgateways. Andere resources zijn niet toegestaan.  U kunt een nieuw subnet maken voor Application Gateway of een bestaande gebruiken. In dit voor beeld maakt u twee subnetten in dit voor beeld: een voor de toepassings gateway en een voor de back-endservers. U kunt het frontend-IP-adres van de Application Gateway zo configureren dat het openbaar of privé is volgens uw use-case. In dit voor beeld kiest u een openbaar frontend-IP.

1. Maak de subnet-configuraties `New-AzVirtualNetworkSubnetConfig`met behulp van.
2. Maak het virtuele netwerk met de subnet-configuraties `New-AzVirtualNetwork`met behulp van. 
3. Maak het open bare IP- `New-AzPublicIpAddress`adres met. 

```azurepowershell-interactive
$agSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myAGSubnet `
  -AddressPrefix 10.0.1.0/24
$backendSubnetConfig = New-AzVirtualNetworkSubnetConfig `
  -Name myBackendSubnet `
  -AddressPrefix 10.0.2.0/24
New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $agSubnetConfig, $backendSubnetConfig
New-AzPublicIpAddress `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -Name myAGPublicIPAddress `
  -AllocationMethod Static `
  -Sku Standard
```
## <a name="create-an-application-gateway"></a>Een toepassingsgateway maken

### <a name="create-the-ip-configurations-and-frontend-port"></a>IP-configuraties en front-endpoort maken

1. Gebruik `New-AzApplicationGatewayIPConfiguration` om de configuratie te maken die het subnet koppelt dat u hebt gemaakt met de Application Gateway. 
2. Gebruik `New-AzApplicationGatewayFrontendIPConfig` om de configuratie te maken waarmee het open bare IP-adres dat u eerder hebt gemaakt, wordt toegewezen aan de toepassings gateway. 
3. Gebruik `New-AzApplicationGatewayFrontendPort` om poort 80 toe te wijzen voor toegang tot de toepassings gateway.

```azurepowershell-interactive
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myAGSubnet
$pip    = Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress 
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

### <a name="create-the-backend-pool"></a>Back-endpool maken

1. Gebruiken `New-AzApplicationGatewayBackendAddressPool` om de back-end-pool voor de toepassings gateway te maken. De back-end-groep zal nu leeg zijn. Wanneer u de back-end-server Nic's in de volgende sectie maakt, voegt u deze toe aan de back-end-groep.
2. Configureer de instellingen voor de back-end `New-AzApplicationGatewayBackendHttpSetting`-groep met.

```azurepowershell-interactive
$backendPool = New-AzApplicationGatewayBackendAddressPool `
  -Name myAGBackendPool
$poolSettings = New-AzApplicationGatewayBackendHttpSetting `
  -Name myPoolSettings `
  -Port 80 `
  -Protocol Http `
  -CookieBasedAffinity Enabled `
  -RequestTimeout 30
```

### <a name="create-the-listener-and-add-a-rule"></a>Listener maken en regel toevoegen

Als u de toepassingsgateway wilt inschakelen om het verkeer op de juiste manier naar de back-endpool te routeren, is in Azure een listener vereist. In Azure is ook een regel vereist, zodat de listener weet welke back-endpool moet worden gebruikt voor binnenkomend verkeer. 

1. Maak een listener met behulp `New-AzApplicationGatewayHttpListener` van de front-end-configuratie en de frontend-poort die u eerder hebt gemaakt. 
2. Gebruiken `New-AzApplicationGatewayRequestRoutingRule` om een regel te maken met de naam *firewallregel1*. 

```azurepowershell-interactive
$defaultlistener = New-AzApplicationGatewayHttpListener `
  -Name myAGListener `
  -Protocol Http `
  -FrontendIPConfiguration $fipconfig `
  -FrontendPort $frontendport
$frontendRule = New-AzApplicationGatewayRequestRoutingRule `
  -Name rule1 `
  -RuleType Basic `
  -HttpListener $defaultlistener `
  -BackendAddressPool $backendPool `
  -BackendHttpSettings $poolSettings
```

### <a name="create-the-application-gateway"></a>De toepassingsgateway maken

Nu u de benodigde ondersteunende resources hebt gemaakt, kunt u de toepassingsgateway maken:

1. Gebruiken `New-AzApplicationGatewaySku` om para meters voor de toepassings gateway op te geven.
2. Gebruiken `New-AzApplicationGateway` om de toepassings gateway te maken.

```azurepowershell-interactive
$sku = New-AzApplicationGatewaySku `
  -Name Standard_v2 `
  -Tier Standard_v2 `
  -Capacity 2
New-AzApplicationGateway `
  -Name myAppGateway `
  -ResourceGroupName myResourceGroupAG `
  -Location eastus `
  -BackendAddressPools $backendPool `
  -BackendHttpSettingsCollection $poolSettings `
  -FrontendIpConfigurations $fipconfig `
  -GatewayIpConfigurations $gipconfig `
  -FrontendPorts $frontendport `
  -HttpListeners $defaultlistener `
  -RequestRoutingRules $frontendRule `
  -Sku $sku
```

### <a name="backend-servers"></a>Back-endservers

Nu u de Application Gateway hebt gemaakt, maakt u de virtuele machine van de back-end die als host fungeert voor de websites. Back-end kan bestaan uit Nic's, virtuele-machine schaal sets, open bare Ip's, interne Ip's, FQDN-namen (Fully Qualified Domain names) en back-ends met meerdere tenants, zoals Azure App Service. In dit voorbeeld maakt u twee virtuele machines die door Azure worden gebruikt als back-endservers voor de toepassingsgateway. U installeert ook IIS op de virtuele machines om te controleren of de toepassingsgateway in Azure is gemaakt.

#### <a name="create-two-virtual-machines"></a>Twee virtuele machines maken

1. De recent gemaakte Application Gateway back-end-groeps configuratie ophalen met `Get-AzApplicationGatewayBackendAddressPool`.
2. Maak een netwerk interface met `New-AzNetworkInterface`.
3. Maak een virtuele-machine configuratie `New-AzVMConfig`met.
4. Maak de virtuele machine met `New-AzVM`.

Wanneer u het volgende codevoorbeeld uitvoert om de virtuele machines te maken, wordt u gevraagd om referenties. Voer *azureuser* in als gebruikers naam en wacht woord:
    
```azurepowershell-interactive
$appgw = Get-AzApplicationGateway -ResourceGroupName myResourceGroupAG -Name myAppGateway
$backendPool = Get-AzApplicationGatewayBackendAddressPool -Name myAGBackendPool -ApplicationGateway $appgw
$vnet   = Get-AzVirtualNetwork -ResourceGroupName myResourceGroupAG -Name myVNet
$subnet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name myBackendSubnet
$cred = Get-Credential
for ($i=1; $i -le 2; $i++)
{
  $nic = New-AzNetworkInterface `
    -Name myNic$i `
    -ResourceGroupName myResourceGroupAG `
    -Location EastUS `
    -Subnet $subnet `
    -ApplicationGatewayBackendAddressPool $backendpool
  $vm = New-AzVMConfig `
    -VMName myVM$i `
    -VMSize Standard_DS2_v2
  Set-AzVMOperatingSystem `
    -VM $vm `
    -Windows `
    -ComputerName myVM$i `
    -Credential $cred
  Set-AzVMSourceImage `
    -VM $vm `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest
  Add-AzVMNetworkInterface `
    -VM $vm `
    -Id $nic.Id
  Set-AzVMBootDiagnostic `
    -VM $vm `
    -Disable
  New-AzVM -ResourceGroupName myResourceGroupAG -Location EastUS -VM $vm
  Set-AzVMExtension `
    -ResourceGroupName myResourceGroupAG `
    -ExtensionName IIS `
    -VMName myVM$i `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
    -Location EastUS
}
```

## <a name="test-the-application-gateway"></a>De toepassingsgateway testen

Het is niet nodig IIS te installeren om de toepassingsgateway te maken, maar u hebt het in deze quickstart geïnstalleerd om te controleren of het maken van de toepassingsgateway in Azure is geslaagd. Gebruik IIS om de toepassingsgateway te testen:

1. Voer `Get-AzPublicIPAddress` uit om het open bare IP-adres van de toepassings gateway op te halen. 
2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. Als u de browser vernieuwt, ziet u de naam van de virtuele machine. Een geldige reactie verifieert of de toepassings gateway is gemaakt en kan verbinding maken met de back-end.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Toepassingsgateway testen](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u hebt gemaakt met de Application Gateway niet meer nodig hebt, verwijdert u de resource groep. Wanneer u de resource groep verwijdert, verwijdert u ook de toepassings gateway en alle gerelateerde resources. 

Als u de resource groep wilt verwijderen, `Remove-AzResourceGroup` roept u de cmdlet aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Webverkeer met een toepassingsgateway beheren met behulp van Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)

