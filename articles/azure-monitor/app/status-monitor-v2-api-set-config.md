---
title: 'Naslag informatie over Azure-toepassing Insights-agent-API: set config | Microsoft Docs'
description: Application Insights agent API-verwijzing. Set-ApplicationInsightsMonitoringConfig. Bewaak de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET-Web-apps die on-premises worden gehost, in Vm's of op Azure.
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
ms.openlocfilehash: 2ab941b5587a8836f1e472fbce3966b12bfa1e11
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388260"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>Application Insights agent-API: set-ApplicationInsightsMonitoringConfig

In dit document wordt een cmdlet beschreven die lid is van de [Power shell-module AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beschrijving

Hiermee stelt u het configuratie bestand in zonder volledige herinstallatie.
Start IIS opnieuw op om de wijzigingen van kracht te laten worden.

> [!IMPORTANT] 
> Voor deze cmdlet is een Power shell-sessie met beheerders machtigingen vereist.


## <a name="examples"></a>Voorbeelden

### <a name="example-with-a-single-instrumentation-key"></a>Voor beeld met één instrumentatie sleutel
In dit voor beeld wordt aan alle apps op de huidige computer één instrumentatie sleutel toegewezen.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Voor beeld met een instrumentatie sleutel toewijzing
In dit voor beeld:
- `MachineFilter` komt overeen met de huidige computer door gebruik te maken van het Joker teken `'.*'`.
- `AppFilter='WebAppExclude'` biedt een `null` instrumentatie sleutel. De opgegeven app wordt niet geinstrumenteerd.
- `AppFilter='WebAppOne'` wijst de opgegeven app een unieke instrumentatie sleutel toe.
- `AppFilter='WebAppTwo'` wijst de opgegeven app een unieke instrumentatie sleutel toe.
- Ten slotte maakt `AppFilter` ook gebruik van het Joker teken `'.*'` om te voldoen aan alle web-apps die niet overeenkomen met de eerdere regels en wijst u een standaard instrumentatie sleutel toe.
- Spaties worden toegevoegd voor de Lees baarheid.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parameters

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Vereist.** Gebruik deze para meter om één instrumentatie sleutel op te geven voor gebruik door alle apps op de doel computer.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Vereist.** Gebruik deze para meter om meerdere instrumentatie sleutels en een toewijzing van de instrumentatie sleutels voor elke app op te geven.
U kunt één installatie script maken voor verschillende computers door `MachineFilter` in te stellen.

> [!IMPORTANT]
> Apps komen overeen met regels in de volg orde waarin de regels worden opgegeven. Daarom moet u eerst de meest specifieke regels opgeven en de meest algemene regels als laatste.

#### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** is een vereiste C# regex van de naam van de computer of virtuele machine.
    - '. * ' komt overeen met alles
    - ComputerName komt alleen overeen met computers met de opgegeven naam.
- **AppFilter** is een vereiste C# regex van de naam van de computer of virtuele machine.
    - '. * ' komt overeen met alles
    - ApplicationName komt alleen overeen met IIS-apps met de opgegeven naam.
- **InstrumentationKey** is vereist om de bewaking van de apps in te scha kelen die overeenkomen met de voor gaande twee filters.
    - Laat deze waarde leeg als u regels wilt definiëren om bewaking uit te sluiten.


### <a name="-verbose"></a>-Verbose
**Algemene para meter.** Gebruik deze schakel optie om gedetailleerde logboeken weer te geven.


## <a name="output"></a>Uitvoer

Standaard geen uitvoer.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Voor beeld van uitgebreide uitvoer van het instellen van het configuratie bestand via-InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Voor beeld van uitgebreide uitvoer van het instellen van het configuratie bestand via-InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>Volgende stappen

  Uw telemetrie weergeven:
 - [Bekijk metrische gegevens](../../azure-monitor/app/metrics-explorer.md) om de prestaties en het gebruik te bewaken.
- [Zoek gebeurtenissen en logboeken](../../azure-monitor/app/diagnostic-search.md) om problemen op te sporen.
- [Gebruik analyses](../../azure-monitor/app/analytics.md) voor meer geavanceerde query's.
- [Dash boards maken](../../azure-monitor/app/overview-dashboard.md).
 
 Meer telemetrie toevoegen:
 - [Maak webtests](monitor-web-app-availability.md) om ervoor te zorgen dat uw site actief blijft.
- [Voeg de telemetrie van de webclient](../../azure-monitor/app/javascript.md) toe om uitzonde ringen van webpagina code te bekijken en tracerings aanroepen in te scha kelen.
- [Voeg de Application INSIGHTS SDK toe aan uw code](../../azure-monitor/app/asp-net.md) zodat u tracerings-en logboek aanroepen kunt invoegen.
 
 Meer doen met Application Insights agent:
 - Gebruik onze hand leiding om Application Insights-agent op te [lossen](status-monitor-v2-troubleshoot.md) .
 - Stel [de configuratie](status-monitor-v2-api-get-config.md) in om te bevestigen dat de instellingen correct zijn geregistreerd.
 - [De status ophalen om de](status-monitor-v2-api-get-status.md) bewaking te controleren.
