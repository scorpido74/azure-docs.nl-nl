---
title: Een IPv4-toepassing upgraden naar IPv6 in Azure Virtual Network - PowerShell
titlesuffix: Azure Virtual Network
description: In dit artikel ziet u hoe u IPv6-adressen implementeert naar een bestaande toepassing in het virtuele Azure-netwerk met Azure Powershell.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/21/2019
ms.author: kumud
ms.openlocfilehash: d08ce1c382d173ac98a0e61e6117ed50b958ba44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76119819"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell-preview"></a>Een IPv4-toepassing upgraden naar IPv6 in het virtuele Azure-netwerk van Azure - PowerShell (Voorbeeld)

In dit artikel ziet u hoe u IPv6-connectiviteit toevoegt aan een bestaande IPv4-toepassing in een virtueel Azure-netwerk met een standaardloadbalansr en openbaar IP. De in-place upgrade omvat:
- IPv6-adresruimte voor het virtuele netwerk en subnet
- een standaard load balancer met zowel IPv4- als IPV6 frontend-configuraties
- VM's met NIC's met zowel een IPv4 + IPv6-configuratie
- IPv6 Public IP, zodat de load balancer ipv6-connectiviteit met internet heeft

> [!Important]
> IPv6-ondersteuning voor Azure Virtual Network is momenteel in openbare preview. Deze preview wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest PowerShell lokaal te installeren en te gebruiken, vereist dit artikel de Azure PowerShell-moduleversie 6.9.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="prerequisites"></a>Vereisten

### <a name="register-the-service"></a>De service registreren

Voordat u een dual stack-toepassing implementeert in Azure, moet u uw abonnement voor deze voorbeeldfunctie configureren met de volgende Azure PowerShell:

Registreer als volgt:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Het duurt maximaal 30 minuten voordat de functieregistratie is voltooid. U uw registratiestatus controleren door de volgende opdracht Azure PowerShell uit te voeren: Controleer de registratie als volgt:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Voer de volgende opdracht uit nadat de registratie is voltooid:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>Een Load Balancer van het type Standard maken
In dit artikel wordt ervan uitgegaan dat u een StandaardLoad Balancer hebt geïmplementeerd zoals beschreven in [Quickstart: Een standaardloadbalancer maken - Azure PowerShell](../load-balancer/quickstart-create-standard-load-balancer-powershell.md).

## <a name="retrieve-the-resource-group"></a>De resourcegroep ophalen

