---
title: Statistieken en waarschuwingen in Azure Traffic Manager
description: In dit artikel leest u de statistieken en waarschuwingen die beschikbaar zijn voor Traffic Manager in Azure.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/11/2018
ms.author: rohink
ms.openlocfilehash: 521e6ac605d187c0f95545611a17a86cfda6e1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938589"
---
# <a name="traffic-manager-metrics-and-alerts"></a>Statistieken en waarschuwingen voor Verkeersbeheer

Traffic Manager biedt u dns-gebaseerde taakverdeling die meerdere routeringsmethoden en eindpuntcontroleopties bevat. In dit artikel worden de statistieken en bijbehorende waarschuwingen beschreven die beschikbaar zijn voor klanten. 

## <a name="metrics-available-in-traffic-manager"></a>Statistieken beschikbaar in Traffic Manager 

Traffic Manager biedt de volgende statistieken per profiel die klanten kunnen gebruiken om inzicht te krijgen in hun gebruik van Traffic manager en de status van hun eindpunten onder dat profiel.  

### <a name="queries-by-endpoint-returned"></a>Query's op eindpunt geretourneerd
Gebruik [deze statistiek](../azure-monitor/platform/metrics-supported.md) om het aantal query's weer te geven dat een Traffic Manager-profiel gedurende een bepaalde periode verwerkt. U dezelfde informatie ook bekijken op een granulariteit op eindpuntniveau, zodat u begrijpt hoe vaak een eindpunt is geretourneerd in de queryantwoorden van Traffic Manager.

In het volgende voorbeeld geeft figuur 1 alle queryreacties weer die het Traffic Manager-profiel retourneert. 

  
![Geaggregeerde weergave van alle query's](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-queries-aggregate-view.png)

*Figuur 1: Geaggregeerde weergave met alle query's*
  
Figuur 2 geeft dezelfde informatie weer, maar deze wordt gesplitst naar eindpunten. Als gevolg hiervan u het volume van queryreacties zien waarin een specifiek eindpunt is geretourneerd.

![Traffic Manager metrics - split view of query volume per eindpunt](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-query-volume-per-endpoint.png)

*Figuur 2: Gesplitste weergave met queryvolume weergegeven per endpoint geretourneerd*

## <a name="endpoint-status-by-endpoint"></a>Eindpuntstatus op eindpunt
Gebruik [deze statistiek](../azure-monitor/platform/metrics-supported.md#microsoftnetworktrafficmanagerprofiles) om de status van de eindpunten in het profiel te begrijpen. Er zijn twee waarden voor nodig:
 - 1 **1** gebruiken als het eindpunt omhoog is.
 - 0 **0** gebruiken als het eindpunt is uitgeschakeld.

Deze statistiek kan worden weergegeven als een geaggregeerde waarde die de status van alle statistieken weergeeft (figuur 3), of kan worden gesplitst (zie figuur 4) om de status van specifieke eindpunten weer te geven. Als het eerste, als het aggregatieniveau is geselecteerd als **Avg,** is de waarde van deze statistiek het rekenkundig gemiddelde van de status van alle eindpunten. Als een profiel bijvoorbeeld twee eindpunten heeft en slechts één is gezond, heeft deze statistiek een waarde van **0,50,** zoals weergegeven in figuur 3. 


![Traffic Manager metrics - samengestelde weergave van de status van eindpunt](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-composite-view.png)

*Figuur 3: Samengestelde weergave van de statistiek van de status van eindpunt – Avg-aggregatie geselecteerd*


![Traffic Manager metrics - split view of endpoint status](./media/traffic-manager-metrics-alerts/traffic-manager-metrics-endpoint-status-split-view.png)

*Figuur 4: Gesplitste weergave van eindpuntstatusstatistieken*

U deze statistieken gebruiken via de portal van [azure monitor-service,](../azure-monitor/platform/metrics-supported.md) [de REST API,](https://docs.microsoft.com/rest/api/monitor/) [Azure CLI](https://docs.microsoft.com/cli/azure/monitor)en [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights)of via het gedeelte metrics van de portalervaring van Traffic Manager.

## <a name="alerts-on-traffic-manager-metrics"></a>Waarschuwingen voor statistieken verkeersbeheer
Naast het verwerken en weergeven van statistieken van Traffic Manager, stelt Azure Monitor klanten in staat om waarschuwingen te configureren en te ontvangen die aan deze statistieken zijn gekoppeld. U kiezen aan welke voorwaarden in deze statistieken moet worden voldaan om een waarschuwing te kunnen ontvangen, hoe vaak deze voorwaarden moeten worden gecontroleerd en hoe de waarschuwingen naar u moeten worden verzonden. Zie [Azure Monitor-waarschuwingendocumentatie](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [azure monitorservice](../azure-monitor/platform/metrics-supported.md)
- Meer informatie over het [maken van een grafiek met Azure Monitor](../azure-monitor/platform/metrics-getting-started.md#create-your-first-metric-chart)
