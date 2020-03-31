---
title: Diagnose van een azure probleem met het routeren van virtuele machines | Microsoft Documenten
description: Meer informatie over het diagnosticeren van een probleem met het routeren van virtuele machines door de effectieve routes voor een virtuele machine te bekijken.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2018
ms.author: kumud
ms.openlocfilehash: 13d74fbb4a7c133ca2365fd2cbfce4b3d2bea72e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75350614"
---
# <a name="diagnose-a-virtual-machine-routing-problem"></a>Diagnose van een probleem met het routeren van virtuele machines

In dit artikel leert u hoe u een routeringsprobleem diagnosticeren door de routes te bekijken die effectief zijn voor een netwerkinterface in een virtuele machine (VM). Azure maakt verschillende standaardroutes voor elk virtueel netwerksubnet. U de standaardroutes van Azure overschrijven door routes in een routetabel te definiëren en vervolgens de routetabel aan een subnet te koppelen. De combinatie van routes die u maakt, de standaardroutes van Azure en alle routes die vanuit uw on-premises netwerk worden gepropageerd via een Azure VPN-gateway (als uw virtuele netwerk is verbonden met uw on-premises netwerk) via het Border Gateway Protocol (BGP), zijn de effectieve routes voor alle netwerkinterfaces in een subnet. Zie [Netwerkoverzicht,](virtual-networks-overview.md) [Netwerkinterface](virtual-network-network-interface.md)en [Routeringsoverzicht](virtual-networks-udr-overview.md)als u niet bekend bent met virtuele netwerk-, netwerkinterface- of routeringsconcepten.

## <a name="scenario"></a>Scenario

