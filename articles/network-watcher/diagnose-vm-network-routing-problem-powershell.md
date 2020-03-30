---
title: Diagnose van een probleem met vm-netwerkroutering - Azure PowerShell
titleSuffix: Azure Network Watcher
description: In dit artikel leert u hoe u een probleem met het routeren van een virtuele machine-netwerk diagnosticeren met behulp van de volgende hopmogelijkheid van Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: b5a636471eab188dc8648761afedd81694331953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834702"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-powershell"></a>Diagnose van een probleem met de routering van een netwerk voor virtuele machines - Azure PowerShell

In dit artikel implementeert u een virtuele machine (VM) en controleert u de communicatie naar een IP-adres en URL. U stelt de oorzaak van mislukte communicatie vast en leert hoe u dit probleem kunt oplossen.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest PowerShell lokaal te installeren en `Az` te gebruiken, vereist dit artikel de Azure PowerShell-module. Voer `Get-Module -ListAvailable Az` uit om te zien welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.



## <a name="create-a-vm"></a>Een virtuele machine maken

Voordat u een VM kunt maken, maakt u eerst een resourcegroep die de VM bevat. Maak een resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

Maak de VM met [New-AzVM](/powershell/module/az.compute/new-azvm). Als deze stap wordt uitgevoerd, wordt u gevraagd referenties op te geven. De waarden die u invoert, worden geconfigureerd als de gebruikersnaam en het wachtwoord voor de virtuele machine.

```azurepowershell-interactive
$vM = New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVm" `
    -Location "East US"
```

Het maken van de virtuele machine duurt een paar minuten. Ga niet door met de resterende stappen totdat de VM is gemaakt en uitvoer wordt geretourneerd met PowerShell.

## <a name="test-network-communication"></a>Netwerkcommunicatie testen

Als u de netwerkcommunicatie met Network Watcher wilt testen, moet u eerst een netwerkwatcher in de regio inschakelen waarin de VM zich bevindt en vervolgens de volgende hopcapaciteit van Network Watcher gebruiken om de communicatie te testen.

## <a name="enable-network-watcher"></a>Netwerk-watcher inschakelen

Als u al een netwerkwatcher hebt ingeschakeld in de regio Oost-VS, gebruikt u [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher) om de netwerkwatcher op te halen. In het volgende voorbeeld wordt een bestaande netwerk-watcher opgehaald met de naam *NetworkWatcher_eastus* die zich in de resourcegroep *NetworkWatcherRG* bevindt:

```azurepowershell-interactive
$networkWatcher = Get-AzNetworkWatcher `
  -Name NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG
```

Als u nog geen netwerkwatcher hebt ingeschakeld in de regio Oost-VS, gebruikt u [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher) om een netwerkwatcher in de regio Oost-VS te maken:

```azurepowershell-interactive
$networkWatcher = New-AzNetworkWatcher `
  -Name "NetworkWatcher_eastus" `
  -ResourceGroupName "NetworkWatcherRG" `
  -Location "East US"
```

### <a name="use-next-hop"></a>Volgende hop gebruiken

Azure maakt automatisch routes naar standaardbestemmingen. U kunt uw eigen, aangepaste routes maken om die standaardroutes te overschrijven. Soms hebben aangepaste routes tot gevolg dat de communicatie mislukt. Als u routering vanaf een vm wilt testen, gebruikt u de opdracht [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) om de volgende routehop te bepalen wanneer het verkeer is bestemd voor een specifiek adres.

Uitgaande communicatie van de VM naar een van de IP-adressen testen voor www.bing.com:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 13.107.21.200
```

Na een paar seconden wordt u door de uitvoer geïnformeerd dat de **NextHopType** **internet**is en dat de **RouteTableId** **systeemroute**is. Dit resultaat laat u weten dat er een geldige route naar de bestemming is.

Uitgaande communicatie van de VM naar 172.31.0.100 testen:

```azurepowershell-interactive
Get-AzNetworkWatcherNextHop `
  -NetworkWatcher $networkWatcher `
  -TargetVirtualMachineId $VM.Id `
  -SourceIPAddress 192.168.1.4 `
  -DestinationIPAddress 172.31.0.100
```

De geretourneerde uitvoer geeft aan dat **Geen** **nexthoptype**is en dat de **RouteTableId** ook **Systeemroute**is. Hieruit kunt u afleiden dat er wel een geldige systeemroute is naar de bestemming, maar dat er geen volgende hop is voor het routeren van het verkeer naar de bestemming.

## <a name="view-details-of-a-route"></a>Details van een route weergeven

Als u routering verder wilt analyseren, bekijkt u de effectieve routes voor de netwerkinterface met de opdracht [Get-AzEffectiveRouteTable:](/powershell/module/az.network/get-azeffectiveroutetable)

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVm `
  -ResourceGroupName myResourceGroup |
  Format-table
```

Uitvoer die de volgende tekst bevat, wordt geretourneerd:

```powershell
Name State  Source  AddressPrefix           NextHopType NextHopIpAddress
---- -----  ------  -------------           ----------- ----------------
     Active Default {192.168.0.0/16}        VnetLocal   {}              
     Active Default {0.0.0.0/0}             Internet    {}              
     Active Default {10.0.0.0/8}            None        {}              
     Active Default {100.64.0.0/10}         None        {}              
     Active Default {172.16.0.0/12}         None        {}              
```

Zoals u zien in de vorige uitvoer, de route met het **adresvoorvoegsel** van **0.0.0.0/0** routes al het verkeer niet bestemd voor adressen binnen het adres van andere route voorvoegsels met een volgende hop van **internet**. Zoals u ook zien in de uitvoer, hoewel er een standaardroute naar het voorvoegsel 172.16.0.0/12 is, dat het 172.31.0.100-adres bevat, is de **nextHopType** **Geen**. Azure maakt een standaardroute naar 172.16.0.0/12, maar stelt geen volgend hoptype in, tenzij daar een reden voor is. Als u bijvoorbeeld het adresbereik 172.16.0.0/12 hebt toegevoegd aan de adresruimte van het virtuele netwerk, wijzigt Azure het **nextHopType** in **virtueel netwerk** voor de route. Een controle zou dan **virtueel netwerk** als **nextHopType**tonen.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer dit niet meer nodig is, u [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de brongroep en alle bronnen die deze bevat te verwijderen:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een VM gemaakt en netwerkroutering van de VM gediagnosticeerd. U hebt geleerd dat Azure verschillende standaardroutes maakt en u hebt de routering naar twee verschillende bestemmingen getest. Lees hier meer over [routering in Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) en hoe u [aangepaste routes maakt](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Voor uitgaande VM-verbindingen u ook de latentie en toegestane en geweigerde netwerkverkeer tussen de VM en een eindpunt bepalen met behulp van de capaciteit [voor het oplossen van verbindingsproblemen](network-watcher-connectivity-powershell.md) van Network Watcher. U de communicatie tussen een VM en een eindpunt, zoals een IP-adres of URL, na verloop van tijd controleren met behulp van de mogelijkheid voor netwerkwatcher-verbindingsmonitor. Zie [Een netwerkverbinding controleren](connection-monitor.md)voor meer informatie.
