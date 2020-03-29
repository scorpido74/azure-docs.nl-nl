---
title: Virtuele netwerken verbinden met VNet-peering - Azure PowerShell
description: In dit artikel leert u hoe u virtuele netwerken verbinden met virtueel netwerkpeeren met Azure PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
tags: azure-resource-manager
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: caa620c2389bb6e2387636bc262ceb2de99d8e34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201285"
---
# <a name="connect-virtual-networks-with-virtual-network-peering-using-powershell"></a>Virtuele netwerken verbinden met virtueel netwerkpeeren met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt virtuele netwerken met elkaar verbinden met virtueel-netwerk peering. Wanneer virtuele netwerken als peers zijn gekoppeld, kunnen resources in beide virtuele netwerken met elkaar communiceren met dezelfde latentie en bandbreedte als wanneer de resources zich in hetzelfde virtuele netwerk zouden bevinden. In dit artikel leert u het volgende:

* Twee virtuele netwerken maken
* Twee virtuele netwerken koppelen met virtueel-netwerkpeering
* Een virtuele machine (VM) implementeren op elk van de virtuele netwerken
* Communicatie tussen virtuele machines

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest PowerShell lokaal te installeren en te gebruiken, vereist dit artikel de Azure PowerShell-moduleversie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-virtual-networks"></a>Virtuele netwerken maken

Voordat u een virtueel netwerk maakt, moet u een brongroep maken voor het virtuele netwerk en alle andere bronnen die in dit artikel zijn gemaakt. Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In het volgende voorbeeld wordt een virtueel netwerk met de naam *myVirtualNetwork1 met* het adresvoorvoegsel *10.0.0.0/16 .*

```azurepowershell-interactive
$virtualNetwork1 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork1 `
  -AddressPrefix 10.0.0.0/16
```

Maak een subnetconfiguratie met [Nieuw-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig). In het volgende voorbeeld wordt een subnetconfiguratie met een adresvoorvoegsel van 10.0.0.0/24:

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork1
```

Schrijf de subnetconfiguratie naar het virtuele netwerk met [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), waardoor het subnet wordt gemaakt:

```azurepowershell-interactive
$virtualNetwork1 | Set-AzVirtualNetwork
```

Maak een virtueel netwerk met een voorvoegsel van 10.1.0.0/16 en één subnet:

```azurepowershell-interactive
# Create the virtual network.
$virtualNetwork2 = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork2 `
  -AddressPrefix 10.1.0.0/16

# Create the subnet configuration.
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Subnet1 `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork2

# Write the subnet configuration to the virtual network.
$virtualNetwork2 | Set-AzVirtualNetwork
```

## <a name="peer-virtual-networks"></a>Peering van virtuele netwerken

Maak een peering met [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering). In het volgende voorbeeld wordt *myVirtualNetwork1* gepeers naar *myVirtualNetwork2*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork1-myVirtualNetwork2 `
  -VirtualNetwork $virtualNetwork1 `
  -RemoteVirtualNetworkId $virtualNetwork2.Id
```

In de uitvoer die wordt geretourneerd nadat de vorige opdracht is uitgevoerd, ziet u dat de **PeeringState** *is gestart*. Het peering blijft in de *geïnitieerde* status totdat u het peering maakt van *myVirtualNetwork2* naar *myVirtualNetwork1*. Maak een peering van *myVirtualNetwork2* naar *myVirtualNetwork1*.

```azurepowershell-interactive
Add-AzVirtualNetworkPeering `
  -Name myVirtualNetwork2-myVirtualNetwork1 `
  -VirtualNetwork $virtualNetwork2 `
  -RemoteVirtualNetworkId $virtualNetwork1.Id
```

In de uitvoer die wordt geretourneerd nadat de vorige opdracht is uitgevoerd, ziet u dat de **PeeringState** is *verbonden*. Azure heeft ook de peeringstatus van de *myVirtualNetwork1-myVirtualNetwork2-peering* gewijzigd in *Verbonden*. Controleer of de peeringstatus voor de *myVirtualNetwork1-myVirtualNetwork2-peering* is gewijzigd *in Verbonden* met [Get-AzVirtualNetworkPeering.](/powershell/module/az.network/get-azvirtualnetworkpeering)

```azurepowershell-interactive
Get-AzVirtualNetworkPeering `
  -ResourceGroupName myResourceGroup `
  -VirtualNetworkName myVirtualNetwork1 `
  | Select PeeringState
