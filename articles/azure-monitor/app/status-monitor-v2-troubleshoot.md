---
title: Problemen met Azure Application Insights Agent oplossen en bekende problemen | Microsoft Documenten
description: De bekende problemen van Application Insights Agent en voorbeelden van probleemoplossing. Monitor de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET web-apps die on-premises, in VM's of op Azure worden gehost.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 217629ba5c386557455cc2d2b8bd47f85fa8f84e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671151"
---
# <a name="troubleshooting-application-insights-agent-formerly-named-status-monitor-v2"></a>Probleemoplossing Application Insights Agent (voorheen statusmonitor v2 genoemd)

Wanneer u bewaking inschakelt, u problemen ondervinden die het verzamelen van gegevens verhinderen.
In dit artikel worden alle bekende problemen weergegeven en worden voorbeelden van problemen opgelost.
Als u een probleem tegenkomt dat hier niet wordt vermeld, u contact met ons opnemen op [GitHub.](https://github.com/Microsoft/ApplicationInsights-Home/issues)

## <a name="known-issues"></a>Bekende problemen

### <a name="conflicting-dlls-in-an-apps-bin-directory"></a>Conflicterende DLL's in de opslaglocatiemap van een app

Als een van deze DLL's aanwezig is in de opslagmap, kan de controle mislukken:

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelatie.dll
- System.Diagnostics.DiagnosticSource.dll

Sommige van deze DLL's zijn opgenomen in de standaardappsjablonen van Visual Studio, zelfs als uw app ze niet gebruikt.
U hulpprogramma's voor probleemoplossing gebruiken om symptomatisch gedrag te zien:

- PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- IISReset en app load (zonder telemetrie). Onderzoeken met Sysinternals (Handle.exe en ListDLLs.exe):
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Conflict met iIS-gedeelde configuratie

Als u een cluster webservers hebt, gebruikt u mogelijk een [gedeelde configuratie.](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)
De HttpModule kan niet in deze gedeelde configuratie worden geïnjecteerd.
Voer de opdracht Inschakelen op elke webserver uit om de DLL in de GAC van elke server te installeren.

Voer de volgende stappen uit nadat u de opdracht Inschakelen hebt uitgevoerd:
1. Ga naar de gedeelde configuratiemap en zoek het bestand applicationHost.config.
2. Voeg deze regel toe aan het gedeelte modules van uw configuratie:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```

### <a name="iis-nested-applications"></a>IIS Geneste toepassingen

We instrument erniet geneste toepassingen in IIS in versie 1.0.
We volgen dit probleem [hier.](https://github.com/microsoft/ApplicationInsights-Home/issues/369)

### <a name="advanced-sdk-configuration-isnt-available"></a>Geavanceerde SDK-configuratie is niet beschikbaar.

De SDK-configuratie wordt niet blootgesteld aan de eindgebruiker in versie 1.0.
We volgen dit probleem [hier.](https://github.com/microsoft/ApplicationInsights-Home/issues/375)

    
    
## <a name="troubleshooting"></a>Problemen oplossen
    
### <a name="troubleshooting-powershell"></a>Problemen met PowerShell oplossen

#### <a name="determine-which-modules-are-available"></a>Bepalen welke modules beschikbaar zijn
U `Get-Module -ListAvailable` de opdracht gebruiken om te bepalen welke modules zijn geïnstalleerd.

#### <a name="import-a-module-into-the-current-session"></a>Een module importeren in de huidige sessie
Als een module niet is geladen in een PowerShell-sessie, `Import-Module <path to psd1>` u deze handmatig laden met behulp van de opdracht.


### <a name="troubleshooting-the-application-insights-agent-module"></a>Problemen oplossen met de module Application Insights Agent

#### <a name="list-the-commands-available-in-the-application-insights-agent-module"></a>De opdrachten weergeven die beschikbaar zijn in de module Application Insights Agent
Voer de `Get-Command -Module Az.ApplicationMonitor` opdracht uit om de beschikbare opdrachten op te halen:

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

#### <a name="determine-the-current-version-of-the-application-insights-agent-module"></a>De huidige versie van de module Application Insights Agent bepalen
Voer `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` de opdracht uit om de volgende informatie over de module weer te geven:
   - PowerShell-moduleversie
   - Sdk-versie van Application Insights
   - Bestandspaden van de PowerShell-module
    
Bekijk de [API-referentie](status-monitor-v2-api-get-status.md) voor een gedetailleerde beschrijving van het gebruik van deze cmdlet.


### <a name="troubleshooting-running-processes"></a>Problemen met lopende processen oplossen

U de processen op de geinstrumenteerde computer inspecteren om te bepalen of alle DLL's worden geladen.
Als de bewaking werkt, moeten ten minste 12 DLL's worden geladen.

Gebruik `Get-ApplicationInsightsMonitoringStatus -InspectProcess` de opdracht om de DLL's te controleren.

Bekijk de [API-referentie](status-monitor-v2-api-get-status.md) voor een gedetailleerde beschrijving van het gebruik van deze cmdlet.


### <a name="collect-etw-logs-by-using-perfview"></a>ETW-logboeken verzamelen met PerfView

#### <a name="setup"></a>Instellen

1. Download PerfView.exe en PerfView64.exe van [GitHub](https://github.com/Microsoft/perfview/releases).
2. Start PerfView64.exe.
3. **Geavanceerde opties**uitbreiden .
4. Schakel de volgende selectievakjes uit:
    - **Zip**
    - **Samenvoegen**
    - **.NET-symboolverzameling**
5. Stel deze **extra providers in:**`61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Logboeken verzamelen

1. Voer in een opdrachtconsole met `iisreset /stop` beheerdersbevoegdheden de opdracht uit om IIS en alle web-apps uit te schakelen.
2. Selecteer Verzameling **starten**in PerfView .
3. Voer in een opdrachtconsole met `iisreset /start` beheerdersbevoegdheden de opdracht uit om IIS te starten.
4. Probeer naar uw app te bladeren.
5. Nadat uw app is geladen, gaat u terug naar PerfView en selecteert u **Verzameling stoppen**.



## <a name="next-steps"></a>Volgende stappen

- Bekijk de [API-verwijzing](status-monitor-v2-overview.md#powershell-api-reference) voor meer informatie over parameters die u mogelijk hebt gemist.
- Als u een probleem tegenkomt dat hier niet wordt vermeld, u contact met ons opnemen op [GitHub.](https://github.com/Microsoft/ApplicationInsights-Home/issues)
