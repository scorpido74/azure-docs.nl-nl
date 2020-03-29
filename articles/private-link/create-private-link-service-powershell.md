---
title: Een Azure-privékoppelingsservice maken met Azure PowerShell| Microsoft Documenten
description: Meer informatie over het maken van een Azure private link-service met Azure PowerShell
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: article
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 225ae9d07cc6df2fa809e250083ee6007ab2f945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932086"
---
# <a name="create-a-private-link-service-using-azure-powershell"></a>Een Private Link-service maken met Azure PowerShell
In dit artikel ziet u hoe u een privékoppelingsservice maakt in Azure met Azure PowerShell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest PowerShell lokaal te installeren en te gebruiken, vereist dit artikel de nieuwste Azure PowerShell-moduleversie. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Voordat u uw privékoppeling maken, moet u een resourcegroep maken met [Nieuw-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de locatie *WestCentralUS* ge:

```azurepowershell
$location = "westcentralus"
$rgName = "myResourceGroup"
New-AzResourceGroup `
  -ResourceGroupName $rgName `
  -Location $location
```
## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
Maak een virtueel netwerk voor uw privéverbinding met [New-AzVirtualNetwork.](/powershell/module/az.network/new-azvirtualnetwork) In het volgende voorbeeld wordt een virtueel netwerk met de naam *myvnet* met subnet voor*frontend (frontendSubnet),* backend *(backendSubnet),* privékoppeling *(otherSubnet):*

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
## <a name="create-internal-load-balancer"></a>Interne load balancer maken
Maak een interne standaard load balancer met [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer). In het volgende voorbeeld wordt een interne standaardloadbalancer gemaakt met behulp van de IP-configuratie, de sonde, de regel en de backendpool die u in de voorgaande stappen hebt gemaakt:

```azurepowershell

$lbBackendName = "LB-backend" 
$lbFrontName = "LB-frontend" 
$lbName = "lb"
 
#Create Internal Load Balancer
$frontendIP = New-AzLoadBalancerFrontendIpConfig -Name $lbFrontName -PrivateIpAddress 10.0.1.5 -SubnetId $vnet.subnets[0].Id 
$beaddresspool= New-AzLoadBalancerBackendAddressPoolConfig -Name $lbBackendName 
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
$rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beaddresspool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
$NRPLB = New-AzLoadBalancer -ResourceGroupName $rgName -Name $lbName -Location $location -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $probe -LoadBalancingRule $rule -Sku Standard 
```
## <a name="create-a-private-link-service"></a>Een privékoppelingsservice maken
Maak een private link service met [New-AzPrivateLinkService](/powershell/module/az.network/new-azloadbalancer).  In dit voorbeeld wordt een privékoppelingsservice met de naam *myPLS* gemaakt met behulp van Standard Load Balancer in resourcegroep *myResourceGroup*. 
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

### <a name="get-private-link-service"></a>Privékoppelingsservice opdoen
Als volgt meer informatie over uw privélinkservice met [Get-AzPrivateLinkService:](/powershell/module/az.network/get-azprivatelinkservice)

```azurepowershell
$pls = Get-AzPrivateLinkService -Name $plsName -ResourceGroupName $rgName 
```

In dit stadium wordt uw Private Link Service gemaakt en is u klaar om het verkeer te ontvangen. Houd er rekening mee dat het bovenstaande voorbeeld alleen is om aan te tonen dat u Een Private Link-service maakt met PowerShell.  We hebben de backendpools van de load balancer of een toepassing op de backendpools niet geconfigureerd om naar het verkeer te luisteren. Als u end-to-end verkeersstromen wilt zien, wordt u ten zeerste aangeraden om uw toepassing achter uw standaard load balancer te configureren. 

Vervolgens zullen we laten zien hoe we deze service in kaart brengen naar een privé eindpunt in verschillende VNet met PowerShell. Nogmaals, het voorbeeld is beperkt tot het maken van het privéeindpunt en het maken van verbinding met private linkservice die hierboven is gemaakt. U virtuele machines in het virtuele netwerk maken om verkeer naar het privéeindpunt te verzenden/ontvangen voor het bouwen van uw scenario. 

## <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken
### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
Maak een virtueel netwerk voor uw privéeindpunt met [New-AzVirtualNetwork.](/powershell/module/az.network/new-azvirtualnetwork) In dit voorbeeld wordt een virtueel netwerk met de naam *vnetPE* in resourcegroep met de naam *myResourceGroup:*
 
```azurepowershell
$virtualNetworkNamePE = "vnetPE"
 
# Create VNet for private endpoint
$peSubnet = New-AzVirtualNetworkSubnetConfig `
-Name peSubnet `
-AddressPrefix "11.0.1.0/24" `
-PrivateEndpointNetworkPolicies "Disabled" 

$vnetPE = New-AzVirtualNetwork `
-Name $virtualNetworkNamePE `
-ResourceGroupName $rgName `
-Location $location `
-AddressPrefix "11.0.0.0/16" `
-Subnet $peSubnet 
```

### <a name="create-a-private-endpoint"></a>Een privé-eindpunt maken
Maak een privéeindpunt voor het consumeren van een privékoppelingsservice die hierboven in uw virtuele netwerk is gemaakt:
 
```azurepowershell
 
$plsConnection= New-AzPrivateLinkServiceConnection `
-Name plsConnection `
-PrivateLinkServiceId  $privateLinkService.Id  

$privateEndpoint = New-AzPrivateEndpoint -ResourceGroupName $rgName -Name $peName -Location $location -Subnet $vnetPE.subnets[0] -PrivateLinkServiceConnection $plsConnection -ByManualRequest 
```
 
### <a name="get-private-endpoint"></a>Privéeindpunt krijgen
Haal het IP-adres van `Get-AzPrivateEndpoint` het privéeindpunt als volgt op:

```azurepowershell
# Get Private Endpoint and its IP Address 
$pe =  Get-AzPrivateEndpoint `
-Name $peName `
-ResourceGroupName $rgName  `
-ExpandResource networkinterfaces

$pe.NetworkInterfaces[0].IpConfigurations[0].PrivateIpAddress 

```

### <a name="approve-the-private-endpoint-connection"></a>De privé-eindpuntverbinding goedkeuren
Keur de privé-eindpuntverbinding met de privékoppelingsservice goed met 'Goedkeuren-AzPrivateEndpointConnection'.

```azurepowershell   

$pls = Get-AzPrivateLinkService `
-Name $plsName `
-ResourceGroupName $rgName 

Approve-AzPrivateEndpointConnection -ResourceId $pls.PrivateEndpointConnections[0].Id -Description "Approved" 

``` 

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Azure private link](private-link-overview.md)
 
