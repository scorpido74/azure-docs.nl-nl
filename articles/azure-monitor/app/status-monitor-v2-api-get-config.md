---
title: Api-verwijzing naar Azure Application Insights Agent-api
description: Api-referentie van Application Insights Agent. Get-ApplicationInsightsMonitoringConfig. Monitor de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET web-apps die on-premises, in VM's of op Azure worden gehost.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 93ab8f613e5634b2eabe7c02189e223d3dfbb0a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671270"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>Application Insights Agent API: Get-ApplicationInsightsMonitoringConfig

In dit artikel wordt een cmdlet beschreven die lid is van de [Az.ApplicationMonitor PowerShell-module.](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)

## <a name="description"></a>Beschrijving

Hiermee wordt het config-bestand opgevraagd en worden de waarden naar de console afgedrukt.

> [!IMPORTANT] 
> Deze cmdlet vereist een PowerShell-sessie met beheerdersmachtigingen.

## <a name="examples"></a>Voorbeelden

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Parameters

Geen parameters vereist.

## <a name="output"></a>Uitvoer


#### <a name="example-output-from-reading-the-config-file"></a>Voorbeelduitvoer van het lezen van het config-bestand

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Volgende stappen

  Uw telemetrie weergeven:
 - [Bekijk statistieken](../../azure-monitor/app/metrics-explorer.md) om de prestaties en het gebruik te controleren.
- [Zoek gebeurtenissen en logboeken](../../azure-monitor/app/diagnostic-search.md) om problemen te diagnosticeren.
- Gebruik [analytics](../../azure-monitor/app/analytics.md) voor meer geavanceerde query's.
- [Dashboards maken](../../azure-monitor/app/overview-dashboard.md).
 
 Meer telemetrie toevoegen:
 - [Maak webtests](monitor-web-app-availability.md) om ervoor te zorgen dat uw site actief blijft.
- [Voeg telemetrie van webclient toe](../../azure-monitor/app/javascript.md) om uitzonderingen van webpaginacode te bekijken en tracecalls in te schakelen.
- [Voeg de Application Insights SDK toe aan uw code,](../../azure-monitor/app/asp-net.md) zodat u trace- en logoproepen invoegen.
 
 Doe meer met Application Insights Agent:
 - Gebruik onze gids om application insights agent op te [lossen.](status-monitor-v2-troubleshoot.md)
 - Breng wijzigingen aan in de config met behulp van de [set config](status-monitor-v2-api-set-config.md) cmdlet.
