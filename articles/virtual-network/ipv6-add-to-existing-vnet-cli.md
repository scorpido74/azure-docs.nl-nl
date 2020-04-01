---
title: IPv6 toevoegen aan een IPv4-toepassing in het virtuele Azure-netwerk - Azure CLI
titlesuffix: Azure Virtual Network
description: In dit artikel ziet u hoe u IPv6-adressen implementeert naar een bestaande toepassing in het virtuele Azure-netwerk met Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: f3f9b32ea55f0ceebf08b22ccc7e2ceec0b6227e
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420793"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>IPv6 toevoegen aan een IPv4-toepassing in het virtuele Azure-netwerk - Azure CLI

In dit artikel ziet u hoe u IPv6-adressen toevoegt aan een toepassing die iPv4-openbaar IP-adres gebruikt in een virtueel Azure-netwerk voor een standaardbelastingsbalansr met Azure CLI. De in-place upgrade omvat een virtueel netwerk en subnet, een Standard Load Balancer met IPv4 + IPV6 frontend configuraties, VM's met NIC's die een IPv4 + IPv6 configuraties, netwerk beveiliging groep, en openbare IP's hebben.


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om Azure CLI lokaal te installeren en te gebruiken, moet u voor deze snelstart versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om na te gaan welke versie er is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) voor installatie- of upgrade-informatie.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een StandaardLoad Balancer hebt geïmplementeerd zoals beschreven in [Quickstart: Een standaardloadbalancer maken - Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md).

## <a name="create-ipv6-addresses"></a>IPv6-adressen maken

Maak een openbaar IPv6-adres met [het az-netwerk public-ip maken](/cli/azure/network/public-ip) voor uw Standard Load Balancer. In het volgende voorbeeld wordt een IPv6-openbaar IP-adres gemaakt met de naam *PublicIP_v6* in de *brongroep myResourceGroupSLB:*

```azurecli
  
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Frontend van IPv6-loadbalancer configureren

COnfigure de load balancer met de nieuwe IPv6 IP-adres met behulp van [az-netwerk lb frontend-ip maken](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) als volgt:

```azurecli
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>IPv6 load balancer backend pool configureren

Maak de backendpool voor NIC's met IPv6-adressen met behulp van de AZ [Network LB-adresgroep als](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) volgt:

```azurecli
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>IPv6-load balancerregels configureren

IPv6-load balancerregels maken met [de AZ-netwerklb-regel maken](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create).

```azurecli
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>IPv6-adresbereiken toevoegen

Voeg IPv6-adresbereiken toe aan het virtuele netwerk en subnet dat de load balancer als volgt host:

```azurecli
az network vnet update \
--name myVnet  `
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "ace:cab:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "ace:cab:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>IPv6-configuratie toevoegen aan NIC's

Configureer de VM NIC's met een IPv6-adres met behulp van [az-netwerk nic ip-config maken](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create) als volgt:

```azurecli
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>IPv6 dual stack virtueel netwerk weergeven in Azure portal
U het virtuele IPv6-dual stack-netwerk in Azure-portal als volgt bekijken:
1. Voer in de zoekbalk van het portaal *myVnet*in.
2. Wanneer **myVnet** in de zoekresultaten wordt weergegeven, selecteert u deze. Dit lanceert de **overzichtspagina** van het dual stack virtuele netwerk genaamd *myVNet*. De dual stack virtuele netwerk toont de drie NIC's met zowel IPv4 en IPv6 configuraties gelegen in de dual stack subnet genaamd *mySubnet*.

  ![IPv6 dual stack virtueel netwerk in Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, de VM en alle gerelateerde resources te verwijderen wanneer u ze niet meer nodig hebt.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een bestaande StandaardLoad Balancer bijgewerkt met een IP-configuratie van IPv4 frontend naar een dual stack -configuratie (IPv4 en IPv6). U hebt ook IPv6-configuraties toegevoegd aan de NIC's van de VM's in de backendpool. Zie [Wat is IPv6 voor Azure Virtual Network voor](ipv6-overview.md) meer informatie over IPv6-ondersteuning in virtuele Azure-netwerken?