U probeert verbinding te maken met een virtuele machine, maar de verbinding mislukt. Als u wilt bepalen waarom u geen verbinding maken met de VM, u de effectieve routes voor een netwerkinterface bekijken via de [Azure-portal,](#diagnose-using-azure-portal) [PowerShell](#diagnose-using-powershell)of azure [CLI](#diagnose-using-azure-cli).

De volgende stappen gaan ervan uit dat u een bestaande VM hebt om de effectieve routes voor te bekijken. Als u geen bestaande VM hebt, implementeert u eerst een [Linux-](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Windows-VM](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) om de taken in dit artikel te voltooien. De voorbeelden in dit artikel zijn voor een VM met de naam *myVM* met een netwerkinterface genaamd *myVMNic1*. De VM- en netwerkinterface bevinden zich in een resourcegroep met de naam *myResourceGroup*en bevinden zich in de regio *Oost-VS.* Wijzig de waarden in de stappen, indien van toepassing, voor de VM waarvoor u het probleem diagnosticeert.

## <a name="diagnose-using-azure-portal"></a>Diagnose stellen met Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een Azure-account met de [benodigde machtigingen.](virtual-network-network-interface.md#permissions)
2. Voer boven aan de Azure-portal de naam in van een vm die zich in de bedrijfsstatus bevindt, in het zoekvak. Wanneer de naam van de virtuele machine wordt weergegeven in de zoekresultaten, selecteert u deze.
3. Selecteer **onder Instellingen** aan de linkerkant **Netwerken**en navigeer naar de bron van de netwerkinterface door de naam ervan te selecteren.
     ![Netwerkinterfaces weergeven](./media/diagnose-network-routing-problem/view-nics.png)
4. Selecteer aan de linkerkant **Effectieve routes**. De effectieve routes voor een netwerkinterface met de naam **myVMNic1** worden weergegeven in de volgende afbeelding: ![Effectieve routes weergeven](./media/diagnose-network-routing-problem/view-effective-routes.png)

    Als er meerdere netwerkinterfaces aan de VM zijn gekoppeld, u de effectieve routes voor elke netwerkinterface bekijken door deze te selecteren. Aangezien elke netwerkinterface zich in een ander subnet kan bevinden, kan elke netwerkinterface verschillende effectieve routes hebben.

    In het voorbeeld in de vorige afbeelding zijn de vermelde routes standaardroutes die Azure voor elk subnet maakt. Uw lijst heeft ten minste deze routes, maar kan extra routes hebben, afhankelijk van de mogelijkheden die u mogelijk hebt ingeschakeld voor uw virtuele netwerk, zoals het wordt gekeken met een ander virtueel netwerk of verbonden met uw on-premises netwerk via een Azure VPN-gateway. Zie [Virtuele netwerkverkeerroutering](virtual-networks-udr-overview.md)voor meer informatie over elk van de routes en andere routes die u voor uw netwerkinterface zien. Als uw lijst een groot aantal routes heeft, u het gemakkelijker vinden om **Download**te selecteren om een CSV-bestand te downloaden met de lijst met routes.

Hoewel in de vorige stappen effectieve routes via de VM zijn bekeken, u ook effectieve routes bekijken via een:
- **Individuele netwerkinterface:** meer informatie over het [weergeven van een netwerkinterface.](virtual-network-network-interface.md#view-network-interface-settings)
- **Tabel afzonderlijke route:** Meer informatie over het [weergeven van een routetabel](manage-route-table.md#view-details-of-a-route-table).

## <a name="diagnose-using-powershell"></a>Diagnose stellen met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U de opdrachten uitvoeren die volgen in de [Azure Cloud Shell](https://shell.azure.com/powershell)of door PowerShell vanaf uw computer uit te voeren. De Azure Cloud Shell is een gratis interactieve shell. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Als u PowerShell vanaf uw computer uitvoert, hebt u de Azure PowerShell-module, versie 1.0.0 of hoger, nodig. Voer `Get-Module -ListAvailable Az` uit op uw computer om de geïnstalleerde versie te vinden. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet `Connect-AzAccount` u ook uitvoeren om u aan te melden bij Azure met een account dat over de [vereiste machtigingen beschikt.](virtual-network-network-interface.md#permissions)

Krijg de effectieve routes voor een netwerkinterface met [Get-AzEffectiveRouteTable.](/powershell/module/az.network/get-azeffectiveroutetable) In het volgende voorbeeld worden de effectieve routes voor een netwerkinterface met de naam *myVMNic1*, die deel uitmaakt van een resourcegroep met de naam *myResourceGroup:*

```azurepowershell-interactive
Get-AzEffectiveRouteTable `
  -NetworkInterfaceName myVMNic1 `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Zie [Routeringsoverzicht](virtual-networks-udr-overview.md)voor meer informatie die in de uitvoer wordt geretourneerd. Uitvoer wordt alleen geretourneerd als de vm in de loopstatus is. Als er meerdere netwerkinterfaces aan de VM zijn gekoppeld, u de effectieve routes voor elke netwerkinterface bekijken. Aangezien elke netwerkinterface zich in een ander subnet kan bevinden, kan elke netwerkinterface verschillende effectieve routes hebben. Als u nog steeds een communicatieprobleem hebt, raadpleegt u [aanvullende diagnose](#additional-diagnosis) en [overwegingen.](#considerations)

Als u de naam van een netwerkinterface niet kent, maar wel de naam weet van de VM waaraan de netwerkinterface is gekoppeld, worden de iD's van alle netwerkinterfaces die aan een VM zijn gekoppeld, als volgt teruggegeven:

```azurepowershell-interactive
$VM = Get-AzVM -Name myVM `
  -ResourceGroupName myResourceGroup
$VM.NetworkProfile
```

U ontvangt uitvoer die vergelijkbaar is met het volgende voorbeeld:

```powershell
NetworkInterfaces
-----------------
{/subscriptions/<ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMNic1
```

In de vorige uitvoer is de naam van de netwerkinterface *myVMNic1*.

## <a name="diagnose-using-azure-cli"></a>Diagnose stellen met Azure CLI

U de opdrachten uitvoeren die volgen in de [Azure Cloud Shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Dit artikel vereist de Azure CLI-versie 2.0.32 of hoger. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). Als u de Azure CLI lokaal uitvoert, `az login` moet u ook Azure uitvoeren en aanmelden met een account dat over de [vereiste machtigingen beschikt.](virtual-network-network-interface.md#permissions)

Krijg de effectieve routes voor een netwerkinterface met [de AZ Network Nic Show-effective-route-table.](/cli/azure/network/nic#az-network-nic-show-effective-route-table) In het volgende voorbeeld worden de effectieve routes voor een netwerkinterface met de naam *myVMNic1* opgenomen in een resourcegroep met de naam *myResourceGroup:*

```azurecli-interactive
az network nic show-effective-route-table \
  --name myVMNic1 \
  --resource-group myResourceGroup
```

Zie [Routeringsoverzicht](virtual-networks-udr-overview.md)voor meer informatie die in de uitvoer wordt geretourneerd. Uitvoer wordt alleen geretourneerd als de vm in de loopstatus is. Als er meerdere netwerkinterfaces aan de VM zijn gekoppeld, u de effectieve routes voor elke netwerkinterface bekijken. Aangezien elke netwerkinterface zich in een ander subnet kan bevinden, kan elke netwerkinterface verschillende effectieve routes hebben. Als u nog steeds een communicatieprobleem hebt, raadpleegt u [aanvullende diagnose](#additional-diagnosis) en [overwegingen.](#considerations)

Als u de naam van een netwerkinterface niet kent, maar wel de naam weet van de VM waaraan de netwerkinterface is gekoppeld, worden de iD's van alle netwerkinterfaces die aan een VM zijn gekoppeld, als volgt teruggegeven:

```azurecli-interactive
az vm show \
  --name myVM \
  --resource-group myResourceGroup
```

## <a name="resolve-a-problem"></a>Een probleem oplossen

Het oplossen van routeringsproblemen bestaat meestal uit:

- Een aangepaste route toevoegen om een van de standaardroutes van Azure te overschrijven. Meer informatie over het [toevoegen van een aangepaste route](manage-route-table.md#create-a-route).
- Een aangepaste route wijzigen of verwijderen die kan leiden naar een ongewenste locatie. Meer informatie over het [wijzigen](manage-route-table.md#change-a-route) of [verwijderen van](manage-route-table.md#delete-a-route) een aangepaste route.
- Ervoor zorgen dat de routetabel die aangepaste routes bevat die u hebt gedefinieerd, is gekoppeld aan het subnet waarin de netwerkinterface zich bevindt. Meer informatie over het [koppelen van een routetabel aan een subnet](manage-route-table.md#associate-a-route-table-to-a-subnet).
- Ervoor zorgen dat apparaten zoals Azure VPN-gateway of virtuele netwerkapparaten die u hebt geïmplementeerd, kunnen worden gebruikt. Gebruik de [VPN-diagnostische](../network-watcher/diagnose-communication-problem-between-networks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) mogelijkheid van Network Watcher om eventuele problemen met een Azure VPN-gateway te bepalen.

Zie [Overwegingen](#considerations) en aanvullende diagnose als u nog steeds communicatieproblemen hebt.

## <a name="considerations"></a>Overwegingen

Houd rekening met de volgende punten bij het oplossen van communicatieproblemen:

- Routering is gebaseerd op de langste prefix match (LPM) tussen routes die u hebt gedefinieerd, border gateway protocol (BGP) en systeemroutes. Als er meer dan één route is met dezelfde LPM-overeenkomst, wordt een route geselecteerd op basis van de oorsprong in de volgorde die wordt vermeld in [het routeringsoverzicht](virtual-networks-udr-overview.md#how-azure-selects-a-route). Met effectieve routes u alleen effectieve routes zien die een LPM-match zijn, op basis van alle beschikbare routes. Als u ziet hoe de routes worden geëvalueerd voor een netwerkinterface, u eenvoudiger problemen oplossen met specifieke routes die van invloed kunnen zijn op de communicatie van uw VM.
- Als u aangepaste routes naar een netwerkvirtueel toestel (NVA) hebt gedefinieerd, met *Virtueel toestel* als het volgende hoptype, moet u ervoor zorgen dat IP-forwarding is ingeschakeld op de NVA die het verkeer ontvangt, of dat pakketten worden verwijderd. Meer informatie over [het inschakelen van IP-forwarding voor een netwerkinterface](virtual-network-network-interface.md#enable-or-disable-ip-forwarding). Daarnaast moet het besturingssysteem of de toepassing binnen de NVA ook netwerkverkeer kunnen doorsturen en daartoe geconfigureerd zijn.
- Als u een route naar 0.0.0.0/0 hebt gemaakt, wordt al het uitgaande internetverkeer doorgestuurd naar de volgende hop die u hebt opgegeven, zoals naar een NVA- of VPN-gateway. Het creëren van een dergelijke route wordt vaak aangeduid als gedwongen tunneling. Externe verbindingen met behulp van de RDP- of SSH-protocollen van het internet naar uw VM werken mogelijk niet met deze route, afhankelijk van hoe de volgende hop het verkeer verwerkt. Geforceerde tunneling kan worden ingeschakeld:
    - Bij het gebruik van site-to-site VPN, door het creëren van een route met een volgende hop type *VPN Gateway*. Meer informatie over [het configureren van gedwongen tunneling](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
    - Als een 0.0.0.0/0 (standaardroute) via BGP wordt geadverteerd via een virtuele netwerkgateway wanneer u een site-to-site VPN of ExpressRoute-circuit gebruikt. Meer informatie over het gebruik van BGP met een [site-to-site VPN](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [ExpressRoute.](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ip-addresses-used-for-azure-private-peering)
- Om virtueel netwerkpeerverkeer correct te laten werken, moet er een systeemroute met een volgende hoptype *VNet-peering* bestaan voor het voorvoegselbereik van het peered virtuele netwerk. Als een dergelijke route niet bestaat en de koppeling voor virtuele netwerkpeering **is verbonden:**
    - Wacht een paar seconden, en probeer het opnieuw. Als het een nieuw opgerichte peeringkoppeling is, duurt het soms langer om routes naar alle netwerkinterfaces in een subnet te verspreiden. Zie Overzicht van [virtuele netwerkpeering](virtual-network-peering-overview.md) voor meer informatie over virtueel netwerkpeeren en [het beheren van virtueel netwerkpeeren.](virtual-network-manage-peering.md)
    - Netwerkbeveiligingsgroepregels kunnen van invloed zijn op de communicatie. Zie [Een probleem met een probleem met een probleem met een probleem met het netwerkfilter voor virtuele machines diagnosticeren](diagnose-network-traffic-filter-problem.md).
- Hoewel Azure standaardroutes toewijst aan elke Azure-netwerkinterface, krijgt alleen de primaire netwerkinterface een standaardroute (0.0.0.0/0) of gateway toegewezen aan het besturingssysteem van de VM als u meerdere netwerkinterfaces aan de VM hebt gekoppeld. Meer informatie over het maken van een standaardroute voor secundaire netwerkinterfaces die zijn gekoppeld aan een [Windows-](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) of [Linux-vm.](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) Meer informatie over [primaire en secundaire netwerkinterfaces](virtual-network-network-interface-vm.md#constraints).

## <a name="additional-diagnosis"></a>Aanvullende diagnose

* Als u een snelle test wilt uitvoeren om het volgende hoptype te bepalen voor verkeer dat is bestemd voor een locatie, gebruikt u de [next-hopmogelijkheid](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) van Azure Network Watcher. Volgende hop vertelt u wat de volgende hop type is voor het verkeer bestemd naar een bepaalde locatie.
* Als er geen routes zijn waardoor de netwerkcommunicatie van een VM mislukt, kan het probleem te wijten zijn aan firewallsoftware die binnen het besturingssysteem van de VM wordt uitgevoerd
* Als u het [verkeer](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) via een VPN-gateway of NVA naar een on-premises apparaat forceert, u mogelijk geen verbinding maken met een vm via internet, afhankelijk van hoe u routering voor de apparaten hebt geconfigureerd. Controleer of de route die u hebt geconfigureerd voor het apparaat verkeer naar een openbaar of privé IP-adres voor de vm leidt.
* Gebruik de mogelijkheid voor het oplossen van problemen met de [verbinding](../network-watcher/network-watcher-connectivity-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) van Network Watcher om route-, filter- en in-OS-oorzaken van uitgaande communicatieproblemen te bepalen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over alle taken, eigenschappen en instellingen voor een [routetabel en routes](manage-route-table.md).
- Meer informatie over alle [volgende hoptypen, systeemroutes en hoe Azure een route selecteert.](virtual-networks-udr-overview.md)
