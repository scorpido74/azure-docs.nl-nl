---
title: Azure VNet-gatewayen en -verbindingen oplossen - Azure PowerShell
titleSuffix: Azure Network Watcher
description: Op deze pagina wordt uitgelegd hoe u de Azure Network Watcher-probleemoplossing powershell-cmdlet gebruiken
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: damendo
ms.openlocfilehash: 0723ddc9b0e2f15d5c8e51c96d51f58f1313493a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673655"
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Problemen met virtuele netwerkgateway en -verbindingen oplossen met Azure Network Watcher PowerShell

> [!div class="op_single_selector"]
> - [Portal](diagnose-communication-problem-between-networks.md)
> - [Powershell](network-watcher-troubleshoot-manage-powershell.md)
> - [Azure-CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST-API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher biedt veel mogelijkheden als het gaat om het begrijpen van uw netwerkbronnen in Azure. Een van deze mogelijkheden is het oplossen van problemen met resources. Resourceprobleemoplossing kan worden aangeroepen via de portal, PowerShell, CLI of REST API. Wanneer network watcher wordt opgeroepen, inspecteert hij de status van een Virtual Network Gateway of een Connection en retourneert het zijn bevindingen.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voordat u begint

In dit scenario wordt ervan uitgegaan dat u de stappen in [Een netwerkwatcher maken](network-watcher-create.md) al hebt gevolgd om een netwerkwatcher te maken.

Voor een lijst met ondersteunde gatewaytypen [u ondersteunde gatewaytypen bezoeken.](network-watcher-troubleshoot-overview.md#supported-gateway-types)

## <a name="overview"></a>Overzicht

Resourceprobleemoplossing biedt de mogelijkheid om problemen op te lossen die zich voordoen bij virtuele netwerkgateways en -verbindingen. Wanneer een aanvraag wordt ingediend voor het oplossen van problemen met resources, worden logboeken opgevraagd en geïnspecteerd. Wanneer de inspectie is voltooid, worden de resultaten geretourneerd. Aanvragen voor het oplossen van problemen met resources zijn langlopende aanvragen, wat meerdere minuten kan duren om een resultaat te retourneren. De logboeken van probleemoplossing worden opgeslagen in een container op een opgegeven opslagaccount.

## <a name="retrieve-network-watcher"></a>Netwerkwatcher ophalen

De eerste stap is het ophalen van de instantie Network Watcher. De `$networkWatcher` variabele wordt `Start-AzNetworkWatcherResourceTroubleshooting` in stap 4 doorgegeven aan de cmdlet.

```powershell
$networkWatcher = Get-AzNetworkWatcher -Location "WestCentralUS" 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Een verbinding met een virtuele netwerkgateway ophalen

In dit voorbeeld wordt het oplossen van resources uitgevoerd op een verbinding. U het ook een Virtual Network Gateway doorgeven.

```powershell
$connection = Get-AzVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Een opslagaccount maken

Resourceprobleemoplossing retourneert gegevens over de status van de bron, het slaat ook logboeken op naar een opslagaccount dat moet worden gecontroleerd. In deze stap maken we een opslagaccount aan, als er een bestaand opslagaccount bestaat, u het gebruiken.

```powershell
$sa = New-AzStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
Set-AzCurrentStorageAccount -ResourceGroupName $sa.ResourceGroupName -Name $sa.StorageAccountName
$sc = New-AzStorageContainer -Name logs
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Probleemoplossing voor Netwerkwatcher-bronnen uitvoeren

U lost resources `Start-AzNetworkWatcherResourceTroubleshooting` op met de cmdlet. We passeren de cmdlet het object Network Watcher, de id van de Connection of Virtual Network Gateway, de opslagaccount-id en het pad om de resultaten op te slaan.

> [!NOTE]
> De `Start-AzNetworkWatcherResourceTroubleshooting` cmdlet is lang lopend en kan enkele minuten duren.

```powershell
Start-AzNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)$($sc.name)"
```

Zodra u de cmdlet uitvoert, bekijkt Network Watcher de bron om de status te verifiëren. Het retourneert de resultaten naar de shell en slaat logboeken van de resultaten op in het opgegeven opslagaccount.

## <a name="understanding-the-results"></a>Inzicht in de resultaten

De actietekst bevat algemene richtlijnen voor het oplossen van het probleem. Als er actie kan worden ondernomen voor het probleem, wordt een koppeling voorzien van aanvullende richtlijnen. In het geval dat er geen aanvullende richtlijnen zijn, biedt het antwoord de url om een ondersteuningsaanvraag te openen.  Ga voor meer informatie over de eigenschappen van het antwoord en wat er is inbegrepen naar [Het Overzicht problemen met problemen met netwerkwatcher](network-watcher-troubleshoot-overview.md)

Raadpleeg aan [de slag met Azure Blob-opslag met .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)voor instructies voor het downloaden van bestanden uit azure-opslagaccounts. Een ander hulpmiddel dat kan worden gebruikt is Storage Explorer. Meer informatie over Storage Explorer vindt u hier via de volgende link: [Storage Explorer](https://storageexplorer.com/)

## <a name="next-steps"></a>Volgende stappen

Zie [Netwerkbeveiligingsgroepen beheren](../virtual-network/manage-network-security-group.md) om de netwerkbeveiligingsgroep en beveiligingsregels op te sporen die mogelijk in het geding zijn, als de instellingen die vpn-connectiviteit stoppen, zijn gewijzigd.
