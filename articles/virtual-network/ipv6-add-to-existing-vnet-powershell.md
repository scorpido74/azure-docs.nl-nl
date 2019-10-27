---
title: Een IPv4-toepassing upgraden naar IPv6 in azure Virtual Network-Power shell
titlesuffix: Azure Virtual Network
description: In dit artikel wordt beschreven hoe u IPv6-adressen implementeert voor een bestaande toepassing in een virtueel Azure-netwerk met behulp van Azure Power shell.
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
ms.openlocfilehash: 907a6de2ff89ddd3c2cb5bdab67e1deb984141dc
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965239"
---
# <a name="upgrade-an-ipv4-application-to-ipv6-in-azure-virtual-network---powershell-preview"></a>Een IPv4-toepassing upgraden naar IPv6 in het virtuele netwerk van Azure-Power shell (preview)

In dit artikel wordt beschreven hoe u IPv6-connectiviteit kunt toevoegen aan een bestaande IPv4-toepassing in een virtueel Azure-netwerk met een Standard Load Balancer en een openbaar IP-adres. Het in-place upgrade omvat:
- IPv6-adres ruimte voor het virtuele netwerk en subnet
- een Standard Load Balancer met zowel IPv4-als IPV6-front-end configuraties
- Vm's met Nic's met een IPv4 + IPv6-configuratie
- IPv open bare IP, zodat de load balancer een Internet gerichte IPv6-verbinding heeft

> [!Important]
> IPv6-ondersteuning voor Azure Virtual Network is momenteel beschikbaar als open bare preview. Deze preview wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u Power shell lokaal wilt installeren en gebruiken, moet u voor dit artikel gebruikmaken van de Azure PowerShell module versie 6.9.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="prerequisites"></a>Vereisten

### <a name="register-the-service"></a>De service registreren

Voordat u een dual stack-toepassing in azure implementeert, moet u uw abonnement voor deze preview-functie configureren met behulp van de volgende Azure PowerShell:

Meld u als volgt aan:
```azurepowershell
Register-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Register-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Het duurt Maxi maal 30 minuten voordat de functie registratie is voltooid. U kunt de registratie status controleren door de volgende Azure PowerShell opdracht uit te voeren: Controleer de registratie als volgt:
```azurepowershell
Get-AzProviderFeature -FeatureName AllowIPv6VirtualNetwork -ProviderNamespace Microsoft.Network
Get-AzProviderFeature -FeatureName AllowIPv6CAOnStandardLB -ProviderNamespace Microsoft.Network
```
Nadat de registratie is voltooid, voert u de volgende opdracht uit:

```azurepowershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

### <a name="create-a-standard-load-balancer"></a>Een Load Balancer van het type Standard maken
In dit artikel wordt ervan uitgegaan dat u een Standard Load Balancer hebt geïmplementeerd zoals beschreven in [Quick Start: een Standard Load Balancer Azure PowerShell maken](../load-balancer/quickstart-create-standard-load-balancer-powershell.md).

## <a name="retrieve-the-resource-group"></a>De resource groep ophalen

Voordat u een virtueel netwerk met twee stacks kunt maken, moet u de resource groep ophalen met [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup).

```azurepowershell
 $rg = Get-AzResourceGroup  -ResourceGroupName "myResourceGroupSLB"
```

## <a name="create-an-ipv6-ip-addresses"></a>Een IPv6-IP-adres maken

Maak een openbaar IPv6-adres met [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) voor uw Standard Load Balancer. In het volgende voor beeld wordt een openbaar IP-adres voor IPv6 gemaakt met de naam *PublicIP_v6* in de resource groep *myResourceGroupSLB* :

```azurepowershell
  
  $PublicIP_v6 = New-AzPublicIpAddress `
  -Name "PublicIP_v6" `
  -ResourceGroupName $rg.ResourceGroupName `
  -Location $rg.Location  `
  -Sku Standard  `
  -AllocationMethod Static `
  -IpAddressVersion IPv6
```

## <a name="configure-load-balancer-frontend"></a>load balancer frontend configureren

Haal de bestaande load balancer configuratie op en voeg als volgt het nieuwe IPv6 IP-adres toe met [add-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/Add-AzLoadBalancerFrontendIpConfig) :

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

## <a name="configure-load-balancer-backend-pool"></a>load balancer back-end-groep configureren

Maak de back-end-groep op de lokale kopie van de load balancer configuratie en werk de actieve load balancer bij met de nieuwe back-end-pool configuratie als volgt:

```azurepowershell
$lb | Add-AzLoadBalancerBackendAddressPoolConfig -Name "LbBackEndPool_v6"
# Update the running load balancer with the new backend pool
$lb | Set-AzLoadBalancer
```

## <a name="configure-load-balancer-rules"></a>load balancer-regels configureren
Haal de bestaande configuratie voor de front-end-en back-AzLoadBalancerRuleConfig op en voeg nieuwe taakverdelings regels toe met behulp van [add-](/powershell/module/az.network/Add-AzLoadBalancerRuleConfig)Load Balancer.

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

Voeg als volgt IPv6-adresbereiken toe aan het virtuele netwerk en het subnet dat als host fungeert voor de Vm's:

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

Configureer alle virtuele machine Nic's met een IPv6-adres met behulp van [add-AzNetworkInterfaceIpConfig](/powershell/module/az.network/Add-AzNetworkInterfaceIpConfig) als volgt:

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

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Virtueel IPv6-netwerk met dubbele stack in Azure Portal weer geven
U kunt het virtuele IPv6-netwerk met dubbele stack als volgt weer geven in Azure Portal:
1. Voer in de zoek balk van de portal *myVnet*in.
2. Wanneer **myVnet** wordt weer gegeven in de zoek resultaten, selecteert u dit. Hiermee opent u de **overzichts** pagina van het virtuele netwerk met dubbele stack met de naam *myVNet*. Het virtuele netwerk met dubbele stack toont de drie Nic's met zowel IPv4-als IPv6-configuraties die zich bevinden in het dubbele stack-subnet met de naam *mySubnet*.

  ![Virtueel IPv6-netwerk met dubbele stack in azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)

> [!NOTE]
> Het virtuele netwerk van IPv6 voor Azure is beschikbaar in het Azure Portal alleen-lezen voor deze preview-versie.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een bestaande Standard Load Balancer bijgewerkt met een IPv4-frontend-IP-configuratie naar een configuratie met dubbele stack (IPv4 en IPv6). U hebt ook IPv6-configuraties toegevoegd aan de Nic's van de virtuele machines in de back-endadresgroep en op de Virtual Network die deze host. Zie [Wat is IPv6 voor azure Virtual Network?](ipv6-overview.md) voor meer informatie over IPv6-ondersteuning in azure Virtual Networks.
