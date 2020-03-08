---
title: Een Azure-route tabel maken, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Meer informatie over het maken, wijzigen of verwijderen van een route tabel.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: fe8ea4dfb4de45a1e09648ac51fe8d74f93a6b9e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78357771"
---
# <a name="create-change-or-delete-a-route-table"></a>Een route tabel maken, wijzigen of verwijderen

Azure routeert automatisch verkeer tussen Azure-subnetten, virtuele netwerken en on-premises netwerken. Als u de standaard routering van Azure wilt wijzigen, doet u dat door een route tabel te maken. Als u geen ervaring hebt met route ring in virtuele netwerken, kunt u meer informatie hierover vinden in het [overzicht van route ring](virtual-networks-udr-overview.md) of door een [zelf studie](tutorial-create-route-table-portal.md)te volt ooien.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voer de volgende taken uit voordat u de stappen in een van de secties van dit artikel uitvoert:

* Als u nog geen Azure-account hebt, kunt u zich aanmelden voor een [gratis proef account](https://azure.microsoft.com/free).<br>
* Als u de portal gebruikt, opent u https://portal.azure.comen meldt u zich aan met uw Azure-account.<br>
* Als u Power shell-opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/powershell)of Power shell uitvoeren vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Voor deze zelf studie is de Azure PowerShell module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.<br>
* Als u Azure-opdracht regel interface opdrachten gebruikt om taken in dit artikel te volt ooien, moet u de opdrachten uitvoeren in de [Azure Cloud shell](https://shell.azure.com/bash)of door de CLI vanaf uw computer uit te voeren. Voor deze zelf studie is de Azure CLI-versie 2.0.31 of hoger vereist. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. Als u de Azure CLI lokaal uitvoert, moet u ook `az login` uitvoeren om een verbinding te maken met Azure.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure met, moet worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [machtigingen](#permissions).

## <a name="create-a-route-table"></a>Een routetabel maken

Er kan maar een beperkt aantal routetabellen worden gemaakt voor elke Azure-locatie en elk Azure-abonnement. Zie [Netwerkenlimieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.

1. Selecteer in de linkerbovenhoek van de portal **+ een resource maken**.
1. Selecteer **netwerken**en selecteer vervolgens **route tabel**.
1. Voer een **naam** in voor de route tabel, selecteer uw **abonnement**, maak een nieuwe **resource groep**of selecteer een bestaande resource groep, selecteer een **locatie**en selecteer vervolgens **maken**. Als u van plan bent om de route tabel te koppelen aan een subnet in een virtueel netwerk dat is verbonden met uw on-premises netwerk via een VPN-gateway en u het **door sturen van virtuele netwerk gateways**uitschakelt, worden uw on-premises routes niet door gegeven aan de netwerk interfaces in het subnet.

### <a name="create-route-table---commands"></a>Route tabel maken-opdrachten

* Azure CLI: [AZ Network Route-Table Create](/cli/azure/network/route-table/route)<br>
* Power shell: [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)

## <a name="view-route-tables"></a>Route tabellen weer geven

Voer in het zoekvak boven aan de portal *route tabellen* in het zoekvak in. Wanneer **route tabellen** worden weer gegeven in de zoek resultaten, selecteert u deze. De route tabellen die in uw abonnement aanwezig zijn, worden weer gegeven.

### <a name="view-route-table---commands"></a>Route tabel weer geven-opdrachten

* Azure CLI: [AZ Network Route-Table List](/cli/azure/network/route-table/route)<br>
* Power shell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="view-details-of-a-route-table"></a>Details van een route tabel weer geven

1. Voer in het zoekvak boven aan de portal *route tabellen* in het zoekvak in. Wanneer **route tabellen** worden weer gegeven in de zoek resultaten, selecteert u deze.
1. Selecteer de route tabel in de lijst waarvoor u details wilt weer geven. Onder **instellingen**kunt u de **routes** in de route tabel weer geven en de **subnetten** waaraan de route tabel is gekoppeld.
1. Voor meer informatie over algemene Azure-instellingen raadpleegt u de volgende informatie:

    * [Activiteitenlogboek](../azure-monitor/platform/platform-logs-overview.md)<br>
    * [Toegangs beheer (IAM)](../role-based-access-control/overview.md)<br>
    * [Tags](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Vergren delingen](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Automatiserings script](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Details van route tabel-opdrachten weer geven

* Azure CLI: [AZ Network Route-Table show](/cli/azure/network/route-table/route)<br>
* Power shell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="change-a-route-table"></a>Een route tabel wijzigen

1. Voer in het zoekvak boven aan de portal *route tabellen* in het zoekvak in. Wanneer **route tabellen** worden weer gegeven in de zoek resultaten, selecteert u deze.
1. Selecteer de route tabel die u wilt wijzigen. De meest voorkomende wijzigingen zijn het [toevoegen](#create-a-route) of [verwijderen](#delete-a-route) van routes en het [koppelen](#associate-a-route-table-to-a-subnet) van route tabellen aan, of het [loskoppelen](#dissociate-a-route-table-from-a-subnet) van route tabellen van subnetten.

### <a name="change-a-route-table---commands"></a>Een route tabel wijzigen-opdrachten

* Azure CLI: [AZ Network Route-Table Update](/cli/azure/network/route-table/route)<br>
* Power shell: [set-AzRouteTable](/powershell/module/az.network/set-azroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Een routetabel aan een subnet koppelen

Een subnet kan worden gekoppeld aan geen enkele of één routetabel. Een routetabel kan worden gekoppeld aan geen enkele of meerdere subnetten. Aangezien routetabellen niet worden gekoppeld aan virtuele netwerken, moet u afzonderlijke routetabellen koppelen aan de gewenste subnetten. Al het verkeer dat het subnet verlaat, wordt doorgestuurd op basis van routes die u hebt gemaakt in route tabellen, [standaard routes](virtual-networks-udr-overview.md#default)en routes die worden door gegeven vanuit een on-premises netwerk, als het virtuele netwerk is verbonden met een virtuele Azure-netwerk gateway (EXPRESSROUTE of VPN). U kunt routetabellen alleen koppelen aan subnetten in virtuele netwerken die zich op dezelfde Azure-locatie en in hetzelfde abonnement bevinden als de routetabel.

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, selecteert u deze.
1. Selecteer het virtuele netwerk in de lijst met het subnet waaraan u een route tabel wilt koppelen.
1. Selecteer **subnetten** onder **instellingen**.
1. Selecteer het subnet waaraan u de route tabel wilt koppelen.
1. Selecteer **route tabel**, selecteer de route tabel die u aan het subnet wilt koppelen en selecteer vervolgens **Opslaan**.

Als uw virtuele netwerk is verbonden met een Azure VPN-gateway, koppelt u geen routetabel aan het [gatewaysubnet](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) met een route die als bestemming heeft: 0.0.0.0/0. Als u dit wel doet, functioneert de gateway mogelijk niet juist. Zie [virtueel netwerk verkeer routeren](virtual-networks-udr-overview.md#default-route)voor meer informatie over het gebruik van 0.0.0.0/0 in een route.

### <a name="associate-a-route-table---commands"></a>Een route tabel koppelen-opdrachten

* Azure CLI: [AZ Network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* Power shell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Een routetabel ontkoppelen van een subnet

Wanneer u een route tabel loskoppelt van een subnet, routeert Azure verkeer op basis van de [standaard routes](virtual-networks-udr-overview.md#default).

1. Voer in het zoekvak boven aan de portal *virtuele netwerken* in het zoekvak in. Wanneer **virtuele netwerken** worden weer gegeven in de zoek resultaten, selecteert u deze.
1. Selecteer het virtuele netwerk dat het subnet bevat dat u wilt loskoppelen van een route tabel.
1. Selecteer **subnetten** onder **instellingen**.
1. Selecteer het subnet dat u wilt loskoppelen van de route tabel.
1. Selecteer **route tabel**, selecteer **geen**en selecteer vervolgens **Opslaan**.

### <a name="dissociate-a-route-table---commands"></a>Een route tabel ontkoppelen-opdrachten

* Azure CLI: [AZ Network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* Power shell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-route-table"></a>Een routetabel verwijderen

Als er een routetabel is gekoppeld aan subnetten, kan deze niet worden verwijderd. [Koppel een routetabel los](#dissociate-a-route-table-from-a-subnet) van alle subnetten voordat u deze probeert te verwijderen.

1. Voer in het zoekvak boven aan de portal *route tabellen* in het zoekvak in. Wanneer **route tabellen** worden weer gegeven in de zoek resultaten, selecteert u deze.
1. Selecteer **...** aan de rechter kant van de route tabel die u wilt verwijderen.
1. Selecteer **verwijderen**en selecteer vervolgens **Ja**.

### <a name="delete-a-route-table---commands"></a>Een route tabel verwijderen-opdrachten

* Azure CLI: [AZ Network Route-Table delete](/cli/azure/network/route-table/route)<br>
* Power shell: [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable)

## <a name="create-a-route"></a>Een route maken

Er geldt een limiet voor het aantal routes per route tabel dat per Azure-locatie en-abonnement kan worden gemaakt. Zie [Netwerkenlimieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.

1. Voer in het zoekvak boven aan de portal *route tabellen* in het zoekvak in. Wanneer **route tabellen** worden weer gegeven in de zoek resultaten, selecteert u deze.
1. Selecteer de route tabel in de lijst waaraan u een route wilt toevoegen.
1. Selecteer **routes**onder **instellingen**.
1. Selecteer **+ Toevoegen**.
1. Voer een unieke **naam** in voor de route in de route tabel.
1. Voer in de CIDR-notatie het **adres voorvoegsel**in waarnaar u verkeer wilt door sturen. Het voorvoegsel kan niet in meer dan één route in de routetabel worden gedupliceerd, hoewel het voorvoegsel zich wel in een ander voorvoegsel kan bevinden. Als u bijvoorbeeld 10.0.0.0/16 als voor voegsel in één route hebt gedefinieerd, kunt u nog steeds een andere route definiëren met het adres voorvoegsel 10.0.0.0/24. Azure selecteert een route voor verkeer op basis van het voor voegsel van de voor voegsels. Zie [route ring Overview](virtual-networks-udr-overview.md#how-azure-selects-a-route)(Engelstalig) voor meer informatie over hoe Azure routes selecteert.
1. Selecteer een **type van de volgende hop**. Zie [overzicht van route ring](virtual-networks-udr-overview.md)voor een gedetailleerde beschrijving van alle volgende typen hops.
1. Voer een IP-adres in voor het adres van de **volgende hop**. U kunt alleen een adres invoeren als u *virtueel apparaat* hebt geselecteerd voor het **type volgende hop**.
1. Selecteer **OK**.

### <a name="create-a-route---commands"></a>Een route-opdrachten maken

* Azure CLI: [AZ Network Route-Table Route Create](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* Power shell: [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)

## <a name="view-routes"></a>Routes weer geven

Een route tabel bevat nul of meerdere routes. Zie [route ring-overzicht](virtual-networks-udr-overview.md)voor meer informatie over de informatie die wordt weer gegeven wanneer u routes bekijkt.

1. Voer in het zoekvak boven aan de portal *route tabellen* in het zoekvak in. Wanneer **route tabellen** worden weer gegeven in de zoek resultaten, selecteert u deze.
1. Selecteer de route tabel in de lijst waarvoor u routes wilt weer geven.
1. Selecteer **routes** onder **instellingen**.

### <a name="view-routes---commands"></a>Routes weer geven-opdrachten

* Azure CLI: [AZ Network Route-Table Route List](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* Power shell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="view-details-of-a-route"></a>Details van een route weergeven

1. Voer in het zoekvak boven aan de portal *route tabellen* in het zoekvak in. Wanneer **route tabellen** worden weer gegeven in de zoek resultaten, selecteert u deze.
1. Selecteer de route tabel waarvan u de details van een route wilt weer geven.
1. **Routes**selecteren.
1. Selecteer de route waarvan u de details wilt weer geven.

### <a name="view-details-of-a-route---commands"></a>Details van een route-opdrachten weer geven

* Azure CLI: [AZ Network Route-Table Route show](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* Power shell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="change-a-route"></a>Een route wijzigen

1. Voer in het zoekvak boven aan de portal *route tabellen* in het zoekvak in. Wanneer **route tabellen** worden weer gegeven in de zoek resultaten, selecteert u deze.
1. Selecteer de route tabel waarvoor u een route wilt wijzigen.
1. **Routes**selecteren.
1. Selecteer de route die u wilt wijzigen.
1. Wijzig de bestaande instellingen in de nieuwe instellingen en selecteer vervolgens **Opslaan**.

### <a name="change-a-route---commands"></a>Een route-opdrachten wijzigen

* Azure CLI: [AZ Network Route-Table Route update](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* Power shell: [set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig)

## <a name="delete-a-route"></a>Een route verwijderen

1. Voer in het zoekvak boven aan de portal *route tabellen* in het zoekvak in. Wanneer **route tabellen** worden weer gegeven in de zoek resultaten, selecteert u deze.
1. Selecteer de route tabel waarvoor u een route wilt verwijderen.
1. **Routes**selecteren.
1. Selecteer in de lijst met routes de optie **...** aan de rechter kant van de route die u wilt verwijderen.
1. Selecteer **verwijderen**en selecteer vervolgens **Ja**.

### <a name="delete-a-route---commands"></a>Een route-opdrachten verwijderen

* Azure CLI: [AZ Network Route-Table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* Power shell: [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig)

## <a name="view-effective-routes"></a>Efficiënte routes weer geven

De actieve routes voor elke netwerk interface die is gekoppeld aan een virtuele machine zijn een combi natie van route tabellen die u hebt gemaakt, de standaard routes van Azure en alle routes die worden door gegeven vanuit on-premises netwerken via BGP via een virtuele Azure-netwerk gateway. Informatie over de efficiënte routes voor een netwerk interface is handig bij het oplossen van problemen met route ring. U kunt de actieve routes weer geven voor elke netwerk interface die is gekoppeld aan een actieve virtuele machine.

1. Voer in het zoekvak boven aan de Portal de naam in van een virtuele machine waarvoor u de juiste routes wilt weer geven. Als u de naam van een virtuele machine niet weet, voert u *virtuele machines* in het zoekvak in. Wanneer **virtuele machines** worden weer gegeven in de zoek resultaten, selecteert u deze en selecteert u een virtuele machine in de lijst.
1. Selecteer **netwerken** onder **instellingen**.
1. Selecteer de naam van een netwerk interface.
1. Selecteer **efficiënte routes** onder **ondersteuning en probleem oplossing**.
1. Bekijk de lijst met efficiënte routes om te bepalen of de juiste route bestaat voor waar u verkeer wilt door sturen. Meer informatie over de typen van de volgende hop die u in deze lijst ziet in [route ring-overzicht](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Efficiënte routes weer geven-opdrachten

* Azure CLI: [AZ Network NIC show-ingangsdatum-route tabel](/cli/azure/network/nic?view=azure-cli-latest)<br>
* Power shell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="validate-routing-between-two-endpoints"></a>Route ring tussen twee eind punten valideren

U kunt het type van de volgende hop bepalen tussen een virtuele machine en het IP-adres van een andere Azure-resource, een on-premises resource of een bron op internet. Het bepalen van de route ring van Azure is handig bij het oplossen van problemen met route ring. Als u deze taak wilt volt ooien, moet u een bestaande Network Watcher hebben. Als u geen bestaande Network Watcher hebt, maakt u er een door de stappen in [een Network Watcher-exemplaar maken](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)uit te voeren.

1. Voer in het zoekvak boven aan de portal *Network Watcher* in het zoekvak in. Selecteer **Network Watcher** in de zoekresultaten.
1. Selecteer **volgende hop** onder **Diagnostische Hulpprogram ma's voor netwerk**.
1. Selecteer uw **abonnement** en de **resource groep** van de virtuele bron machine waarvan u de route ring wilt valideren.
1. Selecteer de **virtuele machine**, de **netwerk interface** die is gekoppeld aan de virtuele machine en het **bron-IP-adres** dat is toegewezen aan de netwerk interface waarvan u de route ring wilt valideren.
1. Voer het **doel-IP-adres** in waarnaar u de route ring wilt valideren.
1. Selecteer **volgende hop**.
1. Na een korte wacht tijd wordt er informatie weer gegeven waarin u het type van de volgende hop en de ID van de route die het verkeer routeert. Meer informatie over de volgende typen hops die worden weer gegeven in [route ring-overzicht](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Route ring tussen twee eind punten valideren-opdrachten

* Azure CLI: [AZ Network Watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest)<br>
* Power shell: [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop)

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren op route tabellen en routes, moet uw account worden toegewezen aan de rol [netwerk bijdrager](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rol waaraan de juiste acties in de volgende tabel zijn toegewezen:

| Bewerking                                                          |   Naam                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Een route tabel lezen                                    |
| Microsoft.Network/routeTables/write                             |   Een route tabel maken of bijwerken                        |
| Microsoft.Network/routeTables/delete                            |   Een routetabel verwijderen                                  |
| Microsoft.Network/routeTables/join/action                       |   Een routetabel aan een subnet koppelen                   |
| Microsoft.Network/routeTables/routes/read                       |   Een route lezen                                          |
| Microsoft.Network/routeTables/routes/write                      |   Een route maken of bijwerken                              |
| Microsoft.Network/routeTables/routes/delete                     |   Een route verwijderen                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   De doel tabel van de route ophalen voor een netwerk interface |
| Microsoft.Network/networkWatchers/nextHop/action                |   Hiermee wordt de volgende hop van een virtuele machine opgehaald                           |

## <a name="next-steps"></a>Volgende stappen

* Een route tabel maken met behulp van [Power shell](powershell-samples.md) of [Azure cli](cli-samples.md) -voorbeeld scripts of met behulp van Azure [Resource Manager-sjablonen](template-samples.md)<br>
* [Azure-beleid](policy-samples.md) maken en Toep assen voor virtuele netwerken
