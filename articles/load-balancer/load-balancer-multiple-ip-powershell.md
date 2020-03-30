---
title: Taakverdeling op meerdere IP-configuraties - Azure CLI
titleSuffix: Azure Load Balancer
description: In dit artikel vindt u meer informatie over taakverdeling in primaire en secundaire IP-configuraties met Azure CLI.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 6ac9e362314cc45e6adbdcf1390f70cbe6b05de8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74075969"
---
# <a name="load-balancing-on-multiple-ip-configurations-using-powershell"></a>Taakverdeling op meerdere IP-configuraties met PowerShell

> [!div class="op_single_selector"]
> * [Portal](load-balancer-multiple-ip.md)
> * [CLI](load-balancer-multiple-ip-cli.md)
> * [Powershell](load-balancer-multiple-ip-powershell.md)


In dit artikel wordt beschreven hoe u Azure Load Balancer gebruiken met meerdere IP-adressen op een secundaire netwerkinterface (NIC). Voor dit scenario hebben we twee VM's met Windows, elk met een primaire en een secundaire NIC. Elk van de secundaire NIC's heeft twee IP-configuraties. Elke VM host zowel websites contoso.com als fabrikam.com. Elke website is gebonden aan een van de IP-configuraties op de secundaire NIC. We gebruiken Azure Load Balancer om twee frontend IP-adressen bloot te leggen, één voor elke website, om verkeer te distribueren naar de desbetreffende IP-configuratie voor de website. In dit scenario wordt hetzelfde poortnummer gebruikt voor beide frontends, evenals beide IP-adressen van de backendpool.

![AFBEELDING VAN LB-scenario](./media/load-balancer-multiple-ip/lb-multi-ip.PNG)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="steps-to-load-balance-on-multiple-ip-configurations"></a>Stappen om het saldo op meerdere IP-configuraties te laden

Volg de onderstaande stappen om het scenario in dit artikel te bereiken:

1. Installeer Azure PowerShell. Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor informatie over het installeren van de nieuwste versie van Azure PowerShell, het selecteren van het abonnement en het aanmelden bij uw account.
2. Maak een resourcegroep met de volgende instellingen:

    ```powershell
    $location = "westcentralus".
    $myResourceGroup = "contosofabrikam"
    ```

    Zie Stap 2 van [Een resourcegroep maken](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json)voor meer informatie.

3. [Maak een beschikbaarheidsset](../virtual-machines/windows/tutorial-availability-sets.md?toc=%2fazure%2fload-balancer%2ftoc.json) met uw VM's. Gebruik voor dit scenario de volgende opdracht:

    ```powershell
    New-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset" -Location "West Central US"
    ```

4. Volg de instructies stappen 3 tot en met 5 in [Een Windows VM-artikel maken](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) om de creatie van een VM voor te bereiden met één NIC. Voer stap 6.1 uit en gebruik het volgende in plaats van stap 6.2:

    ```powershell
    $availset = Get-AzAvailabilitySet -ResourceGroupName "contosofabrikam" -Name "myAvailset"
    New-AzVMConfig -VMName "VM1" -VMSize "Standard_DS1_v2" -AvailabilitySetId $availset.Id
    ```

    Voer vervolgens [Een Windows VM-stappen](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fload-balancer%2ftoc.json) 6.3 tot en met 6.8 aan.

5. Voeg een tweede IP-configuratie toe aan elk van de VM's. Volg de instructies in [Meerdere IP-adressen toewijzen aan](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md#add) het artikel van virtuele machines. Gebruik de volgende configuratie-instellingen:

    ```powershell
    $NicName = "VM1-NIC2"
    $RgName = "contosofabrikam"
    $NicLocation = "West Central US"
    $IPConfigName4 = "VM1-ipconfig2"
    $Subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "mySubnet" -VirtualNetwork $myVnet
    ```

    U hoeft de secundaire IP-configuraties niet te koppelen aan openbare IP-adressen voor het doel van deze zelfstudie. Bewerk de opdracht om het openbare IP-koppelingsonderdeel te verwijderen.

6. Voer stappen 4 tot en met 6 van dit artikel opnieuw uit voor VM2. Zorg ervoor dat u de VM-naam vervangt door VM2 wanneer u dit doet. Houd er rekening mee dat u geen virtueel netwerk hoeft te maken voor de tweede virtuele virtuele machine. U al dan niet een nieuw subnet maken op basis van uw use case.