```

Bronnen in het ene virtuele netwerk kunnen niet communiceren met bronnen in het andere virtuele netwerk totdat de **PeeringState** voor de peerings in beide virtuele netwerken *is verbonden.*

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak een VM in elk virtueel netwerk, zodat u er in een latere stap tussen kunt communiceren.

### <a name="create-the-first-vm"></a>De eerste VM maken

Maak een VM met [New-AzVM](/powershell/module/az.compute/new-azvm). In het volgende voorbeeld wordt een VM met de naam *myVm1* in het virtuele *netwerk myVirtualNetwork1* gemaakt. Met `-AsJob` de optie wordt de VM op de achtergrond gemaakt, zodat u door gaan naar de volgende stap. Voer degebruikersnaam en het wachtwoord in waarmee u zich bij de virtuele machine wilt aanmelden.

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork1" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm1" `
  -AsJob
```

### <a name="create-the-second-vm"></a>De tweede VM maken

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork2" `
  -SubnetName "Subnet1" `
  -ImageName "Win2016Datacenter" `
  -Name "myVm2"
```

Het maken van de virtuele machine duurt een paar minuten. Ga niet verder met latere stappen totdat Azure de VM maakt en de uitvoer naar PowerShell retourneert.

## <a name="communicate-between-vms"></a>Communicatie tussen virtuele machines

U vanaf internet verbinding maken met het openbare IP-adres van een virtuele machine. Gebruik [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) om het openbare IP-adres van een virtuele machine op te halen. In het volgende voorbeeld wordt het openbare IP-adres van de VM *myVm1* opgehaald:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVm1 `
  -ResourceGroupName myResourceGroup | Select IpAddress
```

Gebruik de volgende opdracht om een externe bureaubladsessie te maken met de *myVm1* VM vanaf uw lokale computer. Vervang `<publicIpAddress>` door het IP-adres dat is geretourneerd met de vorige opdracht.

```
mstsc /v:<publicIpAddress>
```

Er wordt een Extern bureaublad-protocol (.rdp)-bestand gemaakt, gedownload naar uw computer en geopend. Voer de gebruikersnaam en het wachtwoord in (mogelijk moet u **Meer keuzes**selecteren, vervolgens een ander **account gebruiken**om de referenties op te geven die u hebt ingevoerd toen u de vm hebt gemaakt) en klik vervolgens op **OK**. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Klik op **Ja** of **Doorgaan** om door te gaan met de verbinding.

Schakel op de *myVm1* VM het Internet Control Message Protocol (ICMP) in via de Windows-firewall, zodat u deze VM in een latere stap pingen vanaf *myVm2* met PowerShell:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Hoewel ping wordt gebruikt om te communiceren tussen VM's in dit artikel, wordt het niet aanbevolen om ICMP via de Windows Firewall toe te staan voor productie-implementaties.

Maak verbinding met *myVm2* door op *myVm1* de volgende opdracht in te voeren vanaf een opdrachtprompt:

```
mstsc /v:10.1.0.4
```

Aangezien u ping op *myVm1*hebt ingeschakeld, u deze nu pingen op IP-adres vanuit een opdrachtprompt op de *myVm2* VM:

```
ping 10.0.0.4
```

U ontvangt vier antwoorden. Verbreek de RDP-sessies met zowel *myVm1* als *myVm2*.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet langer nodig bent, gebruikt u [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) om de resourcegroep en alle resources die deze bevat te verwijderen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u twee netwerken in dezelfde Azure-regio verbinden, met virtuele netwerkpeering. U kunt ook virtuele netwerken in verschillende [ondersteunde regio's](virtual-network-manage-peering.md#cross-region) en in [ verschillende Azure-abonnementen ](create-peering-different-subscriptions.md#powershell) 'peeren', en peering gebruiken om [netwerkontwerpen met een stertopologie](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) te maken. Zie voor meer informatie over virtueel-netwerkpeering [Peering op virtueel netwerk](virtual-network-peering-overview.md) en [Virtueel-netwerkpeerings beheren](virtual-network-manage-peering.md).

U [uw eigen computer verbinden met een virtueel netwerk](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) via een VPN en communiceren met bronnen in een virtueel netwerk of in peered virtuele netwerken. Zie [scriptvoorbeelden](powershell-samples.md)voor herbruikbare scripts om veel van de taken in de virtuele netwerkartikelen te voltooien.