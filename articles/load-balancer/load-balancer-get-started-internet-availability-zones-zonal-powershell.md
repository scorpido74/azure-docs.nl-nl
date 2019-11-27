---
title: Een Load Balancer met zonegebonden-frontend maken-Azure PowerShell
titleSuffix: Azure Load Balancer
description: Meer informatie over het maken van Standard Load Balancer met een zonegebonden-frontend met Azure PowerShell
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 1d05b130a98ce816d070bc3ad16e25b867d7dc8a
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215124"
---
#  <a name="create-a-standard-load-balancer-with-zonal-frontend-using-azure-powershell"></a>Een Standard Load Balancer met zonegebonden-frontend maken met behulp van Azure PowerShell

In dit artikel wordt beschreven hoe u een openbaar [Standard Load Balancer](https://aka.ms/azureloadbalancerstandard) maakt met een zonegebonden-frontend met behulp van een openbaar IP-adres. Zie [Standard Load Balancer-en beschikbaarheids](load-balancer-standard-availability-zones.md)zones voor meer informatie over de werking van beschikbaarheids zones met Standard Load Balancer. 

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

> [!NOTE]
> Ondersteuning voor Beschikbaarheidszones is beschikbaar voor geselecteerde Azure-resources en regio's en groottefamilies van de virtuele machine. Zie [overzicht van Beschikbaarheidszones](https://docs.microsoft.com/azure/availability-zones/az-overview)voor meer informatie over hoe u aan de slag kunt gaan en welke Azure-resources,-regio's en-Series met VM-grootten u de beschikbaarheids zones wilt proberen. Voor ondersteuning kunt u vragen stellen op [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones) of [een Azure-ondersteuningsticket openen](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de volgende opdracht:

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupZLB -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>Maak een openbaar IP-standaard 
Maak een openbaar IP-standaard met de volgende opdracht:

```azurepowershell-interactive
$publicIp = New-AzPublicIpAddress -ResourceGroupName myResourceGroupZLB -Name 'myPublicIPZonal' `
  -Location westeurope -AllocationMethod Static -Sku Standard -zone 1
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>Maak een front-end-IP-configuratie voor de website

Maak een front-end-IP-configuratie met de volgende opdracht:

```azurepowershell-interactive
$feip = New-AzLoadBalancerFrontendIpConfig -Name 'myFrontEnd' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>De back-end-adresgroep maken

Maak een back-end-adresgroep op met de volgende opdracht uit:

```azurepowershell-interactive
$bepool = New-AzLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>Maken van een load balancer-test op poort 80

Maak een statustest op poort 80 voor de load balancer met behulp van de volgende opdracht uit:

```azurepowershell-interactive
$probe = New-AzLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>Een load balancer-regel maken
 Maak een load balancer-regel met de volgende opdracht:

```azurepowershell-interactive
   $rule = New-AzLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>Een load balancer maken
Maak een Standard Load Balancer met behulp van de volgende opdracht:

```azurepowershell-interactive
$lb = New-AzLoadBalancer -ResourceGroupName myResourceGroupZLB -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Standard Load Balancer-en beschikbaarheids zones](load-balancer-standard-availability-zones.md).