7. Maak twee openbare IP-adressen en bewaar deze in de juiste variabelen zoals weergegeven:

    ```powershell
    $publicIP1 = New-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel contoso
    $publicIP2 = New-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam -Location 'West Central US' -AllocationMethod Dynamic -DomainNameLabel fabrikam

    $publicIP1 = Get-AzPublicIpAddress -Name PublicIp1 -ResourceGroupName contosofabrikam
    $publicIP2 = Get-AzPublicIpAddress -Name PublicIp2 -ResourceGroupName contosofabrikam
    ```

8. Maak twee ip-configuraties aan de voorkant:

    ```powershell
    $frontendIP1 = New-AzLoadBalancerFrontendIpConfig -Name contosofe -PublicIpAddress $publicIP1
    $frontendIP2 = New-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2
    ```

9. Maak uw backend-adresgroepen, een sonde en uw regels voor het balanceren van de lasten:

    ```powershell
    $beaddresspool1 = New-AzLoadBalancerBackendAddressPoolConfig -Name contosopool
    $beaddresspool2 = New-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool

    $healthProbe = New-AzLoadBalancerProbeConfig -Name HTTP -RequestPath 'index.html' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    $lbrule1 = New-AzLoadBalancerRuleConfig -Name HTTPc -FrontendIpConfiguration $frontendIP1 -BackendAddressPool $beaddresspool1 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    $lbrule2 = New-AzLoadBalancerRuleConfig -Name HTTPf -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthprobe -Protocol Tcp -FrontendPort 80 -BackendPort 80
    ```

10. Zodra u deze resources hebt gemaakt, maakt u uw load balancer:

    ```powershell
    $mylb = New-AzLoadBalancer -ResourceGroupName contosofabrikam -Name mylb -Location 'West Central US' -FrontendIpConfiguration $frontendIP1 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe
    ```

11. Voeg de tweede backend-adresgroep en ip-configuratie aan de voorzijde toe aan uw nieuw gemaakte load balancer:

    ```powershell
    $mylb = Get-AzLoadBalancer -Name "mylb" -ResourceGroupName $myResourceGroup | Add-AzLoadBalancerBackendAddressPoolConfig -Name fabrikampool | Set-AzLoadBalancer

    $mylb | Add-AzLoadBalancerFrontendIpConfig -Name fabrikamfe -PublicIpAddress $publicIP2 | Set-AzLoadBalancer
    
    Add-AzLoadBalancerRuleConfig -Name HTTP -LoadBalancer $mylb -FrontendIpConfiguration $frontendIP2 -BackendAddressPool $beaddresspool2 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80 | Set-AzLoadBalancer
    ```

12. De onderstaande opdrachten krijgen de NIC's en voegen vervolgens beide IP-configuraties van elke secundaire NIC toe aan de backend-adresgroep van de load balancer:

    ```powershell
    $nic1 = Get-AzNetworkInterface -Name "VM1-NIC2" -ResourceGroupName "MyResourcegroup";
    $nic2 = Get-AzNetworkInterface -Name "VM2-NIC2" -ResourceGroupName "MyResourcegroup";

    $nic1.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic1.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);
    $nic2.IpConfigurations[0].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[0]);
    $nic2.IpConfigurations[1].LoadBalancerBackendAddressPools.Add($mylb.BackendAddressPools[1]);

    $mylb = $mylb | Set-AzLoadBalancer

    $nic1 | Set-AzNetworkInterface
    $nic2 | Set-AzNetworkInterface
    ```

13. Ten slotte moet u DNS-bronrecords configureren om naar het respectievelijke ip-adres van de Load Balancer te wijzen. U uw domeinen hosten in Azure DNS. Zie [Azure DNS gebruiken met andere Azure-services](../dns/dns-for-azure-services.md)voor meer informatie over het gebruik van Azure DNS met Load Balancer.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het combineren van load balancing-services in Azure in [het gebruik van load-balancing services in Azure.](../traffic-manager/traffic-manager-load-balancing-azure.md)
- Meer informatie over hoe u verschillende typen logboeken in Azure gebruiken voor het beheren en oplossen van load balancer in [Azure Monitor-logboeken voor Azure Load Balancer.](../load-balancer/load-balancer-monitor-log.md)
