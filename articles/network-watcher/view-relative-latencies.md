---
title: Relatieve latencies voor Azure-regio's weergeven vanaf specifieke locaties | Microsoft Documenten
description: Meer informatie over het weergeven van relatieve latencies tussen internetproviders naar Azure-regio's vanaf specifieke locaties.
services: network-watcher
documentationcenter: ''
author: damendo
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2017
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: c3a85de3a201a89d6d9500e4f4b2df9e667e3537
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840533"
---
# <a name="view-relative-latency-to-azure-regions-from-specific-locations"></a>De relatieve latentie naar Azure-regio's vanuit specifieke locatie weergeven

> [!WARNING]
> Deze functie is momenteel in preview en nog steeds getest op stabiliteit.

In deze zelfstudie leert u hoe u de Azure [Network Watcher-service](network-watcher-monitoring-overview.md) gebruiken om te bepalen in welke Azure-regio u uw toepassing of service wilt implementeren op basis van de demografische gegevens van uw gebruiker. Bovendien u het gebruiken om de verbindingen van serviceproviders met Azure te evalueren.  
        

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-network-watcher"></a>Een netwerkwatcher maken

Als u al een netwerkwatcher in ten minste één [Azure-regio hebt,](https://azure.microsoft.com/regions)u de taken in deze sectie overslaan. Maak een resourcegroep voor de netwerkwatcher. In dit voorbeeld wordt de resourcegroep gemaakt in de regio Oost-VS, maar u de resourcegroep maken in een Azure-regio.

```powershell
New-AzResourceGroup -Name NetworkWatcherRG -Location eastus
```

Maak een netwerkwatcher. U moet een netwerkwatcher hebben gemaakt in ten minste één Azure-regio. In dit voorbeeld wordt een netwerkwatcher gemaakt in de Azure-regio Oost-VS.

```powershell
New-AzNetworkWatcher -Name NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG -Location eastus
```

## <a name="compare-relative-network-latencies-to-a-single-azure-region-from-a-specific-location"></a>Relatieve netwerklatencies vergelijken met één Azure-regio vanaf een specifieke locatie

Evalueer serviceproviders of los een gebruiker op die een probleem rapporteert, zoals 'de site was traag', van een specifieke locatie naar het azure-gebied waar een service wordt geïmplementeerd. Met de volgende opdracht retourneert u bijvoorbeeld de gemiddelde relatieve latencies van internetserviceproviders tussen de staat Washington in de Verenigde Staten en de Azure-regio West US 2 tussen 13-15 december 2017:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Location "West US 2" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> Het gebied dat u in de vorige opdracht opgeeft, hoeft niet hetzelfde te zijn als het gebied dat u hebt opgegeven toen u de netwerkwatcher ophaalde. De vorige opdracht vereist eenvoudig dat u een bestaande netwerkwatcher opgeeft. De netwerkwatcher kan zich in elke regio bevinden. Als u waarden `-Country` `-State`opgeeft voor en , moeten deze geldig zijn. De waarden zijn hoofdlettergevoelig. Gegevens zijn beschikbaar voor een beperkt aantal landen/regio's, staten en steden. Voer de opdrachten uit in [Beschikbare landen/regio's, staten, steden en providers weergeven](#view-available) om een lijst met beschikbare landen/regio's, steden en staten te bekijken die met de vorige opdracht moeten worden gebruikt. 

> [!WARNING]
> U moet een datum opgeven in `-StartTime` de `-EndTime`afgelopen 30 dagen voor en . Als u een voorafgaande datum opgeeft, worden er geen gegevens geretourneerd.

De uitvoer van de vorige opdracht volgt:

```powershell
AggregationLevel   : State
ProviderLocation   : {
                       "Country": "United States",
                       "State": "washington"
                     }
ReachabilityReport : [
                       {
                         "Provider": "Qwest Communications Company, LLC - ASN 209",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 92
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 92
                           }
                         ]
                       },
                       {
                         "Provider": "Comcast Cable Communications, LLC - ASN 7922",
                         "AzureLocation": "West US 2",
                         "Latencies": [
                           {
                             "TimeStamp": "2017-12-14T00:00:00Z",
                             "Score": 96
                           },
                           {
                             "TimeStamp": "2017-12-13T00:00:00Z",
                             "Score": 96
                           }
                         ]
                       }
                     ]
```

In de geretourneerde uitvoer is de waarde voor **Score** de relatieve latentie tussen regio's en providers. Een score van 1 is de slechtste (hoogste) latentie, terwijl 100 de laagste latentie is. De relatieve latencies zijn gemiddeld voor de dag. In het vorige voorbeeld, terwijl het duidelijk is dat de latencies beide dagen hetzelfde waren en dat er een klein verschil is tussen de latentie van de twee providers, is het ook duidelijk dat de latencies voor beide providers laag zijn op de schaal van 1-100. Hoewel dit wordt verwacht, omdat de staat Washington in de Verenigde Staten fysiek dicht bij de West US 2 Azure-regio ligt, zijn de resultaten soms niet zoals verwacht. Hoe groter het datumbereik dat u opgeeft, hoe meer u in de loop van de tijd de gemiddelde latentie hebben.

## <a name="compare-relative-network-latencies-across-azure-regions-from-a-specific-location"></a>Relatieve netwerklatencies in Azure-regio's vergelijken vanaf een specifieke locatie

Als u in plaats van de relatieve latencies tussen een `-Location`specifieke locatie en een specifieke Azure-regio op te geven met behulp van, de relatieve latencies voor alle Azure-regio's vanaf een specifieke fysieke locatie wilt bepalen, u dat ook doen. Met de volgende opdracht u bijvoorbeeld evalueren in welke azure-regio u een service wilt implementeren als uw primaire gebruikers Comcast-gebruikers zijn die zich in de staat Washington bevinden:

```powershell
Get-AzNetworkWatcherReachabilityReport `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -Provider "Comcast Cable Communications, LLC - ASN 7922" `
  -Country "United States" `
  -State "washington" `
  -StartTime "2017-12-13" `
  -EndTime "2017-12-15"
```

> [!NOTE]
> In tegenstelling tot wanneer u één locatie opgeeft, moet u bij het uitvoeren van de opdracht een internetprovider opgeven als u geen locatie opgeeft of meerdere locaties opgeven, zoals 'West US2', 'West US'. 

## <a name="view-available-countriesregions-states-cities-and-providers"></a><a name="view-available"></a>Beschikbare landen/regio's, staten, steden en providers weergeven

Gegevens zijn beschikbaar voor specifieke internetproviders, landen/regio's, staten en steden. Voer de volgende opdracht in om een lijst met alle beschikbare internetproviders, landen/regio's, staten en steden te bekijken waarvoor u gegevens bekijken:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList -NetworkWatcherName NetworkWatcher_eastus -ResourceGroupName NetworkWatcherRG
```

Gegevens zijn alleen beschikbaar voor de landen/regio's, staten en steden die door de vorige opdracht zijn geretourneerd. Voor de vorige opdracht moet u een bestaande netwerkwatcher opgeven. In het voorbeeld is de *NetworkWatcher_eastus* netwerkwatcher opgegeven in een resourcegroep met de naam *NetworkWatcherRG,* maar u elke bestaande netwerkwatcher opgeven. Als u geen bestaande netwerkwatcher hebt, maakt u er een door de taken te voltooien in [Een netwerkwatcher maken.](#create-a-network-watcher) 

Nadat u de vorige opdracht hebt uitgevoerd, u de geretourneerde uitvoer filteren door desgewenst geldige waarden voor **Land,** **Staat**en **Stad**op te geven.  Als u bijvoorbeeld de lijst met internetproviders wilt bekijken die beschikbaar zijn in Seattle, Washington, in de Verenigde Staten, voert u de volgende opdracht in:

```powershell
Get-AzNetworkWatcherReachabilityProvidersList `
  -NetworkWatcherName NetworkWatcher_eastus `
  -ResourceGroupName NetworkWatcherRG `
  -City Seattle `
  -Country "United States" `
  -State washington
```

> [!WARNING]
> De voor **land** opgegeven waarde moet hoger en lager zijn. De waarden die voor **Staat** en **Stad** zijn opgegeven, moeten kleine letters zijn. De waarden moeten worden weergegeven in de uitvoer die is geretourneerd nadat de opdracht zonder waarden voor **Land**, **Staat**en **Stad**is uitgevoerd . Als u de onjuiste aanvraag opgeeft of een waarde opgeeft voor **Land,** **Staat**of **Plaats** die niet in de uitvoer is geretourneerd nadat de opdracht zonder waarden voor deze eigenschappen is uitgevoerd, is de geretourneerde uitvoer leeg.
