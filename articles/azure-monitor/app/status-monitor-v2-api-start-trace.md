---
title: Api-verwijzing naar Azure Application Insights Agent-api
description: Api-referentie van Application Insights Agent. Start-Trace. ETW-logboeken verzamelen van Status Monitor en Application Insights SDK.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b9680101f1a22dd6d9c1617c8afc13a10ad1c594
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671219"
---
# <a name="application-insights-agent-api-start-applicationinsightsmonitoringtrace"></a>Application Insights Agent API: Start-ApplicationInsightsMonitoringTrace

In dit artikel wordt een cmdlet beschreven die lid is van de [Az.ApplicationMonitor PowerShell-module.](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)

## <a name="description"></a>Beschrijving

Verzamelt [ETW-gebeurtenissen](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) van de codeloze runtime voor het toevoegen van codeloos. Deze cmdlet is een alternatief voor het uitvoeren van [PerfView.](https://github.com/microsoft/perfview)

Verzamelde gebeurtenissen worden in realtime op de console afgedrukt en opgeslagen in een ETL-bestand. Het uitvoer-ETL-bestand kan door [PerfView](https://github.com/microsoft/perfview) worden geopend voor verder onderzoek.

Deze cmdlet wordt uitgevoerd totdat deze de time-outduur heeft bereikt`Ctrl + C`(standaard 5 minuten) of handmatig wordt gestopt ( ).

> [!IMPORTANT] 
> Deze cmdlet vereist een PowerShell-sessie met beheerdersmachtigingen.

## <a name="examples"></a>Voorbeelden

### <a name="how-to-collect-events"></a>Evenementen verzamelen

Normaal gesproken vragen we u gebeurtenissen te verzamelen om te onderzoeken waarom uw aanvraag niet wordt uitgevoerd.

De codeloze runtime voor het koppelen van codeloos zendt ETW-gebeurtenissen uit wanneer IIS wordt opgestart en wanneer uw toepassing wordt opgestart.

Ga als het volgende van deze evenementen:
1. Voer IIS en alle web-apps uit in een cmd-console met beheerdersrechten. `iisreset /stop`
2. Deze cmdlet uitvoeren
3. Voer IIS uit in `iisreset /start` een cmd-console met beheerdersbevoegdheden.
4. Probeer naar uw app te bladeren.
5. Nadat uw app klaar is met laden,`Ctrl + C`u deze handmatig stoppen () of wachten op de time-out.

### <a name="what-events-to-collect"></a>Welke evenementen te verzamelen

Je hebt drie opties bij het verzamelen van evenementen:
1. Gebruik de `-CollectSdkEvents` switch om gebeurtenissen te verzamelen die worden uitgezonden vanuit de Application Insights SDK.
2. Gebruik de `-CollectRedfieldEvents` schakelaar om gebeurtenissen te verzamelen die worden uitgezonden door Statusmonitor en de Redfield Runtime. Deze logboeken zijn handig bij het diagnosticeren van IIS en het opstarten van toepassingen.
3. Gebruik beide switches om beide gebeurtenistypen te verzamelen.
4. Als er geen switch is opgegeven, worden beide gebeurtenistypen verzameld.


## <a name="parameters"></a>Parameters

### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Optionele.** Gebruik deze parameter om in te stellen hoe lang dit script gebeurtenissen moet verzamelen. Standaard is dit 5 minuten.

### <a name="-logdirectory"></a>-LogDirectory
**Optionele.** Gebruik deze schakelaar om de uitvoermap van het ETL-bestand in te stellen. Standaard wordt dit bestand gemaakt in de map PowerShell-modules. Het volledige pad wordt weergegeven tijdens de uitvoering van het script.


### <a name="-collectsdkevents"></a>-CollectsdkEvents
**Optionele.** Gebruik deze schakelaar om APPLICATION Insights SDK-gebeurtenissen te verzamelen.

### <a name="-collectredfieldevents"></a>-RedfieldEvents verzamelen
**Optionele.** Gebruik deze schakelaar om gebeurtenissen te verzamelen van Statusmonitor en de Redfield runtime.

### <a name="-verbose"></a>-Verbose
**Algemene parameter.** Gebruik deze schakelaar om gedetailleerde logboeken uit te schakelen.



## <a name="output"></a>Uitvoer


### <a name="example-of-application-startup-logs"></a>Voorbeeld van logboeken voor het opstarten van toepassingen
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

Aanvullende probleemoplossing:

- Bekijk hier de volgende stappen voor het oplossen van problemen:https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-troubleshoot
- Bekijk de [API-verwijzing](status-monitor-v2-overview.md#powershell-api-reference) voor meer informatie over parameters die u mogelijk hebt gemist.
- Als je extra hulp nodig hebt, kun je contact met ons opnemen via [GitHub.](https://github.com/Microsoft/ApplicationInsights-Home/issues)



 Doe meer met Application Insights Agent:
 - Gebruik onze gids om application insights agent op te [lossen.](status-monitor-v2-troubleshoot.md)
 - [Laat de config](status-monitor-v2-api-get-config.md) bevestigen dat uw instellingen correct zijn geregistreerd.
 - [Zorg dat de status de](status-monitor-v2-api-get-status.md) controle inspecteert.
