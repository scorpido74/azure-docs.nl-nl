---
title: 'Azure-toepassing Insights API-referentie voor agent: Get config | Microsoft Docs'
description: Application Insights agent API-verwijzing. Get-ApplicationInsightsMonitoringConfig. Bewaak de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET-Web-apps die on-premises worden gehost, in Vm's of op Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: e0b389dfb27443b9b26b8ed650399c641c55eb86
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388272"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>Application Insights agent-API: Get-ApplicationInsightsMonitoringConfig

In dit artikel wordt een cmdlet beschreven die lid is van de [Power shell-module AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beschrijving

Hiermee wordt het configuratie bestand opgehaald en worden de waarden afgedrukt op de-console.

> [!IMPORTANT] 
> Voor deze cmdlet is een Power shell-sessie met beheerders machtigingen vereist.

## <a name="examples"></a>Voorbeelden

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>Parameters

Geen para meters vereist.

## <a name="output"></a>Uitvoer


#### <a name="example-output-from-reading-the-config-file"></a>Voor beeld van uitvoer van het lezen van het configuratie bestand

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>Volgende stappen

  Uw telemetrie weergeven:
 - [Bekijk metrische gegevens](../../azure-monitor/app/metrics-explorer.md) om de prestaties en het gebruik te bewaken.
- [Zoek gebeurtenissen en logboeken](../../azure-monitor/app/diagnostic-search.md) om problemen op te sporen.
- Gebruik [analyses](../../azure-monitor/app/analytics.md) voor meer geavanceerde query's.
- [Dash boards maken](../../azure-monitor/app/overview-dashboard.md).
 
 Meer telemetrie toevoegen:
 - [Maak webtests](monitor-web-app-availability.md) om ervoor te zorgen dat uw site actief blijft.
- [Voeg de telemetrie van de webclient](../../azure-monitor/app/javascript.md) toe om uitzonde ringen van webpagina code te bekijken en tracerings aanroepen in te scha kelen.
- [Voeg de Application INSIGHTS SDK toe aan uw code](../../azure-monitor/app/asp-net.md) zodat u tracerings-en logboek aanroepen kunt invoegen.
 
 Meer doen met Application Insights agent:
 - Gebruik onze hand leiding om Application Insights-agent op te [lossen](status-monitor-v2-troubleshoot.md) .
 - Breng wijzigingen aan in de configuratie met behulp van de [set config](status-monitor-v2-api-set-config.md) -cmdlet.
