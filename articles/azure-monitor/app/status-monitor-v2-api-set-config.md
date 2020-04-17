---
title: Api-verwijzing naar Azure Application Insights Agent-api
description: Api-referentie van Application Insights Agent. Set-ApplicationInsightsMonitoringConfig. Monitor de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET web-apps die on-premises, in VM's of op Azure worden gehost.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: c47b9b5f297fa62c474e6c29737d6d11b887130d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537471"
---
# <a name="application-insights-agent-api-set-applicationinsightsmonitoringconfig"></a>Application Insights Agent API: Set-ApplicationInsightsMonitoringConfig

In dit document wordt een cmdlet beschreven die lid is van de [Az.ApplicationMonitor PowerShell-module.](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)

## <a name="description"></a>Beschrijving

Hiermee stelt u het config-bestand in zonder een volledige herinstallatie te doen.
Start IIS opnieuw op om uw wijzigingen van kracht te laten worden.

> [!IMPORTANT] 
> Deze cmdlet vereist een PowerShell-sessie met beheerdersmachtigingen.


## <a name="examples"></a>Voorbeelden

### <a name="example-with-a-single-instrumentation-key"></a>Voorbeeld met één instrumentatietoets
In dit voorbeeld krijgen alle apps op de huidige computer één instrumentatiesleutel toegewezen.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Voorbeeld met een instrumentatiesleutelkaart
In dit voorbeeld:
- `MachineFilter`komt overeen met `'.*'` de huidige computer met behulp van de wildcard.
- `AppFilter='WebAppExclude'`biedt `null` een instrumentatiesleutel. De opgegeven app wordt niet geinstrumenteerd.
- `AppFilter='WebAppOne'`wijst de opgegeven app een unieke instrumentatiesleutel toe.
- `AppFilter='WebAppTwo'`wijst de opgegeven app een unieke instrumentatiesleutel toe.
- Tot `AppFilter` slot gebruikt `'.*'` u de wildcard ook om alle web-apps aan te passen die niet overeenkomen met de eerdere regels en een standaardinstrumentatiesleutel toe te wijzen.
- Spaties worden toegevoegd voor leesbaarheid.

```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap `
       @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
          @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
          @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
          @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})
```

## <a name="parameters"></a>Parameters

### <a name="-instrumentationkey"></a>-Instrumentatiesleutel
**Vereist.** Gebruik deze parameter om één instrumentatiesleutel te leveren voor gebruik door alle apps op de doelcomputer.

### <a name="-instrumentationkeymap"></a>-InstrumentatieKeyMap
**Vereist.** Gebruik deze parameter om meerdere instrumentatietoetsen en een toewijzing van de instrumentatietoetsen die door elke app worden gebruikt, te leveren.
U één installatiescript voor meerdere `MachineFilter`computers maken door.

> [!IMPORTANT]
> Apps komen overeen met de regels in de volgorde waarin de regels worden verstrekt. U moet dus eerst de meest specifieke regels opgeven en de meest algemene regels duren.

#### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** is een vereiste C# regex van de computer of VM-naam.
    - '.*' komt overeen met alle
    - 'ComputerName' komt alleen overeen met computers met de opgegeven naam.
- **AppFilter** is een vereiste C# regex van de computer of VM-naam.
    - '.*' komt overeen met alle
    - 'ApplicationName' komt alleen overeen met IIS-apps met de opgegeven naam.
- **InstrumentationKey** is vereist om de bewaking van de apps die overeenkomen met de voorgaande twee filters mogelijk te maken.
    - Laat deze waarde nietig als u regels wilt definiëren om controle uit te sluiten.


### <a name="-verbose"></a>-Verbose
**Algemene parameter.** Gebruik deze schakelaar om gedetailleerde logboeken weer te geven.


## <a name="output"></a>Uitvoer

Standaard geen uitvoer.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Voorbeeld van verbose-uitvoer van het instellen van het config-bestand via -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Voorbeeld van verbose-uitvoer van het instellen van het config-bestand via -InstrumentationKeyMap

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
 - [Bekijk statistieken](../../azure-monitor/platform/metrics-charts.md) om de prestaties en het gebruik te controleren.
- [Zoek gebeurtenissen en logboeken](../../azure-monitor/app/diagnostic-search.md) om problemen te diagnosticeren.
- [Gebruik Analytics](../../azure-monitor/app/analytics.md) voor meer geavanceerde query's.
- [Dashboards maken](../../azure-monitor/app/overview-dashboard.md).
 
 Meer telemetrie toevoegen:
 - [Maak webtests](monitor-web-app-availability.md) om ervoor te zorgen dat uw site actief blijft.
- [Voeg telemetrie van webclient toe](../../azure-monitor/app/javascript.md) om uitzonderingen van webpaginacode te bekijken en tracecalls in te schakelen.
- [Voeg de Application Insights SDK toe aan uw code,](../../azure-monitor/app/asp-net.md) zodat u trace- en logcalls invoegen
 
 Doe meer met Application Insights Agent:
 - Gebruik onze gids om application insights agent op te [lossen.](status-monitor-v2-troubleshoot.md)
 - [Laat de config](status-monitor-v2-api-get-config.md) bevestigen dat uw instellingen correct zijn geregistreerd.
 - [Zorg dat de status de](status-monitor-v2-api-get-status.md) controle inspecteert.
