---
title: API-naslag informatie over Azure-toepassing Insights-agent
description: Application Insights agent API-verwijzing. Start-Trace. ETW-logboeken verzamelen van Status Monitor en Application Insights SDK.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b9680101f1a22dd6d9c1617c8afc13a10ad1c594
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671219"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>Application Insights agent-API: start-ApplicationInsightsMonitoringTrace

In dit artikel wordt een cmdlet beschreven die lid is van de [Power shell-module AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beschrijving

Verzamelt [etw-gebeurtenissen](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) vanuit de runtime van code koppelen. Deze cmdlet is een alternatief voor het uitvoeren van [PerfView](https://github.com/microsoft/perfview).

Verzamelde gebeurtenissen worden in realtime afgedrukt op de console en opgeslagen in een ETL-bestand. Het ETL-bestand van de uitvoer kan worden geopend door [PerfView](https://github.com/microsoft/perfview) voor verder onderzoek.

Deze cmdlet wordt uitgevoerd totdat de time-outperiode (standaard 5 minuten) is bereikt of hand matig wordt gestopt (`Ctrl + C`).

> [!IMPORTANT] 
> Voor deze cmdlet is een Power shell-sessie met beheerders machtigingen vereist.

## <a name="examples"></a>Voorbeelden

### <a name="how-to-collect-events"></a>Gebeurtenissen verzamelen

Normaal gesp roken wordt u gevraagd om gebeurtenissen te verzamelen om te onderzoeken waarom uw toepassing niet wordt geinstrumenteerd.

Tijdens de uitvoering van het programma code koppelen worden ETW-gebeurtenissen meegeteld wanneer IIS wordt gestart en wanneer de toepassing wordt gestart.

Deze gebeurtenissen verzamelen:
1. Voer `iisreset /stop` uit in een cmd-console met beheerders bevoegdheden om IIS en alle web-apps uit te scha kelen.
2. Deze cmdlet uitvoeren
3. Voer `iisreset /start` uit in een cmd-console met beheerders bevoegdheden om IIS te starten.
4. Probeer naar uw app te bladeren.
5. Nadat de app is geladen, kunt u deze hand matig stoppen (`Ctrl + C`) of wachten op de time-out.

### <a name="what-events-to-collect"></a>Welke gebeurtenissen moeten worden verzameld

Er zijn drie opties voor het verzamelen van gebeurtenissen:
1. Gebruik de Schakel `-CollectSdkEvents` om gebeurtenissen te verzamelen die afkomstig zijn van de Application Insights SDK.
2. Gebruik de Schakel `-CollectRedfieldEvents` om gebeurtenissen te verzamelen die worden gegenereerd door Status Monitor en de Redfield-runtime. Deze logboeken zijn handig bij het vaststellen van IIS en het opstarten van toepassingen.
3. Gebruik beide Schakel opties om beide gebeurtenis typen te verzamelen.
4. Standaard als er geen schakelaar is opgegeven, worden beide gebeurtenis typen verzameld.


## <a name="parameters"></a>Parameters

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Beschrijving.** Gebruik deze para meter om in te stellen hoe lang dit script gebeurtenissen moet verzamelen. Standaard is dit 5 minuten.

### <a name="-logdirectory"></a>-LogDirectory
**Beschrijving.** Gebruik deze schakel optie om de map uitvoermap van het ETL-bestand in te stellen. Dit bestand wordt standaard gemaakt in de map Power shell-modules. Het volledige pad wordt weer gegeven tijdens het uitvoeren van het script.


### <a name="-collectsdkevents"></a>-CollectSdkEvents
**Beschrijving.** Gebruik deze schakel optie om Application Insights SDK-gebeurtenissen te verzamelen.

### <a name="-collectredfieldevents"></a>-CollectRedfieldEvents
**Beschrijving.** Gebruik deze schakel optie om gebeurtenissen van Status Monitor en de Redfield-runtime te verzamelen.

### <a name="-verbose"></a>-Verbose
**Algemene para meter.** Gebruik deze optie om gedetailleerde logboeken uit te voeren.



## <a name="output"></a>Uitvoer


### <a name="example-of-application-startup-logs"></a>Voor beeld van opstart logboeken van toepassingen
```
PS C:\Windows\system32> Start-ApplicationInsightsMonitoringTrace -ColectRedfieldEvents
Starting...
Log File: C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\logs\20190627_144217_ApplicationInsights_ETW_Trace.etl
Tracing enabled, waiting for events.
Tracing will timeout in 5 minutes. Press CTRL+C to cancel.

2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:31 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 70 ms
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Current assembly 'Microsoft.ApplicationInsights.RedfieldIISModule, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3' location 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Matched filter '.*'~'STATUSMONITORTE', '.*'~'DemoWithSql'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace Lightup assembly calculated path: 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll'
2:42:31 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Loaded applicationInsights.config from assembly's resource Microsoft.ApplicationInsights.Redfield.Lightup, Version=2.8.18.27202, Culture=neutral, PublicKeyToken=f23a46de0be5d6f3/Microsoft.ApplicationInsights.Redfield.Lightup.ApplicationInsights-recommended.config
2:42:34 PM EVENT: Microsoft-ApplicationInsights-FrameworkLightup Trace Successfully attached ApplicationInsights SDK
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.LoadLightupAssemblyAndGetLightupHttpModuleClass, success, 2687 ms
2:42:34 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:34 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 3288 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftAppInsights_ManagedHttpModulePath='C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll', MicrosoftAppInsights_ManagedHttpModuleType='Microsoft.ApplicationInsights.RedfieldIISModule.RedfieldIISModule'
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Resolved variables to: MicrosoftDiagnosticServices_ManagedHttpModulePath2='', MicrosoftDiagnosticServices_ManagedHttpModuleType2=''
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace Environment variable 'MicrosoftDiagnosticServices_ManagedHttpModulePath2' or 'MicrosoftDiagnosticServices_ManagedHttpModuleType2' is null, skipping managed dll loading
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace MulticastHttpModule.constructor, success, 0 ms
2:42:35 PM EVENT: Microsoft-ApplicationInsights-RedfieldIISModule Trace RedfieldIISModule.CreateAndInitializeApplicationInsightsHttpModules(lightupHttpModuleClass), success
2:42:35 PM EVENT: Microsoft-ApplicationInsights-IIS-ManagedHttpModuleHelper Trace ManagedHttpModuleHelper, multicastHttpModule.Init() success, 0 ms
Timeout Reached. Stopping...
```


## <a name="next-steps"></a>Volgende stappen

Extra probleem oplossing:

- Bekijk hier de aanvullende stappen voor probleem oplossing: https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Raadpleeg de [API-verwijzing](status-monitor-v2-overview.md#powershell-api-reference) voor meer informatie over de para meters die u mogelijk hebt gemist.
- Als u meer hulp nodig hebt, kunt u contact met ons opnemen op [github](https://github.com/Microsoft/ApplicationInsights-Home/issues).



 Meer doen met Application Insights agent:
 - Gebruik onze hand leiding om Application Insights-agent op te [lossen](status-monitor-v2-troubleshoot.md) .
 - Stel [de configuratie](status-monitor-v2-api-get-config.md) in om te bevestigen dat de instellingen correct zijn geregistreerd.
 - [De status ophalen om de](status-monitor-v2-api-get-status.md) bewaking te controleren.