Voordat u uw virtuele dual-stacknetwerk maken, moet u de brongroep ophalen met [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>Een IPv6-IP-adres maken

Maak een openbaar IPv6-adres met [Nieuw-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) voor uw Standaard Load Balancer. In het volgende voorbeeld wordt een IPv6-openbaar IP-adres gemaakt met de naam *PublicIP_v6* in de *brongroep myResourceGroupSLB:*

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>Load balancer frontend configureren

Haal de bestaande configuratie van de load balancer op en voeg vervolgens het nieuwe IPv6-IP-adres toe met [Add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) als volgt:

```azurepowershell
# Retrieve the load balancer configuration
$lb = Get-AzLoadBalancer -ResourceGroupName $rg.ResourceGroupName -Name "MyLoadBalancer"
# Add IPv6 components to the local copy of the load balancer configuration
$lb | Add-AzLoadBalancerFrontendIpConfig `
  -Name "dsLbFrontEnd_v6" `
  -PublicIpAddress $PublicIP_v6
#Update the running load balancer with the new frontend
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-backend-pool"></a>Backendpool load balancer configureren

Maak de backendpool op de lokale kopie van de configuratie van de load balancer en werk de running load balancer als volgt bij met de nieuwe backendpoolconfiguratie:

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>Regels voor load balancers configureren
Haal de bestaande configuratie van de load balancer frontend en backend pool op en voeg vervolgens nieuwe regels voor het balanceren van de lasten toe met [Add-AzLoadBalancerRuleConfig](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig).

```azurepowershell
# Retrieve the updated (live) versions of the frontend and backend pool
$frontendIPv6 = Get-AzLoadBalancerFrontendIpConfig -Name "dsLbFrontEnd_v6" -LoadBalancer $lb
$backendPoolv6 = Get-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6" -LoadBalancer $lb
# Create new LB rule with the frontend and backend
$lb | Add-AzLoadBalancerRuleConfig `
  -Name "dsLBrule_v6" `
  -FrontendIpConfiguration $frontendIPv6 `
  -BackendAddressPool $backendPoolv6 `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
#Finalize all the load balancer updates on the running load balancer
$lb | Set-AzLoadBalancer
```
## <a name="add-ipv6-address-ranges"></a>IPv6-adresbereiken toevoegen

Voeg IPv6-adresbereiken toe aan het virtuele netwerk en subnet dat als volgt de VM's host:

```azurepowershell
#Add IPv6 ranges to the VNET and subnet
#Retreive the VNET object
$vnet = Get-AzVirtualNetwork  -ResourceGroupName $rg.ResourceGroupName -Name "myVnet" 
#Add IPv6 prefix to the VNET
$vnet.addressspace.addressprefixes.add("ace:cab:deca::/48")
#Update the running VNET
$vnet |  Set-AzVirtualNetwork

#Retrieve the subnet object from the local copy of the VNET
$subnet= $vnet.subnets[0]
#Add IPv6 prefix to the Subnet (subnet of the VNET prefix, of course)
$subnet.addressprefix.add("ace:cab:deca::/64")
#Update the running VNET with the new subnet configuration
$vnet |  Set-AzVirtualNetwork

```
## <a name="add-ipv6-configuration-to-nic"></a>IPv6-configuratie toevoegen aan NIC

Configureer alle VM NIC's met een IPv6-adres met [Add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) als volgt:

```azurepowershell

#Retrieve the NIC objects
$NIC_1 = Get-AzNetworkInterface -Name "myNic1" -ResourceGroupName $rg.ResourceGroupName
$NIC_2 = Get-AzNetworkInterface -Name "myNic2" -ResourceGroupName $rg.ResourceGroupName
$NIC_3 = Get-AzNetworkInterface -Name "myNic3" -ResourceGroupName $rg.ResourceGroupName
#Add an IPv6 IPconfig to NIC_1 and update the NIC on the running VM
$NIC_1 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_1 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_2 and update the NIC on the running VM
$NIC_2 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_2 | Set-AzNetworkInterface
#Add an IPv6 IPconfig to NIC_3 and update the NIC on the running VM
$NIC_3 | Add-AzNetworkInterfaceIpConfig -Name MyIPv6Config -Subnet $vnet.Subnets[0]  -PrivateIpAddressVersion IPv6 -LoadBalancerBackendAddressPool $backendPoolv6 
$NIC_3 | Set-AzNetworkInterface

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>IPv6 dual stack virtueel netwerk weergeven in Azure portal
U het virtuele IPv6-dual stack-netwerk in Azure-portal als volgt bekijken:
1. Voer in de zoekbalk van het portaal *myVnet*in.
2. Wanneer **myVnet** in de zoekresultaten wordt weergegeven, selecteert u deze. Dit lanceert de **overzichtspagina** van het dual stack virtuele netwerk genaamd *myVNet*. De dual stack virtuele netwerk toont de drie NIC's met zowel IPv4 en IPv6 configuraties gelegen in de dual stack subnet genaamd *mySubnet*.

  ![IPv6 dual stack virtueel netwerk in Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> Het virtuele IPv6 voor Azure-netwerk is beschikbaar in de Azure-portal in alleen-lezen voor deze preview-release.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een bestaande StandaardLoad Balancer bijgewerkt met een IP-configuratie van IPv4 frontend naar een dual stack -configuratie (IPv4 en IPv6). U hebt ook IPv6-configuraties toegevoegd aan de NIC's van de VM's in de backendpool en aan het virtuele netwerk dat ze host. Zie [Wat is IPv6 voor Azure Virtual Network voor](ipv6-overview.md) meer informatie over IPv6-ondersteuning in virtuele Azure-netwerken?
