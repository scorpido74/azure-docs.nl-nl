---
title: Poorten openen voor een vm met Azure PowerShell
description: Meer informatie over het openen van een poort / het maken van een eindpunt voor uw Windows-VM met de implementatiemodus Azure Resource Manager en Azure PowerShell
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.openlocfilehash: a0dcc53d84edb4dd697213106c02626df24acfd8
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869403"
---
# <a name="how-to-open-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Poorten en eindpunten openen voor een VM in Azure met PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Snelle opdrachten
Als u een Netwerkbeveiligingsgroep en ACL-regels wilt maken, moet [u de nieuwste versie van Azure PowerShell installeren.](/powershell/azureps-cmdlets-docs) U deze stappen ook [uitvoeren met de Azure-portal.](nsg-quickstart-portal.md)

Meld u aan bij uw Azure-account:

```powershell
Connect-AzAccount
```

Vervang in de volgende voorbeelden parameternamen door uw eigen waarden. Voorbeelden van parameternamen waren *myResourceGroup,* *myNetworkSecurityGroup*en *myVnet*.

Maak een regel met [Nieuw-AzNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecurityruleconfig). In het volgende voorbeeld wordt een regel met de naam *myNetworkSecurityGroupRule* maakt om *tcp-verkeer* op poort *80*toe te staan:

```powershell
$httprule = New-AzNetworkSecurityRuleConfig `
    -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" `
    -Access "Allow" `
    -Protocol "Tcp" `
    -Direction "Inbound" `
    -Priority "100" `
    -SourceAddressPrefix "Internet" `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 80
```

Maak vervolgens uw groep Netwerkbeveiliging met [New-AzNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/az.network/new-aznetworksecuritygroup) en wijs de HTTP-regel toe die u zojuist als volgt hebt gemaakt. In het volgende voorbeeld wordt een netwerkbeveiligingsgroep met de naam *myNetworkSecurityGroup geopperd:*

```powershell
$nsg = New-AzNetworkSecurityGroup `
    -ResourceGroupName "myResourceGroup" `
    -Location "EastUS" `
    -Name "myNetworkSecurityGroup" `
    -SecurityRules $httprule
```

Laten we nu uw netwerkbeveiligingsgroep toewijzen aan een subnet. In het volgende voorbeeld wordt een bestaand virtueel netwerk met de naam *myVnet* aan de variabele *$vnet* met [Get-AzVirtualNetwork:](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork)

```powershell
$vnet = Get-AzVirtualNetwork `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Koppel uw netwerkbeveiligingsgroep aan uw subnet met [Set-AzVirtualNetworkSubnetConfig.](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetworksubnetconfig) In het volgende voorbeeld wordt het subnet *mySubnet* gekoppeld aan uw netwerkbeveiligingsgroep:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Werk ten slotte uw virtuele netwerk bij met [Set-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/set-azvirtualnetwork) om uw wijzigingen van kracht te laten worden:

```powershell
Set-AzVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Meer informatie over netwerkbeveiligingsgroepen
Met de snelle opdrachten u aan de slag met verkeer dat naar uw VM stroomt. Netwerkbeveiligingsgroepen bieden veel geweldige functies en granulariteit voor het beheren van de toegang tot uw bronnen. U hier meer lezen over [het maken van een netwerkbeveiligingsgroep en ACL-regels.](tutorial-virtual-network.md#secure-network-traffic)

Voor zeer beschikbare webtoepassingen moet u uw VM's achter een Azure Load Balancer plaatsen. De load balancer distribueert verkeer naar VM's, met een netwerkbeveiligingsgroep die verkeersfiltering biedt. Zie Voor meer informatie [hoe u linux-virtuele machines in Azure in balans laden om een zeer beschikbare toepassing te maken.](tutorial-load-balancer.md)

## <a name="next-steps"></a>Volgende stappen
In dit voorbeeld hebt u een eenvoudige regel gemaakt om HTTP-verkeer toe te staan. In de volgende artikelen vindt u informatie over het maken van meer gedetailleerde omgevingen:

* [Overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Wat is een netwerkbeveiligingsgroep?](../../virtual-network/security-overview.md)
* [Overzicht van Azure Load Balancer](../../load-balancer/load-balancer-overview.md)

