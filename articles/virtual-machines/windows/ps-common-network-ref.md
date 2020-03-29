---
title: Algemene PowerShell-opdrachten voor Azure Virtual Networks
description: Algemene PowerShell-opdrachten om u aan de slag te helpen bij het maken van een virtueel netwerk en de bijbehorende bronnen voor VM's.
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
ms.openlocfilehash: b9debe04759772ef51946dc99943ec4eff6f61dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77911828"
---
# <a name="common-powershell-commands-for-azure-virtual-networks"></a>Algemene PowerShell-opdrachten voor Azure Virtual Networks

Als u een virtuele machine wilt maken, moet u een [virtueel netwerk](../../virtual-network/virtual-networks-overview.md) maken of weten over een bestaand virtueel netwerk waarin de VM kan worden toegevoegd. Wanneer u een vm maakt, moet u doorgaans ook overwegen de bronnen te maken die in dit artikel worden beschreven.

Zie [Azure PowerShell installeren en configureren](/powershell/azure/overview) voor informatie over het installeren van de nieuwste versie van Azure PowerShell, het selecteren van het abonnement en het aanmelden bij uw account.

Sommige variabelen kunnen nuttig zijn voor u als u meer dan één van de opdrachten in dit artikel uitvoert:

- $location - De locatie van de netwerkbronnen. U [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation) gebruiken om een [geografische regio](https://azure.microsoft.com/regions/) te vinden die voor u werkt.
- $myResourceGroup - De naam van de resourcegroep waar de netwerkbronnen zich bevinden.

## <a name="create-network-resources"></a>Netwerkbronnen maken

| Taak | Opdracht |
| ---- | ------- |
| Subnetconfiguraties maken |$subnet1 = [Nieuw-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) -Naam "mySubnet1" -AddressPrefix XX. X.X.X/XX<BR>$subnet2 = Nieuw-AzVirtualNetworkSubnetConfig -Naam "mySubnet2" -AddressPrefix XX. X.X.X/XX<BR><BR>Een typisch netwerk kan een subnet hebben voor een [laadbalans op internet en](../../load-balancer/load-balancer-internet-overview.md) een apart subnet voor een [interne load balancer.](../../load-balancer/load-balancer-internal-overview.md) |
| Een virtueel netwerk maken |$vnet = [Nieuw-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) -Naam "myVNet" -ResourceGroupName $myResourceGroup -Locatie $location -AddressPrefix XX. X.X.X/XX -Subnet $subnet1, $subnet2 |
| Testen op een unieke domeinnaam |[Test-AzDnsAvailability](https://docs.microsoft.com/powershell/module/az.network/test-azdnsavailability) -DomainNameLabel "myDNS" -Locatie $location<BR><BR>U een DNS-domeinnaam opgeven voor een [openbare IP-bron,](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)waarmee een toewijzing wordt gemaakt voor domainname.location.cloudapp.azure.com naar het openbare IP-adres in de door Azure beheerde DNS-servers. De naam mag alleen letters, cijfers en afbreekstreepjes bevatten. Het eerste en laatste teken moet een letter of nummer zijn en de domeinnaam moet uniek zijn binnen de Azure-locatie. Als **True** wordt geretourneerd, is uw voorgestelde naam wereldwijd uniek. |
| Een openbaar IP-adres maken |$pip = [Nieuw-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) -Naam "myPublicIp" -ResourceGroupName $myResourceGroup -DomainNameLabel "myDNS" -Locatie $location -AllocationMethod Dynamic<BR><BR>Het openbare IP-adres gebruikt de domeinnaam die u eerder hebt getest en wordt gebruikt door de frontend-configuratie van de load balancer. |
| Een frontend IP-configuratie maken |$frontendIP = [Nieuw-AzLoadBalancerFrontendIpConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerfrontendipconfig) -Naam "myFrontendIP" -PublicIpAddress $pip<BR><BR>De frontendconfiguratie bevat het openbare IP-adres dat u eerder hebt gemaakt voor binnenkomend netwerkverkeer. |
| Een back-endadresgroep maken |$beAddressPool = [Nieuw-AzLoadBalancerBackendAddressPoolConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) -Naam "myBackendAddressPool"<BR><BR>Biedt interne adressen voor de back-end van de load balancer die toegankelijk zijn via een netwerkinterface. |
| Een sonde maken |$healthProbe = [Nieuw-AzLoadBalancerProbeConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerprobeconfig) -Naam "myProbe" -RequestPath 'HealthProbe.aspx' -Protocol http-Port 80 -IntervalInSeconds 15 -ProbeCount 2<BR><BR>Bevat statussondes die worden gebruikt om de beschikbaarheid van virtuele machines-exemplaren in de backend-adresgroep te controleren. |
| Een regel voor taakverdeling maken |$lbRule = [Nieuw-AzLoadBalancerRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerruleconfig) -Naam HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80<BR><BR>Bevat regels die een openbare poort op de load balancer toewijzen aan een poort in de groep backendadres. |
| Een binnenkomende NAT-regel maken |$inboundNATRule = [Nieuw-AzLoadBalancerInboundNatRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) -Naam "myInboundRule1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389<BR><BR>Bevat regels voor het in kaart brengen van een openbare poort op de load balancer naar een poort voor een specifieke virtuele machine in de backend-adresgroep. |
| Een load balancer maken |$loadBalancer = [Nieuw-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/new-azloadbalancer) -ResourceGroupName $myResourceGroup -Naam "myLoadBalancer" -Locatie $location -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule -LoadBalancingRule $lbRule -BackendAddressPool $beAddressPool -Probe $healthProbe |
| Een netwerkinterface maken |$nic1= [Nieuw-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) -ResourceGroupName $myResourceGroup -Naam "myNIC" -Locatie $location -PrivateIpAddress XX. X.X.X -Subnet $subnet2 -LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] -LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Maak een netwerkinterface met behulp van het openbare IP-adres en het virtuele netwerksubnet dat u eerder hebt gemaakt. |

## <a name="get-information-about-network-resources"></a>Informatie over netwerkbronnen

| Taak | Opdracht |
| ---- | ------- |
| Virtuele netwerken weergeven |[Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork) -ResourceGroupName $myResourceGroup<BR><BR>Hier worden alle virtuele netwerken in de resourcegroep weergegeven. |
| Informatie over een virtueel netwerk |Get-AzVirtualNetwork -Naam "myVNet" -ResourceGroupName $myResourceGroup |
| Subnetten in een virtueel netwerk weergeven |Get-AzVirtualNetwork -Naam "myVNet" -ResourceGroupName $myResourceGroup &#124; Select Subnets |
| Informatie over een subnet |[Get-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetworksubnetconfig) -Naam "mySubnet1" -VirtualNetwork $vnet<BR><BR>Hier vindt u informatie over het subnet in het opgegeven virtuele netwerk. De $vnet waarde vertegenwoordigt het object dat wordt geretourneerd door Get-AzVirtualNetwork. |
| IP-adressen lijst |[Get-azpublicipaddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) -ResourceGroupName $myResourceGroup<BR><BR>Hiermee worden de openbare IP-adressen in de resourcegroep weergegeven. |
| Load balancers aanbieden |[Get-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer) -ResourceGroupName $myResourceGroup<BR><BR>Hier worden alle load balancers in de resourcegroep weergegeven. |
| Netwerkinterfaces weergeven |[Get-azNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterface) -ResourceGroupName $myResourceGroup<BR><BR>Hier worden alle netwerkinterfaces in de resourcegroep weergegeven. |
| Informatie over een netwerkinterface |Get-AzNetworkInterface -Naam "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Krijgt informatie over een specifieke netwerkinterface. |
| De IP-configuratie van een netwerkinterface downloaden |[Get-AzNetworkInterfaceIPConfig](https://docs.microsoft.com/powershell/module/az.network/get-aznetworkinterfaceipconfig) -Naam "myNICIP" -NetworkInterface $nic<BR><BR>Hier vindt u informatie over de IP-configuratie van de opgegeven netwerkinterface. De $nic waarde vertegenwoordigt het object dat wordt geretourneerd door Get-AzNetworkInterface. |

## <a name="manage-network-resources"></a>Netwerkresources beheren

| Taak | Opdracht |
| ---- | ------- |
| Een subnet toevoegen aan een virtueel netwerk |[Add-AzVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/az.network/add-azvirtualnetworksubnetconfig) -AddressPrefix XX. X.X.X/XX -Naam "mySubnet1" -VirtualNetwork $vnet<BR><BR>Hiermee voegt u een subnet toe aan een bestaand virtueel netwerk. De $vnet waarde vertegenwoordigt het object dat wordt geretourneerd door Get-AzVirtualNetwork. |
| Een virtueel netwerk verwijderen |[Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork) -Name "myVNet" -ResourceGroupName $myResourceGroup<BR><BR>Hiermee verwijdert u het opgegeven virtuele netwerk uit de brongroep. |
| Een netwerkinterface verwijderen |[Remove-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/remove-aznetworkinterface) -Name "myNIC" -ResourceGroupName $myResourceGroup<BR><BR>Hiermee verwijdert u de opgegeven netwerkinterface uit de brongroep. |
| Een load balancer verwijderen |[Remove-AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer) -Naam "myLoadBalancer" -ResourceGroupName $myResourceGroup<BR><BR>Hiermee verwijdert u de opgegeven load balancer uit de resourcegroep. |
| Een openbaar IP-adres verwijderen |[Remove-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress)-Name "myIPAddress" -ResourceGroupName $myResourceGroup<BR><BR>Hiermee verwijdert u het opgegeven openbare IP-adres uit de brongroep. |

## <a name="next-steps"></a>Volgende stappen
Gebruik de netwerkinterface die u zojuist hebt gemaakt wanneer u [een VM maakt.](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


