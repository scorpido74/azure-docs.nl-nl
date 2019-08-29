---
title: Algemene Power shell-opdrachten voor virtuele Azure-netwerken | Microsoft Docs
description: Algemene Power shell-opdrachten waarmee u aan de slag kunt gaan met het maken van een virtueel netwerk en de bijbehorende resources voor Vm's.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 56e1a73c-8299-4996-bd03-f74585caa1dc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: d7ab705291b8705994aed96f1d270f792e4b2fb0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102528"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Algemene Power shell-opdrachten voor virtuele Azure-netwerken

Als u een virtuele machine wilt maken, moet u een [virtueel netwerk](../../virtual-network/virtual-networks-overview.md) maken of een bestaand virtueel netwerk weten waarin de VM kan worden toegevoegd. Wanneer u een virtuele machine maakt, moet u meestal ook overwegen om de resources te maken die in dit artikel worden beschreven.

Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor informatie over het installeren van de nieuwste versie van Azure PowerShell, het selecteren van het abonnement en het aanmelden bij uw account.

Sommige variabelen kunnen nuttig zijn wanneer u meer dan een van de opdrachten in dit artikel uitvoert:

- $location: de locatie van de netwerk bronnen. U kunt [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) gebruiken om een [geografische regio](https://azure.microsoft.com/regions/) te vinden die geschikt is voor u.
- $myResourceGroup: de naam van de resource groep waarin de netwerk bronnen zich bevinden.

## <a name="create-network-resources"></a>Netwerkbronnen maken

| Taak | Opdracht |
| ---- | ------- |
| Subnetconfiguraties maken |$subnet1 = [New-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Name "mySubnet1" -AddressPrefix XX.X.X.X/XX<BR>$subnet2 = New-AzVirtualNetworkSubnetConfig -Name "mySubnet2" -AddressPrefix XX.X.X.X/XX<BR><BR>Een typisch netwerk heeft mogelijk een subnet voor een [Internet gerichte Load Balancer](../../load-balancer/load-balancer-internet-overview.md) en een afzonderlijk subnet voor een [interne Load Balancer](../../load-balancer/load-balancer-internal-overview.md). |
| Een virtueel netwerk maken |$vnet = [New-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup -Location $location -AddressPrefix XX.X.X.X/XX -Subnet $subnet1, $subnet2 |
| Testen op een unieke domein naam |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -domeinnaam label "myDNS"-locatie $location<BR><BR>U kunt een DNS-domein naam opgeven voor een [open bare IP-resource](../../virtual-network/virtual-network-ip-addresses-overview-arm.md), waarmee een toewijzing voor domainname.location.cloudapp.Azure.com wordt gemaakt aan het open bare IP-adres op de door Azure beheerde DNS-servers. De naam mag alleen letters, cijfers en afbreekstreepjes bevatten. Het eerste en laatste teken moeten een letter of cijfer zijn en de domein naam moet uniek zijn binnen de Azure-locatie. Als **waar** wordt geretourneerd, is de voorgestelde naam wereld wijd uniek. |
| Een openbaar IP-adres maken |$pip = [New-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Name "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Location $location -AllocationMethod Dynamic<BR><BR>Het open bare IP-adres gebruikt de domein naam die u eerder hebt getest en wordt gebruikt door de front-end-configuratie van de load balancer. |
| Een front-end-IP-configuratie maken |$frontendIP = [New-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Name "myFrontendIP" -PublicIpAddress $pip<BR><BR>De front-end-configuratie bevat het open bare IP-adres dat u eerder hebt gemaakt voor binnenkomend netwerk verkeer. |
| Een back-endadresgroep maken |$beAddressPool = [New-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Name "myBackendAddressPool"<BR><BR>Biedt interne adressen voor de back-end van de load balancer die toegankelijk zijn via een netwerk interface. |
| Een test maken |$healthProbe = [New-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -name "myProbe"-RequestPath ' healthProbe. aspx '-protocol http-poort 80-IntervalInSeconds 15-ProbeCount 2<BR><BR>Bevat status tests die worden gebruikt om de beschik baarheid van exemplaren van virtuele machines in de back-end-adres groep te controleren. |
| Een taakverdelings regel maken |$lbRule = [New-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Bevat regels die een open bare poort op de load balancer toewijzen aan een poort in de back-end-adres groep. |
| Een binnenkomende NAT-regel maken |$inboundNATRule = [New-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -name "myInboundRule1"-FrontendIpConfiguration $FrontendIP-protocol TCP-FrontendPort 3441-BackendPort 3389<BR><BR>Bevat regels die een open bare poort op de load balancer toewijzen aan een poort voor een specifieke virtuele machine in de back-end-adres groep. |
| Een load balancer maken |$loadBalancer = [New-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $MyResourceGroup-name "myLoadBalancer"-location $location-FrontendIpConfiguration $FrontendIP-InboundNatRule $InboundNATRule-LoadBalancingRule $LbRule-BackendAddressPool $ beAddressPool-test $healthProbe |
| Een netwerk interface maken |$nic 1 = [New-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $MyResourceGroup-name "myNIC"-location $location-PrivateIpAddress xx. X. X. X-subnet $subnet 2-LoadBalancerBackendAddressPool $loadBalancer. BackendAddressPools [0]-LoadBalancerInboundNatRule $loadBalancer. InboundNatRules [0]<BR><BR>Maak een netwerk interface met behulp van het open bare IP-adres en het subnet van het virtuele netwerk dat u eerder hebt gemaakt. |

## <a name="get-information-about-network-resources"></a>Informatie over netwerk bronnen ophalen

| Taak | Opdracht |
| ---- | ------- |
| Virtuele netwerken weer geven |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Een lijst met alle virtuele netwerken in de resource groep. |
| Informatie over een virtueel netwerk ophalen |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup |
| Subnetten in een virtueel netwerk weer geven |Get-AzVirtualNetwork -Name "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Informatie over een subnet ophalen |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Name "mySubnet1" -VirtualNetwork $vnet<BR><BR>Hiermee haalt u informatie op over het subnet in het opgegeven virtuele netwerk. De $vnet waarde vertegenwoordigt het object dat door Get-AzVirtualNetwork wordt geretourneerd. |
| IP-adressen weer geven |[Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Een lijst met de open bare IP-adressen in de resource groep. |
| Load balancers weer geven |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Een lijst met alle load balancers in de resource groep. |
| Netwerk interfaces weer geven |[Get-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Een lijst met alle netwerk interfaces in de resource groep. |
| Informatie over een netwerk interface ophalen |Get-AzNetworkInterface-name "myNIC"-ResourceGroupName $myResourceGroup<BR><BR>Hiermee haalt u informatie op over een specifieke netwerk interface. |
| De IP-configuratie van een netwerk interface ophalen |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -name "myNICIP"-Network Interface $NIC<BR><BR>Hiermee haalt u informatie op over de IP-configuratie van de opgegeven netwerk interface. De $nic waarde vertegenwoordigt het object dat door Get-AzNetworkInterface wordt geretourneerd. |

## <a name="manage-network-resources"></a>Netwerkresources beheren

| Taak | Opdracht |
| ---- | ------- |
| Een subnet toevoegen aan een virtueel netwerk |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix xx. X. X. X/XX-name "mySubnet1"-VirtualNetwork $vnet<BR><BR>Hiermee voegt u een subnet toe aan een bestaand virtueel netwerk. De $vnet waarde vertegenwoordigt het object dat door Get-AzVirtualNetwork wordt geretourneerd. |
| Een virtueel netwerk verwijderen |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Hiermee verwijdert u het opgegeven virtuele netwerk uit de resource groep. |
| Een netwerk interface verwijderen |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Hiermee verwijdert u de opgegeven netwerk interface uit de resource groep. |
| Een load balancer verwijderen |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Name "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Hiermee verwijdert u de opgegeven load balancer uit de resource groep. |
| Een openbaar IP-adres verwijderen |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Hiermee verwijdert u het opgegeven open bare IP-adres uit de resource groep. |

## <a name="next-steps"></a>Volgende stappen
* Gebruik de netwerk interface die u zojuist hebt gemaakt tijdens [het maken van een virtuele machine](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Meer informatie over hoe u [een virtuele machine met meerdere netwerk interfaces kunt maken](../../virtual-network/virtual-network-deploy-multinic-classic-ps.md).

