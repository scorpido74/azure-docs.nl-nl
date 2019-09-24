---
title: Een persoonlijke Azure-koppelings service maken met behulp van Azure PowerShell | Microsoft Docs
description: Meer informatie over het maken van een persoonlijke Azure-koppelings service met Azure PowerShell
services: private-link
author: KumudD
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: kumud
ms.openlocfilehash: 8ed3b8e507a93f75b036b3a97eb34395ce525314
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202934"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Een persoonlijke koppelings service maken met behulp van Azure PowerShell
In dit artikel wordt beschreven hoe u een persoonlijke koppelings service maakt in azure met behulp van Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u Power shell lokaal wilt installeren en gebruiken, is voor dit artikel de nieuwste versie van Azure PowerShell module vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u een persoonlijke koppeling kunt maken, moet u een resource groep maken met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voor beeld wordt een resource groep met de naam *myResourceGroup* gemaakt op de locatie *WestCentralUS* :

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
Maak een virtueel netwerk voor uw persoonlijke koppeling met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In het volgende voor beeld wordt een virtueel netwerk gemaakt met de naam *myvnet* met subnet voor frontend (*frontendSubnet*), back-end (*backendSubnet*), persoonlijke koppeling (*otherSubnet*):

```azurepowershell
$virtualNetworkName = "myvnet"


# Create subnet config

$frontendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name frontendSubnet `
-AddressPrefix "10.0.1.0/24"  

$backendSubnet = New-AzVirtualNetworkSubnetConfig `
-Name backendSubnet `
-AddressPrefix "10.0.2.0/24"  

$otherSubnet = New-AzVirtualNetworkSubnetConfig `
-Name otherSubnet `
-AddressPrefix "10.0.3.0/24" `
-PrivateLinkServiceNetworkPolicies "Disabled" 

# Create the virtual network
$vnet = New-AzVirtualNetwork `
-Name $virtualNetworkName `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "10.0.0.0/16" `
-Subnet $frontendSubnet,$backendSubnet,$otherSubnet 
```
## <a name="create-internal-load-balancer"></a>Interne Load Balancer maken
Maak een interne Standard Load Balancer met [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). In het volgende voor beeld wordt een interne Standard Load Balancer gemaakt met behulp van de front-end IP-configuratie, test, regel en back-end-pool die u in de voor gaande stappen hebt gemaakt:

```azurepowershell

$lbBackendName = "LB-backend" 
$lbFrontName = "LB-frontend" 
$lbName = "lb"
 
#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id 
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName 
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard 
```
## <a name="create-a-private-link-service"></a>Een persoonlijke koppelings service maken
Maak een persoonlijke koppelings service met [New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  In dit voor beeld wordt een persoonlijke koppelings service met de naam *myPLS* gemaakt met behulp van Standard Load Balancer in resource groep met de naam *myResourceGroup*. 
```azurepowershell

$plsIpConfigName = "PLS-ipconfig" 
$plsName = "pls"
$peName = "pe" 
  
$IPConfig = New-AzPrivateLinkServiceIpConfig `
-Name $plsIpConfigName `
-Subnet $vnet.subnets[2] `
-PrivateIpAddress 10.0.3.5 

$fe = Get-AzLoadBalancer -Name $lbName | Get-AzLoadBalancerFrontendIpConfig 

$privateLinkService = New-AzPrivateLinkService `
-ServiceName $plsName `
-ResourceGroupName $rgName `
-Location $location `
-LoadBalancerFrontendIpConfiguration $frontendIP `
-IpConfiguration $IPConfig 
```

### <a name="get-private-link-service"></a>Persoonlijke koppelings service ophalen
Ga als volgt te weten over uw persoonlijke koppelings service met [New-AzPrivateLinkService](/powershell/module/az.network/get-azprivatelinkservice) :

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

In deze fase is uw persoonlijke koppelings service gemaakt en is deze klaar om het verkeer te ontvangen. Het bovenstaande voor beeld is alleen om te demonstreren hoe u een persoonlijke koppelings service maakt met behulp van Power shell.  De load balancer back-end-Pools of een toepassing op de back-endservers voor het Luis teren naar het verkeer is niet geconfigureerd. Als u end-to-end verkeers stromen wilt zien, kunt u het beste uw toepassing configureren achter uw standaard load balancer. 

Vervolgens laten we zien hoe u deze service kunt toewijzen aan een persoonlijk eind punt in verschillende VNet met behulp van Power shell. Het voor beeld is beperkt tot het maken van het persoonlijke eind punt en het verbinden met de persoonlijke koppelings service die hierboven is gemaakt. U kunt Virtual Machines in de Virtual Network maken voor het verzenden/ontvangen van verkeer naar het persoonlijke eind punt voor het bouwen van uw scenario. 

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken
### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
Maak een virtueel netwerk voor uw persoonlijke eind punt met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In dit voor beeld wordt een virtueel netwerk gemaakt met de naam *vnetPE* in de resource groep met de naam *myResourceGroup*:
 
```azurepowershell
$virtualNetworkNamePE = "vnetPE"
 
# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled" 

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName myResourceGroup `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet 
```

### <a name="create-a-private-endpoint"></a>Een persoonlijk eind punt maken
Maak een persoonlijk eind punt voor het gebruik van de service voor persoonlijke koppelingen die hierboven in uw virtuele netwerk is gemaakt:
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>Persoonlijk eind punt ophalen
Haal het IP-adres van het persoonlijke eind `Get-AzPrivateEndpoint` punt op met de volgende:

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>De verbinding met het persoonlijke eind punt goed keuren
Keur de verbinding van het particuliere eind punt met de persoonlijke koppelings service goed met ' goed keuren-AzPrivateEndpointConnection '.

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [persoonlijke Azure-koppelingen](private-link-overview.md)
 
