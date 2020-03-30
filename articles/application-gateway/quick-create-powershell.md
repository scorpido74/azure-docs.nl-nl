---
title: 'Snelstart: direct webverkeer met PowerShell'
titleSuffix: Azure Application Gateway
description: Meer informatie over het gebruik van Azure PowerShell om een Azure Application Gateway te maken die webverkeer naar virtuele machines in een backendpool leidt.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 03/05/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: abb38dfc342c8ff692ed1a3a05376b5dcefe8a3d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78399567"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway-using-azure-powershell"></a>Snelstart: direct webverkeer met Azure Application Gateway met Azure PowerShell

In deze quickstart gebruikt u Azure PowerShell om een toepassingsgateway te maken. Dan test je het om ervoor te zorgen dat het goed werkt. 

De toepassingsgateway leidt het webverkeer van toepassingen naar specifieke bronnen in een backend-groep. U wijst luisteraars toe aan poorten, maakt regels en voegt resources toe aan een backendpool. Omwille van de eenvoud, dit artikel maakt gebruik van een eenvoudige setup met een openbare front-end IP, een fundamentele luisteraar om een enkele site host op de applicatie gateway, een basisaanvraag routing regel, en twee virtuele machines in de backend pool.

U deze quickstart ook voltooien met [Azure CLI](quick-create-cli.md) of de [Azure-portal.](quick-create-portal.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Azure PowerShell-versie 1.0.0 of hoger](/powershell/azure/install-az-ps) (als u Azure PowerShell lokaal uitvoert).

## <a name="connect-to-azure"></a>Verbinding maken met Azure

Voer verbinding met `Connect-AzAccount`Azure uit om verbinding te maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. U een bestaande resourcegroep gebruiken of een nieuwe groep maken.

Als u een nieuwe resourcegroep wilt maken, gebruikt u de `New-AzResourceGroup` cmdlet: 

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupAG -Location eastus
```
## <a name="create-network-resources"></a>Netwerkbronnen maken

Er is een virtueel netwerk nodig voor communicatie tussen de resources die u maakt.  Het subnet van de toepassingsgateway kan alleen bestaan uit toepassingsgateways. Andere resources zijn niet toegestaan.  U een nieuw subnet voor application gateway maken of een bestaand subnet gebruiken. In dit voorbeeld maakt u twee subnetten in dit voorbeeld: een voor de toepassingsgateway en een andere voor de backendservers. U het Frontend IP van de Application Gateway configureren als openbaar of privé volgens uw use case. In dit voorbeeld kiest u een openbaar frontend-ip.

1. Maak de subnetconfiguraties `New-AzVirtualNetworkSubnetConfig`met behulp van .
2. Maak het virtuele netwerk met de `New-AzVirtualNetwork`subnetconfiguraties met behulp van . 
3. Maak het openbare `New-AzPublicIpAddress`IP-adres met behulp van . 

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

1. Gebruik `New-AzApplicationGatewayIPConfiguration` de configuratie die het subnet dat u hebt gemaakt, koppelt aan de toepassingsgateway. 
2. Gebruik `New-AzApplicationGatewayFrontendIPConfig` de configuratie die het openbare IP-adres toewijst dat u eerder aan de toepassingsgateway hebt gemaakt. 
3. Gebruik `New-AzApplicationGatewayFrontendPort` om poort 80 toe te wijzen om toegang te krijgen tot de toepassingsgateway.

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

1. Gebruik `New-AzApplicationGatewayBackendAddressPool` de backendpool voor de toepassingsgateway. De backend pool is voorlopig leeg en terwijl u de backendserver NIC's in de volgende sectie maakt, voegt u ze toe aan de backendpool.
2. Configureer de instellingen voor `New-AzApplicationGatewayBackendHttpSetting`de backendpool met .

```azurepowershell-interactive
$address1 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic1
$address2 = Get-AzNetworkInterface -ResourceGroupName myResourceGroupAG -Name myNic2
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

1. Maak een `New-AzApplicationGatewayHttpListener` listener met de frontend-configuratie en frontend-poort die u eerder hebt gemaakt. 
2. Gebruik `New-AzApplicationGatewayRequestRoutingRule` om een regel met de naam regel te *maken1*. 

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

1. Met `New-AzApplicationGatewaySku` deze functie worden parameters voor de toepassingsgateway opgegeven.
2. Gebruiken `New-AzApplicationGateway` om de toepassingsgateway te maken.

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

Nu u de Application Gateway hebt gemaakt, maakt u de backend virtuele machines die de websites hosten. Backend kan bestaan uit NIC's, virtuele machineschaalsets, openbare IP's, interne IP's, volledig gekwalificeerde domeinnamen (FQDN) en back-ends met meerdere tenant's zoals Azure App Service. In dit voorbeeld maakt u twee virtuele machines die door Azure worden gebruikt als back-endservers voor de toepassingsgateway. U installeert ook IIS op de virtuele machines om te controleren of de toepassingsgateway in Azure is gemaakt.

#### <a name="create-two-virtual-machines"></a>Twee virtuele machines maken

1. Download de onlangs gemaakte backendpoolconfiguratie van Application Gateway met `Get-AzApplicationGatewayBackendAddressPool`.
2. Maak een netwerkinterface met `New-AzNetworkInterface`.
3. Maak een virtuele `New-AzVMConfig`machineconfiguratie met .
4. Maak de virtuele `New-AzVM`machine met .

Wanneer u het volgende codevoorbeeld uitvoert om de virtuele machines te maken, wordt u gevraagd om referenties. Voer *azureuser* in voor de gebruikersnaam en een wachtwoord:
    
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

1. Voer `Get-AzPublicIPAddress` uit om het openbare IP-adres van de toepassingsgateway te krijgen. 
2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser. Als u de browser vernieuwt, ziet u de naam van de virtuele machine. Een geldig antwoord controleert of de toepassingsgateway is gemaakt en kan met succes verbinding maken met de backend.

```azurepowershell-interactive
Get-AzPublicIPAddress -ResourceGroupName myResourceGroupAG -Name myAGPublicIPAddress
```

![Toepassingsgateway testen](./media/quick-create-powershell/application-gateway-iistest.png)


## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die u met de toepassingsgateway hebt gemaakt, niet meer nodig hebt, verwijdert u de brongroep. Wanneer u de brongroep verwijdert, verwijdert u ook de toepassingsgateway en alle bijbehorende bronnen. 

Als u de brongroep `Remove-AzResourceGroup` wilt verwijderen, roept u de cmdlet aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Webverkeer met een toepassingsgateway beheren met behulp van Azure PowerShell](./tutorial-manage-web-traffic-powershell.md)

