---
title: Een Azure Network Watcher-exemplaar maken | Microsoft Documenten
description: Meer informatie over het inschakelen van Network Watcher in een Azure-regio.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 77812a3765a027152c957f6dbb7c9b3811a2278f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191183"
---
# <a name="create-an-azure-network-watcher-instance"></a>Een Azure Network Watcher-exemplaar maken

Network Watcher is een regionale service waarmee u de omstandigheden op netwerkscenarioniveau in, naar en vanuit Azure controleren en diagnosticeren. Met bewaking op scenarioniveau u problemen diagnosticeren bij een end-to-end netwerkniveauweergave. Netwerkdiagnose- en visualisatietools die beschikbaar zijn met Network Watcher helpen u inzicht te krijgen in uw netwerk in Azure, diagnoses en inzichten te krijgen. Network Watcher is ingeschakeld door het maken van een Network Watcher-bron. Met deze bron u gebruikmaken van network watcher-mogelijkheden.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>Network Watcher is automatisch ingeschakeld
Wanneer u een virtueel netwerk in uw abonnement maakt of bijwerkt, wordt Network Watcher automatisch ingeschakeld in de regio van uw virtuele netwerk. Het automatisch inschakelen van Network Watcher heeft geen invloed op uw resources en er worden geen kosten in rekening gebracht.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Opt-out van Network Watcher automatische enablement
Als u zich wilt afmelden voor automatische enablement van Network Watcher, u dit doen door de volgende opdrachten uit te voeren:

> [!WARNING]
> Opt-out van Network Watcher automatische enablement is een permanente verandering. Zodra u zich afmelden, u zich niet aanmelden zonder [contact op te nemen met ondersteuning](https://azure.microsoft.com/support/options/)

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Een netwerkwatcher maken in de portal

Navigeer naar **All Services** > **Networking** > **Network Watcher**. U alle abonnementen selecteren waarvoor u Network Watcher wilt inschakelen. Met deze actie wordt een netwerkwatcher gemaakt in elke regio die beschikbaar is.

![een netwerkwatcher maken](./media/network-watcher-create/figure1.png)

Wanneer u Network Watcher inschakelt via de portal, wordt de naam van het exemplaar Network Watcher automatisch ingesteld op *NetworkWatcher_region_name* waar *region_name* overeenkomt met het Azure-gebied waar de instantie is ingeschakeld. Een Netwerkwatcher die is ingeschakeld in de regio West Central US krijgt bijvoorbeeld de naam *NetworkWatcher_westcentralus*.

De instantie Network Watcher wordt automatisch gemaakt in een resourcegroep met de naam *NetworkWatcherRG*. De resourcegroep wordt gemaakt als deze nog niet bestaat.

Als u de naam van een instantie Network Watcher en de brongroep waarin deze is geplaatst, wilt aanpassen, u Powershell, de Azure CLI, de REST API of ARMClient-methoden gebruiken die worden beschreven in de volgende secties. In elke optie moet de brongroep bestaan voordat u er een Netwerkwatcher in maakt.  

## <a name="create-a-network-watcher-with-powershell"></a>Een netwerkwatcher maken met PowerShell

Voer het volgende voorbeeld uit om een instantie van Network Watcher te maken:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Een netwerkwatcher maken met de Azure CLI

Voer het volgende voorbeeld uit om een instantie van Network Watcher te maken:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Een netwerkwatcher maken met de REST-API

De ARM-client wordt gebruikt om de REST API aan te roepen met PowerShell. De ARMClient is te vinden op chocolatey bij [ARMClient op Chocolatey](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Inloggen met ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>De netwerkwatcher maken

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="delete-a-network-watcher-in-the-portal"></a>Een netwerkwatcher in de portal verwijderen

Navigeer naar **All Services** > **Networking** > **Network Watcher**.

Selecteer het tabblad Overzicht als u er nog niet bent. Gebruik de vervolgkeuzelijst om het abonnement te selecteren waarin u de netwerkwatcher wilt uitschakelen.
Vouw de lijst met regio's voor het gekozen abonnement uit door op de pijl te klikken. Gebruik voor elk gegeven de 3 puntjes aan de rechterkant om toegang te krijgen tot het contextmenu.
Klik op 'Netwerkwatcher uitschakelen' om te beginnen met uitschakelen. U wordt gevraagd deze stap te bevestigen. Klik op Ja om door te gaan.
Op het portaal moet u dit individueel doen voor elke regio in elk abonnement.


## <a name="delete-a-network-watcher-with-powershell"></a>Een netwerkwatcher verwijderen met PowerShell

Voer het volgende voorbeeld uit om een instantie van Network Watcher te verwijderen:

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location westcentralus
New-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG -Location westcentralus
Remove-AzNetworkWatcher -Name NetworkWatcher_westcentralus -ResourceGroup NetworkWatcherRG
```

## <a name="next-steps"></a>Volgende stappen

Nu u een exemplaar van Network Watcher hebt, u meer te weten komen over de beschikbare functies:

* [Topologie](network-watcher-topology-overview.md)
* [Pakketopname](network-watcher-packet-capture-overview.md)
* [IP-stroomverificatie](network-watcher-ip-flow-verify-overview.md)
* [Volgende hop](network-watcher-next-hop-overview.md)
* [Beveiligingsgroepweergave](network-watcher-security-group-view-overview.md)
* [NSG-stroomlogboekregistratie](network-watcher-nsg-flow-logging-overview.md)
* [Probleemoplossing voor virtuele netwerkgateway](network-watcher-troubleshoot-overview.md)
