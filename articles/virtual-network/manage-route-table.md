---
title: Een Azure-routetabel maken, wijzigen of verwijderen
titlesuffix: Azure Virtual Network
description: Meer informatie over het maken, wijzigen of verwijderen van een routetabel.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: 79310ddf121d6ada10755b198b515fdc9c1114d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247058"
---
# <a name="create-change-or-delete-a-route-table"></a>Een routetabel maken, wijzigen of verwijderen

Azure routeert automatisch verkeer tussen Azure-subnetten, virtuele netwerken en on-premises netwerken. Als u een van de standaardroutering van Azure wilt wijzigen, u dit doen door een routetabel te maken. Als u nieuw bent in routering in virtuele netwerken, u er meer over te weten komen in [virtuele netwerkverkeerroutering](virtual-networks-udr-overview.md) of door een zelfstudie in te [vullen.](tutorial-create-route-table-portal.md)

## <a name="before-you-begin"></a>Voordat u begint

Als u er geen hebt, stelt u een Azure-account in met een actief abonnement. [Maak gratis een account aan.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Voer vervolgens een van deze taken uit voordat u stappen start in een gedeelte van dit artikel:

- **Portalgebruikers:** Meld u aan bij de [Azure-portal](https://portal.azure.com) met uw Azure-account.

- **PowerShell-gebruikers**: Voer de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/powershell)of voer PowerShell uit vanaf uw computer. Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Zoek op het tabblad Azure Cloud Shell de vervolgkeuzelijst **Voor de omgeving selecteren** en kies **PowerShell** als deze nog niet is geselecteerd.

    Als u PowerShell lokaal gebruikt, gebruikt u Azure PowerShell-moduleversie 1.0.0 of hoger. Voer `Get-Module -ListAvailable Az.Network` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Ook `Connect-AzAccount` worden uitgevoerd om een verbinding met Azure te maken.

- **CLI-gebruikers (Azure Command-line interface)** uitvoeren: voer de opdrachten uit in de [Azure Cloud Shell](https://shell.azure.com/bash)of voer de CLI uit vanaf uw computer. Gebruik Azure CLI-versie 2.0.31 of hoger als u de Azure CLI lokaal uitvoert. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli). Ook `az login` worden uitgevoerd om een verbinding met Azure te maken.

Het account waarmee u zich aanmeldt of verbinding maakt met Azure, moet worden toegewezen aan de [rol netwerkbijdrage raan](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties zijn toegewezen die worden vermeld in [Machtigingen](#permissions).

## <a name="create-a-route-table"></a>Een routetabel maken

Er is een limiet aan het aantal routetabellen dat u per Azure-locatie en -abonnement maken. Zie [Netwerklimieten voor](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)meer informatie - Azure Resource Manager .

1. Selecteer in het menu [azure portal](https://portal.azure.com) of op **de** startpagina de optie Een **bron maken**.

1. Voer in het zoekvak de *tabel Route*in . Wanneer **de tabel Route** wordt weergegeven in de zoekresultaten, selecteert u deze.

1. Selecteer op de pagina **Routetabel** de optie **Maken**.

1. Ga als volgt te werk in het dialoogvenster **Routetabel maken:**

    1. Voer een **naam** in voor de routetabel.
    1. Kies uw **abonnement**.
    1. Kies een bestaande **resourcegroep** of selecteer **Nieuw maken** om een nieuwe resourcegroep te maken.
    1. Kies een **Locatie**.
    1. Als u van plan bent de routetabel te koppelen aan een subnet in een virtueel netwerk dat via een VPN-gateway is verbonden met uw on-premises netwerk, en u uw on-premises routes niet wilt propageren naar de netwerkinterfaces in het subnet, stelt u de verspreiding van **de virtuele netwerkgatewayroute in** naar **Uitgeschakeld.**

1. Selecteer **Maken** om uw nieuwe routetabel te maken.

### <a name="create-route-table---commands"></a>Routetabel maken - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Routetabellen weergeven

Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Routetabellen zoeken **Route tables**en selecteren . De routetabellen die in uw abonnement bestaan, worden weergegeven.

### <a name="view-route-table---commands"></a>Routetabel weergeven - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [routetabellijst az-netwerk](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Details van een routetabel weergeven

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Routetabellen zoeken **Route tables**en selecteren .

1. Kies in de lijst met routetabel de routetabel waarvoor u details wilt weergeven.

1. Bekijk op de routetabelpagina onder **Instellingen**de **routes** in de routetabel of de **subnetten** waaraan de routetabel is gekoppeld.

Zie de volgende gegevens voor meer informatie over algemene Azure-instellingen:

- [Activiteitenlogboek](../azure-monitor/platform/platform-logs-overview.md)
- [Toegangsbeheer (IAM)](../role-based-access-control/overview.md)
- [Tags](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Sloten](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Automation-script](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Details van de routetabel weergeven - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [az-netwerk route-tabel show](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Een routetabel wijzigen

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Routetabellen zoeken **Route tables**en selecteren .

1. Kies in de lijst met routetabel de routetabel die u wilt wijzigen.

De meest voorkomende wijzigingen zijn het [toevoegen](#create-a-route) van routes, [het verwijderen van](#delete-a-route) routes, het koppelen [van](#associate-a-route-table-to-a-subnet) routetabellen aan subnetten [of](#dissociate-a-route-table-from-a-subnet) het scheiden van routetabellen van subnetten.

### <a name="change-a-route-table---commands"></a>Een routetabel wijzigen - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [update az-netwerkroutetabel](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-azroutetabel](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Een routetabel aan een subnet koppelen

U optioneel een routetabel koppelen aan een subnet. Een routetabel kan worden gekoppeld aan nul of meer subnetten. Omdat routetabellen niet zijn gekoppeld aan virtuele netwerken, moet u een routetabel koppelen aan elk subnet waaraan u de routetabel wilt koppelen. Azure leidt al het verkeer dat het subnet verlaat op basis van routes die u hebt gemaakt in routetabellen, [standaardroutes](virtual-networks-udr-overview.md#default)en routes die worden gepropageerd vanuit een on-premises netwerk, als het virtuele netwerk is verbonden met een Azure virtual network gateway (ExpressRoute of VPN). U kunt routetabellen alleen koppelen aan subnetten in virtuele netwerken die zich op dezelfde Azure-locatie en in hetzelfde abonnement bevinden als de routetabel.

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Zoeken naar virtuele netwerken en selecteer **deze.**

1. Kies in de lijst met virtuele netwerken het virtuele netwerk dat het subnet bevat waaraan u een routetabel wilt koppelen.

1. Kies **Subnetten**in de menubalk van het virtuele netwerk .

1. Selecteer het subnet waaraan u de routetabel wilt koppelen.

1. Kies **in de tabel Route**de routetabel die u aan het subnet wilt koppelen.

1. Selecteer **Opslaan**.

Als uw virtuele netwerk is verbonden met een Azure VPN-gateway, koppelt u geen routetabel aan het [gatewaysubnet](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) met een route met een bestemming van *0.0.0.0/0*. Als u dit wel doet, functioneert de gateway mogelijk niet juist. Zie Routering van [virtueel netwerkverkeer](virtual-networks-udr-overview.md#default-route)voor meer informatie over het gebruik van *0.0.0.0/0* in een route.

### <a name="associate-a-route-table---commands"></a>Een routetabel koppelen - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Een routetabel ontkoppelen van een subnet

Wanneer u een routetabel van een subnet scheidt, leidt Azure verkeer op basis van de [standaardroutes](virtual-networks-udr-overview.md#default).

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw virtuele netwerk te beheren. Zoeken naar virtuele netwerken en selecteer **deze.**

1. Kies in de lijst met virtuele netwerken het virtuele netwerk dat het subnet bevat waarvan u een routetabel wilt scheiden.

1. Kies **Subnetten**in de menubalk van het virtuele netwerk .

1. Selecteer het subnet waaruit u de routetabel wilt scheiden.

1. Kies **Geen**in **de tabel Route**.

1. Selecteer **Opslaan**.

### <a name="dissociate-a-route-table---commands"></a>Een routetabel scheiden - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Een routetabel verwijderen

U een routetabel die is gekoppeld aan subnetten niet verwijderen. [Koppel een routetabel los](#dissociate-a-route-table-from-a-subnet) van alle subnetten voordat u deze probeert te verwijderen.

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw routetabellen te beheren. Routetabellen zoeken **Route tables**en selecteren .

1. Kies in de lijst met routetabel de routetabel die u wilt verwijderen.

1. Selecteer **Verwijderen**en selecteer **Opein in** het bevestigingsdialoogvenster.

### <a name="delete-a-route-table---commands"></a>Een routetabel verwijderen - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [router-tabel az-netwerk verwijderen](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Tabel verwijderen-azroutetabel](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Een route maken

Er is een limiet aan het aantal routes per routetabel dat per Azure-locatie en -abonnement kan worden gemaakt. Zie [Netwerklimieten voor](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)meer informatie - Azure Resource Manager .

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw routetabellen te beheren. Routetabellen zoeken **Route tables**en selecteren .

1. Kies in de lijst met routetabel de routetabel waaraan u een route wilt toevoegen.

1. Kies Routes**toevoegen**in de menubalk **van** > de routetabel .

1. Voer een unieke **routenaam** in voor de route in de routetabel.

1. Voer het **adresvoorvoegsel**in CIDR-notatie (Classless Inter-Domain Routing) in waarnaar u verkeer wilt leiden. Het voorvoegsel kan niet worden gedupliceerd in meer dan één route binnen de routetabel, hoewel het voorvoegsel zich in een ander voorvoegsel kan bevinden. Als u bijvoorbeeld *10.0.0.0/16* als voorvoegsel in één route hebt gedefinieerd, u nog steeds een andere route definiëren met het voorvoegsel *10.0.0.0/22.* Azure selecteert een route voor verkeer op basis van de langste voorvoegselovereenkomst. Zie [Hoe Azure een route selecteert](virtual-networks-udr-overview.md#how-azure-selects-a-route)voor meer informatie.

1. Kies een **next hoptype**. Zie [Virtuele netwerkverkeerroutering](virtual-networks-udr-overview.md)voor meer informatie over volgende hoptypen.

1. Als u een **next hop-type** **virtueel toestel hebt**gekozen, voert u een IP-adres in voor volgende **hopadres**.

1. Selecteer **OK**.

### <a name="create-a-route---commands"></a>Een route maken - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Routes bekijken

Een routetabel bevat nul of meer routes. Zie [Virtuele netwerkverkeerroutering](virtual-networks-udr-overview.md)voor meer informatie over de informatie die wordt vermeld bij het weergeven van routes.

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw routetabellen te beheren. Routetabellen zoeken **Route tables**en selecteren .

1. Kies in de lijst met routetabel de routetabel waarvoor u routes wilt weergeven.

1. Kies **Routes** om de lijst met routes te bekijken in de menubalk van de routetabel.

### <a name="view-routes---commands"></a>Routes weergeven - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [routeroutelijst az-netwerk](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Details van een route weergeven

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw routetabellen te beheren. Routetabellen zoeken **Route tables**en selecteren .

1. Kies in de lijst met routetabel de routetabel met de route waarvoor u de details wilt weergeven.

1. Kies **Routes** om de lijst met routes te bekijken in de menubalk van de routetabel.

1. Selecteer de route waarvan u de details wilt weergeven.

### <a name="view-details-of-a-route---commands"></a>Details van een route weergeven - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [route-tabelroute az-netwerk tonen](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Een route wijzigen

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw routetabellen te beheren. Routetabellen zoeken **Route tables**en selecteren .

1. Kies in de lijst met routetabel de routetabel met de route die u wilt wijzigen.

1. Kies **Routes** om de lijst met routes te bekijken in de menubalk van de routetabel.

1. Kies de route die u wilt wijzigen.

1. Wijzig bestaande instellingen in hun nieuwe instellingen en selecteer **Opslaan**.

### <a name="change-a-route---commands"></a>Een route wijzigen - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [update az-netwerkrouteroute](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Een route verwijderen

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw routetabellen te beheren. Routetabellen zoeken **Route tables**en selecteren .

1. Kies in de lijst met routetabel de routetabel met de route die u wilt verwijderen.

1. Kies **Routes** om de lijst met routes te bekijken in de menubalk van de routetabel.

1. Kies de route die u wilt verwijderen.

1. Selecteer **Verwijderen**en selecteer **Vervolgens Ja** in het bevestigingsdialoogvenster.

### <a name="delete-a-route---commands"></a>Een route verwijderen - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [routerouteroute van AZ-netwerk verwijderen](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Bekijk effectieve routes

De effectieve routes voor elke vm-gekoppelde netwerkinterface zijn een combinatie van routetabellen die u hebt gemaakt, de standaardroutes van Azure en alle routes die via het Border Gateway Protocol (BGP) via een Virtueel Azure-netwerk worden gepropageerd vanuit on-premises netwerken Gateway. Inzicht in de effectieve routes voor een netwerkinterface is handig bij het oplossen van routeringsproblemen. U de effectieve routes bekijken voor elke netwerkinterface die is gekoppeld aan een draaiende VM.

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw VM's te beheren. Zoeken naar virtuele machines en selecteer **deze**.

1. Kies in de lijst met virtuele machines de VM waarvoor u effectieve routes wilt weergeven.

1. Kies **netwerken**in de menubalk van de VM .

1. Selecteer de naam van een netwerkinterface.

1. Selecteer in de menubalk van de netwerkinterface de optie **Effectieve routes**.

1. Bekijk de lijst met effectieve routes om te zien of de juiste route bestaat voor waar u verkeer naartoe wilt leiden. Meer informatie over volgende hoptypen die u in deze lijst ziet in [virtuele netwerkverkeerroutering](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Effectieve routes weergeven - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [az netwerk nic show-effective-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Routering tussen twee eindpunten valideren

U het volgende hoptype bepalen tussen een virtuele machine en het IP-adres van een andere Azure-bron, een on-premises bron of een bron op internet. Het bepalen van de routering van Azure is handig bij het oplossen van routeringsproblemen. Om deze taak te voltooien, moet u een bestaande netwerkwatcher hebben. Als u geen bestaande netwerkwatcher hebt, maakt u er een door de stappen in [een exemplaar Netwerkwatcher maken](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json)te voltooien.

1. Ga naar de [Azure-portal](https://portal.azure.com) om uw netwerkwatchers te beheren. Zoeken naar en selecteer **Network Watcher**.

1. Kies volgende hop in de menubalk **van**netwerkwatcher .

1. In de **Network Watcher | Volgende hoppagina:**

    1. Kies uw **abonnement** en de **resourcegroep** van de bron-VM waarvan u de routering wilt valideren.

    1. Kies de **virtuele machine** en de **netwerkinterface** die is gekoppeld aan de vm.
    
    1. Voer een **bron-IP-adres** in dat is toegewezen aan de netwerkinterface waarvan u de routering wilt valideren.

    1. Voer een **doel-IP-adres** in waarnaar u de routering wilt valideren.

1. Selecteer **Volgende hop**.

Na een korte wachttijd geeft Azure aan dat het volgende hoptype en de id van de route die het verkeer heeft omgeleid. Meer informatie over volgende hoptypen die u terugziet in [virtuele netwerkverkeerroutering](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Routering tussen twee eindpunten valideren - opdrachten

| Hulpprogramma | Opdracht |
| ---- | ------- |
| Azure-CLI | [az network watcher show-next-hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Machtigingen

Als u taken wilt uitvoeren op routetabellen en routes, moet uw account worden toegewezen aan de [rol netwerkbijdrage raanof](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) of aan een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) waaraan de juiste acties in de volgende tabel zijn toegewezen:

| Actie                                                          |   Name                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTabellen/lezen                              |   Een routetabel lezen                                    |
| Microsoft.Network/routeTabellen/schrijven                             |   Een routetabel maken of bijwerken                        |
| Microsoft.Network/routeTabellen/verwijderen                            |   Een routetabel verwijderen                                  |
| Microsoft.Network/routeTabellen/join/actie                       |   Een routetabel aan een subnet koppelen                   |
| Microsoft.Network/routeTabellen/routes/lezen                       |   Een route lezen                                          |
| Microsoft.Network/routeTabellen/routes/schrijven                      |   Een route maken of bijwerken                              |
| Microsoft.Network/routeTabellen/routes/verwijderen                     |   Een route verwijderen                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   De effectieve routetabel voor een netwerkinterface downloaden |
| Microsoft.Network/networkWatchers/nextHop/actie                |   Krijgt de volgende hop van een VM                           |

## <a name="next-steps"></a>Volgende stappen

- Een routetabel maken met [PowerShell-](powershell-samples.md) of [Azure CLI-voorbeeldscripts](cli-samples.md) of Azure [Resource Manager-sjablonen](template-samples.md)
- [Azure-beleid](policy-samples.md) maken en toepassen voor virtuele netwerken
