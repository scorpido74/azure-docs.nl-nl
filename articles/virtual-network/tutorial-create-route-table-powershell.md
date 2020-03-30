---
title: Routenetwerkverkeer Azure PowerShell | Microsoft Documenten
description: In dit artikel leert u hoe u netwerkverkeer routeren met een routetabel met PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: 986371e479f7718fff2e1699401987cb0ca8f623
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73163986"
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Netwerkverkeer routeren met een routetabel met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure routeert standaard automatisch verkeer tussen alle subnetten in een virtueel netwerk. U kunt uw eigen routes maken om de standaardroutering van Azure te overschrijven. De mogelijkheid voor het maken van aangepaste routes is handig als u bijvoorbeeld verkeer tussen subnetten wilt routeren via een NVA (virtueel netwerkapparaat). In dit artikel leert u het volgende:

* Een routetabel maken
* Een route maken
* Een virtueel netwerk met meerdere subnetten maken
* Een routetabel aan een subnet koppelen
* Een NVA maken voor het routeren van verkeer
* Virtuele machines (VM's) implementeren in verschillende subnetten
* Verkeer van het ene subnet naar het andere leiden via een NVA

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest PowerShell lokaal te installeren en te gebruiken, vereist dit artikel de Azure PowerShell-moduleversie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-route-table"></a>Een routetabel maken

Voordat u een routetabel maken, maakt u een resourcegroep met [Nieuw-AzResourceGroep](/powershell/module/az.resources/new-azresourcegroup). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt voor alle bronnen die in dit artikel zijn gemaakt.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Maak een routetabel met [Nieuw-AzRouteTable](/powershell/module/az.network/new-azroutetable). In het volgende voorbeeld wordt een routetabel met de naam *myRouteTablePublic gemaakt.*

```azurepowershell-interactive
$routeTablePublic = New-AzRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Een route maken

Maak een route door het object routetabel op te halen met [Get-AzRouteTable,](/powershell/module/az.network/get-azroutetable)maak een route met [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig)en schrijf vervolgens de routeconfiguratie naar de routetabel met [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable).

```azurepowershell-interactive
Get-AzRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzRouteTable
```

## <a name="associate-a-route-table-to-a-subnet"></a>Een routetabel aan een subnet koppelen

Voordat u een routetabel aan een subnet kunt koppelen, moet u een virtueel netwerk en subnet maken. Maak een virtueel netwerk met [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). In het volgende voorbeeld wordt een virtueel netwerk met de naam *myVirtualNetwork* met het adresvoorvoegsel *10.0.0.0/16 .*

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Maak drie subnetten door drie subnetconfiguraties te maken met [New-AzVirtualNetworkSubnetConfig.](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) In het volgende voorbeeld worden drie subnetconfiguraties gemaakt voor *subnetten voor openbare,* *private*en *DMZ-subnetten:*

```azurepowershell-interactive
$subnetConfigPublic = Add-AzVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Schrijf de subnetconfiguraties naar het virtuele netwerk met [Set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork), waardoor de subnetten in het virtuele netwerk worden gemaakt:

```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

Koppel de *tabel myRouteTablePublic* route aan het *openbare* subnet met [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) en schrijf vervolgens de subnetconfiguratie naar het virtuele netwerk met [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork).

```azurepowershell-interactive
Set-AzVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzVirtualNetwork
```

## <a name="create-an-nva"></a>Een NVA maken

Een NVA is een VM die een netwerkfunctie uitvoert, zoals routering, firewall of WAN-optimalisatie.

Maak voordat u een VM maakt een netwerkinterface.

### <a name="create-a-network-interface"></a>Een netwerkinterface maken

Voordat u een netwerkinterface maakt, moet u de virtuele netwerk-id ophalen met [Get-AzVirtualNetwork,](/powershell/module/az.network/get-azvirtualnetwork)vervolgens de subnet-id met [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig). Maak een netwerkinterface met [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) in het *Subnet DMZ* met IP forwarding ingeschakeld:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

### <a name="create-a-vm"></a>Een virtuele machine maken

Als u een VM wilt maken en er een bestaande netwerkinterface aan wilt koppelen, moet u eerst een VM-configuratie maken met [Nieuw-AzVMConfig](/powershell/module/az.compute/new-azvmconfig). De configuratie bevat de netwerkinterface die in de vorige stap is gemaakt. Wanneer u om een gebruikersnaam en wachtwoord wordt gevraagd, selecteert u de gebruikersnaam en het wachtwoord waarmee u zich bij de vm wilt aanmelden.

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the VM."

# Create a VM configuration.
$vmConfig = New-AzVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzVMNetworkInterface -Id $nic.Id
```

Maak de VM met de [VM-configuratie met Nieuw-AzVM](/powershell/module/az.compute/new-azvm). In het volgende voorbeeld wordt een VM met de naam *myVmNva geopperd.*

```azurepowershell-interactive
$vmNva = New-AzVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

Met `-AsJob` de optie wordt de VM op de achtergrond gemaakt, zodat u door gaan naar de volgende stap.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak twee VM's in het virtuele netwerk, zodat u valideren dat verkeer van het *openbare* subnet in een latere stap via het virtuele netwerknaar het subnet *Privé* wordt doorgestuurd.

Maak een VM in het *openbare* subnet met [Nieuw-AzVM](/powershell/module/az.compute/new-azvm). In het volgende voorbeeld wordt een VM met de naam *myVmPublic* gemaakt in het *openbare* subnet van het virtuele *myVirtualNetwork-netwerk.*

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPublic" `
  -AsJob
```

Maak een VM in het *subnet Privé.*

```azurepowershell-interactive
New-AzVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmPrivate"
```

Het maken van de virtuele machine duurt een paar minuten. Ga niet verder met de volgende stap totdat de VM is gemaakt en Azure de uitvoer teruggeeft aan PowerShell.

## <a name="route-traffic-through-an-nva"></a>Verkeer routeren via een NVA

Gebruik [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) om het openbare IP-adres van de *myVmPrivate* VM terug te geven. In het volgende voorbeeld wordt het openbare IP-adres van de *myVmPrivate-vm* geretourneerd:

```azurepowershell-interactive
Get-AzPublicIpAddress `
  -Name myVmPrivate `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Gebruik de volgende opdracht om een externe bureaubladsessie te maken met de *myVmPrivate* VM vanaf uw lokale computer. Vervang `<publicIpAddress>` door het IP-adres dat is geretourneerd met de vorige opdracht.

```
mstsc /v:<publicIpAddress>
```

Open het gedownloade RDP-bestand. Selecteer **Verbinding maken** wanneer hierom wordt gevraagd.

Voer de gebruikersnaam en het wachtwoord in die u bij het maken van de VM hebt opgegeven (mogelijk moet u **Meer opties** en **Een ander account gebruiken** selecteren om de aanmeldingsgegevens op te geven die u hebt ingevoerd tijdens het maken van de virtuele machine) en selecteer **OK**. Er wordt mogelijk een certificaatwaarschuwing weergegeven tijdens het aanmelden. Selecteer **Ja** om door te gaan met de verbinding.

In een latere `tracert.exe` stap wordt de opdracht gebruikt om routering te testen. Tracert maakt gebruik van het Internet Control Message Protocol (ICMP), dat wordt geweigerd via de Windows Firewall. Schakel ICMP via de Windows-firewall in met de volgende opdracht vanuit PowerShell op de VM *myVmPrivate*:

```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4
```

Hoewel traceroute wordt gebruikt om routering in dit artikel te testen, wordt het niet aanbevolen om ICMP via de Windows Firewall toe te staan voor productie-implementaties.

U hebt doorsturen via IP in Azure voor de netwerkinterface van de VM ingeschakeld in Doorsturen via IP inschakelen. In de VM moet het besturingssysteem of een toepassing die wordt uitgevoerd op de virtuele machine, ook netwerkverkeer kunnen doorsturen. Ip forwarding inschakelen binnen het besturingssysteem van de *myVmNva*.

Van een opdrachtprompt op de *myVmPrivate* VM, extern bureaublad tot de *myVmNva:*

``` 
mstsc /v:myvmnva
```

Om Doorsturen via IP binnen het besturingssysteem in te schakelen, voert u de volgende opdracht in PowerShell in vanaf de VM *myVmNva *:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```

Start *myVmNva* opnieuw op. Hierdoor wordt ook de extern-bureaubladsessie verbroken.

Maak terwijl u nog verbinding hebt met de VM *myVmPrivate* een extern-bureaubladsessie met de VM *myVmPublic* nadat de VM *myVmNva* opnieuw is opgestart:

``` 
mstsc /v:myVmPublic
```

Schakel ICMP via de Windows-firewall in met de volgende opdracht vanuit PowerShell op de VM *myVmPublic*:

```powershell
New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
```

Om de routering van netwerkverkeer naar de VM *myVmPrivate* van de VM *myVmPublic* te testen, voert u op de VM *myVmPublic* de volgende opdracht van PowerShell uit:

```
tracert myVmPrivate
```

Het antwoord is vergelijkbaar met het volgende voorbeeld:

```
Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:

1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4

Trace complete.
```

U ziet dat de eerste hop 10.0.2.4 is, het privé IP-adres van het NVA. De tweede hop is 10.0.1.4, het privé- IP-adres van de VM *myVmPrivate*. Door de route die is toegevoegd aan de routetabel *myRouteTablePublic* en gekoppeld aan het *Openbare* subnet leidt Azure het verkeer via het NVA in plaats van rechtstreeks naar het *Privé*-subnet.

Sluit de externe bureaubladsessie met de VM *myVmPublic*. U houdt nog verbinding met de VM *myVmPrivate*.

Om de routering van netwerkverkeer naar de VM *myVmPublic* van de VM *myVmPrivate* VM te testen, voert u op de VM *myVmPrivate* de volgende opdracht uit vanaf een opdrachtprompt:

```
tracert myVmPublic
```

Het antwoord is vergelijkbaar met het volgende voorbeeld:

```
Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:

1     1 ms     1 ms     1 ms  10.0.0.4

Trace complete.
```

U ziet dat verkeer rechtstreeks vanuit *myVmPrivate* naar *myVmPublic* wordt geleid. Standaard routeert Azure verkeer rechtstreeks tussen subnetten.

Sluit de externe bureaubladsessie naar de VM *myVmPrivate*.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet langer nodig bent, gebruikt u [Remove-AzResourcegroup](/powershell/module/az.resources/remove-azresourcegroup) om de resourcegroep en alle resources die deze bevat te verwijderen.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een routetabel gemaakt en deze gekoppeld aan een subnet. U hebt een eenvoudig netwerkvirtueel toestel gemaakt dat verkeer van een openbaar subnet naar een privésubnet heeft geleid. Implementeer verschillende vooraf geconfigureerde virtuele netwerkapparaten die netwerkfuncties uitvoeren, zoals firewall- en WAN-optimalisatie vanuit de [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) Zie [Routeringoverzicht](virtual-networks-udr-overview.md) en [Routetabel beheren](manage-route-table.md) voor meer informatie over routeren.

Hoewel u veel Azure-resources binnen een virtueel netwerk kunt implementeren, kunnen resources voor sommige Azure PaaS-diensten niet in een virtueel netwerk worden geïmplementeerd. U kunt de toegang tot de resources van sommige Azure PaaS-diensten echter nog steeds beperken tot alleen verkeer vanaf een subnet van een virtueel netwerk. Zie [Netwerktoegang tot PaaS-bronnen beperken voor](tutorial-restrict-network-access-to-resources-powershell.md)meer informatie.