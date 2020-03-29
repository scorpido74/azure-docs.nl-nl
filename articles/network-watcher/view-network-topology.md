---
title: Azure-topologie voor virtuele netwerken weergeven | Microsoft Documenten
description: Meer informatie over het weergeven van de bronnen in een virtueel netwerk en de relaties tussen de resources.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2018
ms.author: damendo
ms.openlocfilehash: 675919db55932d3ccc04fd5397f6f673832b4900
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840567"
---
# <a name="view-the-topology-of-an-azure-virtual-network"></a>De topologie van een virtueel Azure-netwerk weergeven

In dit artikel leert u hoe u resources in een virtueel Microsoft Azure-netwerk en de relaties tussen de resources weergeven. Een virtueel netwerk bevat bijvoorbeeld subnetten. Subnetten bevatten bronnen, zoals Azure Virtual Machines (VM). VM's hebben een of meer netwerkinterfaces. Aan elk subnet kan een netwerkbeveiligingsgroep en een routetabel zijn gekoppeld. Met de topologiemogelijkheid van Azure Network Watcher u alle bronnen in een virtueel netwerk, de resources die zijn gekoppeld aan resources in een virtueel netwerk en de relaties tussen de bronnen weergeven.

U de [Azure-portal](#azure-portal), de [Azure CLI](#azure-cli)of [PowerShell](#powershell) gebruiken om een topologie weer te geven.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="view-topology---azure-portal"></a><a name = "azure-portal"></a>Topologie weergeven - Azure-portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een account met de benodigde [machtigingen.](required-rbac-permissions.md)
2. Selecteer Alle **services**op de linkerbovenhoek van het portaal .
3. Voer *netwerkwatcher*in het filtervak **Alle services** in. Selecteer **Network Watcher** in de resultaten.
4. Selecteer **Topologie**. Voor het genereren van een topologie is een netwerkwatcher in dezelfde regio nodig waarvoor het virtuele netwerk waarin u de topologie wilt genereren, bestaat. Als u geen netwerkwatcher hebt ingeschakeld in de regio waarvoor het virtuele netwerk waarvoor u een topologie wilt genereren, zich bevindt, worden netwerkwatchers automatisch voor u gemaakt in alle regio's. De netwerkwatchers worden gemaakt in een resourcegroep met de naam **NetworkWatcherRG**.
5. Selecteer een abonnement, de brongroep van een virtueel netwerk waarvoor u de topologie wilt weergeven en selecteer vervolgens het virtuele netwerk. In de volgende afbeelding wordt een topologie weergegeven voor een virtueel netwerk met de naam *MyVnet*, in de resourcegroep *myresourcegroep:*

    ![Topologie weergeven](./media/view-network-topology/view-topology.png)

    Zoals u zien in de vorige afbeelding, het virtuele netwerk bevat drie subnetten. In één subnet zit een VM. De VM heeft een netwerkinterface eraan verbonden en er een openbaar IP-adres aan gekoppeld. De andere twee subnetten hebben een routetabel die eraan is gekoppeld. Elke routetabel bevat twee routes. Aan één subnet is een netwerkbeveiligingsgroep gekoppeld. Topologie-informatie wordt alleen weergegeven voor bronnen die:
    
    - Binnen dezelfde resourcegroep en -regio als het virtuele *myVnet-netwerk.* Een netwerkbeveiligingsgroep die bestaat in een andere resourcegroep dan *MyResourceGroup,* wordt bijvoorbeeld niet weergegeven, zelfs niet als de netwerkbeveiligingsgroep is gekoppeld aan een subnet in het virtuele *MyVnet-netwerk.*
    - Binnen of gekoppeld aan resources binnen het *virtuele myVnet-netwerk.* Een netwerkbeveiligingsgroep die niet is gekoppeld aan een subnet of netwerkinterface in het virtuele *myVnet-netwerk,* wordt bijvoorbeeld niet weergegeven, zelfs niet als de netwerkbeveiligingsgroep zich in de brongroep *MyResourceGroup* bevindt.

   De topologie in de afbeelding is voor het virtuele netwerk dat is gemaakt na het implementeren van het **routeverkeer via een voorbeeld van een netwerkscript voor virtuele apparaten,** dat u implementeren met behulp van azure [CLI](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)of [PowerShell](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

6. Selecteer **Topologie downloaden** om de afbeelding te downloaden als bewerkbaar bestand in svg-indeling.

De bronnen in het diagram zijn een subset van de netwerkcomponenten in het virtuele netwerk. Terwijl bijvoorbeeld een netwerkbeveiligingsgroep wordt weergegeven, worden de beveiligingsregels daarin niet weergegeven in het diagram. Hoewel niet gedifferentieerd in het diagram, de lijnen vertegenwoordigen een van de twee relaties: *Containment* of *geassocieerd*. Als u de volledige lijst met resources in het virtuele netwerk en het type relatie tussen de resources wilt weergeven, genereert u de topologie met [PowerShell](#powershell) of azure [CLI](#azure-cli).

## <a name="view-topology---azure-cli"></a><a name = "azure-cli"></a>Topologie weergeven - Azure CLI

U de opdrachten uitvoeren in de volgende stappen:
- Selecteer in de Azure Cloud Shell **Probeer het** rechtsboven in een opdracht. De Azure Cloud Shell is een gratis interactieve shell met gemeenschappelijke Azure-hulpprogramma's die vooraf zijn geïnstalleerd en geconfigureerd om te gebruiken met uw account.
- Door de CLI vanaf uw computer uit te voeren. Als u de CLI vanaf uw computer uitvoert, vereisen stappen in dit artikel de Azure CLI-versie 2.0.31 of hoger. Voer `az --version` uit om te kijken welke versie is geïnstalleerd. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). Als u de Azure CLI lokaal uitvoert, `az login` moet u ook worden uitgevoerd om een verbinding met Azure te maken.

Het account dat u gebruikt, moet over de vereiste [machtigingen](required-rbac-permissions.md)beschikken.

1. Als u al een netwerkwatcher in dezelfde regio hebt als het virtuele netwerk waarvoor u een topologie wilt maken, gaat u naar stap 3. Maak een resourcegroep met een netwerkwatcher met [de AZ-groep.](/cli/azure/group) In het volgende voorbeeld wordt de resourcegroep in de *eastus-regio* gezoend:

    ```azurecli-interactive
    az group create --name NetworkWatcherRG --location eastus
    ```

2. Maak een netwerkwatcher met [az-netwerkwatcher configureren](/cli/azure/network/watcher#az-network-watcher-configure). In het volgende voorbeeld wordt een netwerkwatcher in de *eastusregio* gezoend:

    ```azurecli-interactive
    az network watcher configure \
      --resource-group NetworkWatcherRG \
      --location eastus \
      --enabled true
    ```

3. Bekijk de topologie met [az network watcher show-topology](/cli/azure/network/watcher#az-network-watcher-show-topology). In het volgende voorbeeld wordt de topologie voor een resourcegroep met de naam *MyResourceGroup weergegeven:*

    ```azurecli-interactive
    az network watcher show-topology --resource-group MyResourceGroup
    ```

    Topologie-informatie wordt alleen geretourneerd voor resources die zich binnen dezelfde resourcegroep bevinden als de resourcegroep *MyResourceGroup* en dezelfde regio als de netwerkwatcher. Een netwerkbeveiligingsgroep die bestaat in een andere resourcegroep dan *MyResourceGroup,* wordt bijvoorbeeld niet weergegeven, zelfs niet als de netwerkbeveiligingsgroep is gekoppeld aan een subnet in het virtuele *MyVnet-netwerk.*

   Meer informatie over de relaties en [eigenschappen](#properties) in de geretourneerde uitvoer. Als u geen bestaand virtueel netwerk hebt om een topologie voor te bekijken, u er een maken met het voorbeeld routeverkeer via een voorbeeld [van een netwerkscript voor virtuele apparaten.](../virtual-network/scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) Als u een diagram van de topologie wilt weergeven en deze wilt downloaden in een bewerkbaar bestand, gebruikt u de [portal](#azure-portal).

## <a name="view-topology---powershell"></a><a name = "powershell"></a>Topologie weergeven - PowerShell

U de opdrachten uitvoeren in de volgende stappen:
- Selecteer in de Azure Cloud Shell **Probeer het** rechtsboven in een opdracht. De Azure Cloud Shell is een gratis interactieve shell met gemeenschappelijke Azure-hulpprogramma's die vooraf zijn geïnstalleerd en geconfigureerd om te gebruiken met uw account.
- Door PowerShell vanaf uw computer uit te voeren. Als u PowerShell vanaf uw computer uitvoert, `Az` vereist dit artikel de Azure PowerShell-module. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

Het account dat u gebruikt, moet over de vereiste [machtigingen](required-rbac-permissions.md)beschikken.

1. Als u al een netwerkwatcher in dezelfde regio hebt als het virtuele netwerk waarvoor u een topologie wilt maken, gaat u naar stap 3. Maak een resourcegroep met een netwerkwatcher met [Nieuw-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). In het volgende voorbeeld wordt de resourcegroep in de *eastus-regio* gezoend:

    ```azurepowershell-interactive
    New-AzResourceGroup -Name NetworkWatcherRG -Location EastUS
    ```

2. Maak een netwerk watcher met [New-AzNetworkWatcher](/powershell/module/az.network/new-aznetworkwatcher). In het volgende voorbeeld wordt een netwerkwatcher in de eastusregio gezoend:

    ```azurepowershell-interactive
    New-AzNetworkWatcher `
      -Name NetworkWatcher_eastus `
      -ResourceGroupName NetworkWatcherRG
    ```

3. Een voorbeeld van Network Watcher ophalen met [Get-AzNetworkWatcher](/powershell/module/az.network/get-aznetworkwatcher). In het volgende voorbeeld wordt een netwerkwatcher in de regio Oost-VS opgehaald:

    ```azurepowershell-interactive
    $nw = Get-AzResource `
      | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "EastUS" }
    $networkWatcher = Get-AzNetworkWatcher `
      -Name $nw.Name `
      -ResourceGroupName $nw.ResourceGroupName
    ```

4. Haal een topologie op met [Get-AzNetworkWatcherTopology](/powershell/module/az.network/get-aznetworkwatchertopology). In het volgende voorbeeld wordt een topologie voor een virtueel netwerk opgehaald in de resourcegroep met de naam *MyResourceGroup:*

    ```azurepowershell-interactive
    Get-AzNetworkWatcherTopology `
      -NetworkWatcher $networkWatcher `
      -TargetResourceGroupName MyResourceGroup
    ```

   Topologie-informatie wordt alleen geretourneerd voor resources die zich binnen dezelfde resourcegroep bevinden als de resourcegroep *MyResourceGroup* en dezelfde regio als de netwerkwatcher. Een netwerkbeveiligingsgroep die bestaat in een andere resourcegroep dan *MyResourceGroup,* wordt bijvoorbeeld niet weergegeven, zelfs niet als de netwerkbeveiligingsgroep is gekoppeld aan een subnet in het virtuele *MyVnet-netwerk.*

   Meer informatie over de relaties en [eigenschappen](#properties) in de geretourneerde uitvoer. Als u geen bestaand virtueel netwerk hebt om een topologie voor te bekijken, u er een maken met het voorbeeld routeverkeer via een voorbeeld [van een netwerkscript voor virtuele apparaten.](../virtual-network/scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) Als u een diagram van de topologie wilt weergeven en deze wilt downloaden in een bewerkbaar bestand, gebruikt u de [portal](#azure-portal).

## <a name="relationships"></a>Relaties

Alle resources die in een topologie worden geretourneerd, hebben een van de volgende typen relaties met een andere resource:

| Relatietype | Voorbeeld                                                                                                |
| ---               | ---                                                                                                    |
| Containment       | Een virtueel netwerk bevat een subnet. Een subnet bevat een netwerkinterface.                            |
| Bijbehorend        | Een netwerkinterface is gekoppeld aan een VM. Een openbaar IP-adres is gekoppeld aan een netwerkinterface. |

## <a name="properties"></a>Eigenschappen

Alle resources die in een topologie worden geretourneerd, hebben de volgende eigenschappen:

- **Naam**: De naam van de resource
- **Id:** De URI van de resource.
- **Locatie:** het Azure-gebied waarin de bron bestaat.
- **Verenigingen**: Een lijst van associaties met het genoemde object. Elke vereniging heeft de volgende eigenschappen:
    - **AssociationType**: verwijst naar de relatie tussen het onderliggende object en de bovenliggende. Geldige waarden zijn *Bevat* of *Gekoppeld*.
    - **Naam:** de naam van de bron waarnaar wordt verwezen.
    - **ResourceId**: - De URI van de resource waarnaar in de koppeling wordt verwezen.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [diagnosticeren van een probleem met netwerkverkeerfilters van of naar een vm](diagnose-vm-network-traffic-filtering-problem.md) met behulp van de IP-stroomverificatiemogelijkheid van Network Watcher
- Meer informatie over het [diagnosticeren van een probleem met het routeren van netwerkverkeer van een vm](diagnose-vm-network-routing-problem.md) met behulp van de volgende hopcapaciteit van Network Watcher
