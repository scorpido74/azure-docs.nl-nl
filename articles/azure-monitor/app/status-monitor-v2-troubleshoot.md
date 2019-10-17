---
title: Problemen oplossen met Azure-toepassing Insights-agent en bekende problemen | Microsoft Docs
description: De bekende problemen met Application Insights agent en voor beelden van probleem oplossing. Bewaak de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET-Web-apps die on-premises worden gehost, in Vm's of op Azure.
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
ms.openlocfilehash: ab1ce01c41679c6ff686ab37692d3b8e9167a4f8
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388206"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Problemen met Application Insights agent oplossen (voorheen Status Monitor v2 genoemd)

Wanneer u controle inschakelt, kunt u problemen ondervinden die het verzamelen van gegevens verhinderen.
In dit artikel worden alle bekende problemen beschreven en vindt u voor beelden van problemen oplossen.
Als u een probleem ondervindt dat hier niet wordt vermeld, kunt u contact met ons opnemen op [github](https://github.com/Microsoft/ApplicationInsights-Home/issues).

## <a name="known-issues"></a>Bekende problemen

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Conflicterende dll-bestanden in de bin-map van een app

Als een of meer van deze DLL-bestanden aanwezig zijn in de bin-map, kan de bewaking mislukken:

- Microsoft. ApplicationInsights. dll
- Micro soft. AspNet. TelemetryCorrelation. dll
- System. Diagnostics. DiagnosticSource. dll

Sommige van deze DLL-bestanden zijn opgenomen in de Visual Studio-sjablonen voor standaard-apps, zelfs als deze niet worden gebruikt door uw app.
U kunt hulpprogram ma's voor probleem oplossing gebruiken om het gedrag van Symptomatic te bekijken:

- PerfView
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset-en app-belasting (zonder telemetrie). Onderzoeken met Sysinternals (handle. exe en ListDLLs. exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Conflict met gedeelde IIS-configuratie

Als u een cluster van webservers hebt, kunt u gebruikmaken van een [gedeelde configuratie](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
De HTTP module kan niet worden ingevoegd in deze gedeelde configuratie.
Voer de opdracht inschakelen op elke webserver uit om de DLL te installeren in de GAC van elke server.

Nadat u de opdracht inschakelen hebt uitgevoerd, voert u de volgende stappen uit:
1. Ga naar de gedeelde configuratiemap en zoek het bestand applicationHost. config.
2. Voeg deze regel toe aan het gedeelte modules van uw configuratie:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>Geneste IIS-toepassingen

Geneste toepassingen in IIS in versie 1,0 worden niet geinstrumenteerd.
Dit probleem wordt [hier](https://github.com/microsoft/ApplicationInsights-Home/issues/369)bijgehouden.

### <a name="advanced-sdk-configuration-isnt-available"></a>Geavanceerde SDK-configuratie is niet beschikbaar.

De SDK-configuratie wordt niet weer gegeven aan de eind gebruiker in versie 1,0.
Dit probleem wordt [hier](https://github.com/microsoft/ApplicationInsights-Home/issues/375)bijgehouden.

    
    
## <a name="troubleshooting"></a>Problemen oplossen
    
### <a name="troubleshooting-powershell"></a>Problemen met Power shell oplossen

#### <a name="determine-which-modules-are-available"></a>Bepalen welke modules beschikbaar zijn
U kunt de `Get-Module -ListAvailable`-opdracht gebruiken om te bepalen welke modules zijn geïnstalleerd.

#### <a name="import-a-module-into-the-current-session"></a>Een module in de huidige sessie importeren
Als een module niet is geladen in een Power shell-sessie, kunt u deze hand matig laden met behulp van de `Import-Module <path to psd1>` opdracht.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Problemen met de module Application Insights agent oplossen

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>De beschik bare opdrachten in de module Application Insights agent weer geven
Voer de opdracht `Get-Command -Module Az.ApplicationMonitor` uit om de beschik bare opdrachten op te halen:

```
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Disable-ApplicationInsightsMonitoring              0.4.0      Az.ApplicationMonitor
Cmdlet          Disable-InstrumentationEngine                      0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-ApplicationInsightsMonitoring               0.4.0      Az.ApplicationMonitor
Cmdlet          Enable-InstrumentationEngine                       0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.4.0      Az.ApplicationMonitor
Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.4.0      Az.ApplicationMonitor
Cmdlet          Start-ApplicationInsightsMonitoringTrace           0.4.0      Az.ApplicationMonitor
```

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>De huidige versie van de Application Insights Agent-module bepalen
Voer de `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` opdracht uit om de volgende informatie over de module weer te geven:
   - Versie van Power shell-module
   - Application Insights SDK-versie
   - Bestands paden van de Power shell-module
    
Raadpleeg de [API-naslag informatie](status-monitor-v2-api-get-status.md) voor een gedetailleerde beschrijving van het gebruik van deze cmdlet.


### <a name="troubleshooting-running-processes"></a>Problemen met actieve processen oplossen

U kunt de processen op de geinstrumenteerde computer controleren om te bepalen of alle Dll's zijn geladen.
Als de bewaking werkt, moeten ten minste 12 Dll's worden geladen.

Gebruik de `Get-ApplicationInsightsMonitoringStatus -InspectProcess` opdracht om de Dll's te controleren.

Raadpleeg de [API-naslag informatie](status-monitor-v2-api-get-status.md) voor een gedetailleerde beschrijving van het gebruik van deze cmdlet.


### <a name="collect-etw-logs-by-using-perfview"></a>ETW-logboeken verzamelen met behulp van PerfView

#### <a name="setup"></a>Instellen

1. Down load PerfView. exe en PerfView64. exe van [github](https://github.com/Microsoft/perfview/releases).
2. Start PerfView64. exe.
3. Vouw **Geavanceerde opties**uit.
4. Schakel deze selectie vakjes uit:
    - **Telefoon**
    - **Samen**
    - **.NET-symbool verzameling**
5. Deze **extra providers**instellen: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Logboeken verzamelen

1. Voer in een opdracht console met beheerders bevoegdheden de `iisreset /stop` opdracht uit om IIS en alle web-apps uit te scha kelen.
2. Selecteer in PerfView de optie **verzameling starten**.
3. Voer in een opdracht console met beheerders bevoegdheden de `iisreset /start` opdracht uit om IIS te starten.
4. Probeer naar uw app te bladeren.
5. Nadat de app is geladen, keert u terug naar PerfView en selecteert u **verzameling stoppen**.



## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de [API-verwijzing](status-monitor-v2-overview.md#powershell-api-reference) voor meer informatie over de para meters die u mogelijk hebt gemist.
- Als u een probleem ondervindt dat hier niet wordt vermeld, kunt u contact met ons opnemen op [github](https://github.com/Microsoft/ApplicationInsights-Home/issues).
