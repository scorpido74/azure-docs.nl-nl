---
title: 'Naslag informatie voor Azure Status Monitor v2 API: Status ophalen | Microsoft Docs'
description: Status Monitor v2 API-referentie. Get-ApplicationInsightsMonitoringStatus. Bewaak de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET-web-apps die on-premises worden gehost, die in virtuele machines worden gehost en die via Azure worden gehost.
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
ms.openlocfilehash: c3982e7eb78c1113c73a8e7e9d7b00ad403ac486
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058261"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringstatus"></a>Status Monitor v2-API: Get-ApplicationInsightsMonitoringStatus

In dit artikel wordt een cmdlet beschreven die lid is van de [Power shell-module AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Description

Deze cmdlet biedt informatie over het oplossen van problemen met Status Monitor.
Gebruik deze cmdlet om de bewakings status, versie van de Power shell-module te onderzoeken en het actieve proces te controleren.
Met deze cmdlet worden versie-informatie en informatie over de vereiste sleutel bestanden voor bewaking gerapporteerd.

> [!IMPORTANT] 
> Voor deze cmdlet is een Power shell-sessie met beheerders machtigingen vereist.

## <a name="examples"></a>Voorbeelden

### <a name="example-application-status"></a>Voorbeeld: Toepassings status

Voer de opdracht `Get-ApplicationInsightsMonitoringStatus` uit om de bewakings status van websites weer te geven.

```
PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus
Machine Identifier:
811D43F7EC807E389FEA2E732381288ACCD70AFFF9F569559AC3A75F023FA639

IIS Websites:

SiteName               : Default Web Site
ApplicationPoolName    : DefaultAppPool
SiteId                 : 1
SiteState              : Stopped

SiteName               : DemoWebApp111
ApplicationPoolName    : DemoWebApp111
SiteId                 : 2
SiteState              : Started
ProcessId              : not found

SiteName               : DemoWebApp222
ApplicationPoolName    : DemoWebApp222
SiteId                 : 3
SiteState              : Started
ProcessId              : 2024
Instrumented           : true
InstrumentationKey     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx123

SiteName               : DemoWebApp333
ApplicationPoolName    : DemoWebApp333
SiteId                 : 4
SiteState              : Started
ProcessId              : 5184
AppAlreadyInstrumented : true
```

In dit voor beeld;
- **Machine-id** is een anonieme id die wordt gebruikt om uw server uniek te identificeren. Als u een ondersteunings aanvraag maakt, hebt u deze ID nodig om logboeken voor uw server te vinden.
- De **standaard website** is gestopt in IIS
- **DemoWebApp111** is gestart in IIS, maar heeft geen aanvragen ontvangen. Dit rapport geeft aan dat er geen proces kan worden uitgevoerd (ProcessId: niet gevonden).
- **DemoWebApp222** wordt uitgevoerd en wordt bewaakt (instrumented: True). Op basis van de gebruikers configuratie is de instrumentatie sleutel XXXXXXXX-XXXX-XXXX-XXXX-xxxxxxxxx123 overeenkomt met deze site.
- **DemoWebApp333** is hand matig geinstrumenteerd met behulp van de Application Insights SDK. De SDK is Status Monitor gedetecteerd en de site wordt niet bewaakt.


### <a name="example-powershell-module-information"></a>Voorbeeld: Informatie over de Power shell-module

Voer de opdracht `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` uit om informatie weer te geven over de huidige module:

```
PS C:\> Get-ApplicationInsightsMonitoringStatus -PowerShellModule

PowerShell Module version:
0.4.0-alpha

Application Insights SDK version:
2.9.0.3872

Executing PowerShell Module Assembly:
Microsoft.ApplicationInsights.Redfield.Configurator.PowerShell, Version=2.8.14.11432, Culture=neutral, PublicKeyToken=31bf3856ad364e35

PowerShell Module Directory:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.2\content\PowerShell

Runtime Paths:
ParentDirectory (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content

ConfigurationPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config

ManagedHttpModuleHelperPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll

RedfieldIISModulePath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

InstrumentationEngine86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\MicrosoftInstrumentationEngine_x86.dll

InstrumentationEngine64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll

InstrumentationEngineExtensionHost86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.ApplicationInsights.ExtensionsHost_x86.dll

InstrumentationEngineExtensionHost64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll

InstrumentationEngineExtensionConfig86Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation32\Microsoft.InstrumentationEngine.Extensions.config

InstrumentationEngineExtensionConfig64Path (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.InstrumentationEngine.Extensions.config

ApplicationInsightsSdkPath (Exists: True)
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll
```

### <a name="example-runtime-status"></a>Voorbeeld: Runtimestatus

U kunt het proces op de geinstrumenteerde computer controleren om te controleren of alle Dll's zijn geladen. Als de bewaking werkt, moeten ten minste 12 Dll's worden geladen.

Voer de volgende `Get-ApplicationInsightsMonitoringStatus -InspectProcess`opdracht uit:


```
PS C:\> Get-ApplicationInsightsMonitoringStatus -InspectProcess

iisreset.exe /status
Status for IIS Admin Service ( IISADMIN ) : Running
Status for Windows Process Activation Service ( WAS ) : Running
Status for Net.Msmq Listener Adapter ( NetMsmqActivator ) : Running
Status for Net.Pipe Listener Adapter ( NetPipeActivator ) : Running
Status for Net.Tcp Listener Adapter ( NetTcpActivator ) : Running
Status for World Wide Web Publishing Service ( W3SVC ) : Running

handle64.exe -accepteula -p w3wp
BF0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.ServerTelemetryChannel.dll
C58: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.AzureAppServices.dll
C68: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.DependencyCollector.dll
C78: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.WindowsServer.dll
C98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Web.dll
CBC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.PerfCounterCollector.dll
DB0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.AI.Agent.Intercept.dll
B98: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll
BB4: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.Contracts.dll
BCC: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.Redfield.Lightup.dll
BE0: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.dll

listdlls64.exe -accepteula w3wp
0x0000000019ac0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
0x00000000198b0000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
0x000000000c460000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
0x000000000ad60000  0x108000  C:\Windows\TEMP\2.4.0.0.Microsoft.ApplicationInsights.Extensions.Intercept_x64.dll
```

## <a name="parameters"></a>Parameters

### <a name="no-parameters"></a>(Geen para meters)

Met deze cmdlet wordt standaard de bewakings status van webtoepassingen gerapporteerd.
Gebruik deze optie om te controleren of uw toepassing is geinstrumenteerd.
U kunt ook controleren welke instrumentatie sleutel is afgestemd op uw site.


### <a name="-powershellmodule"></a>-PowerShellModule
**Optioneel**. Gebruik deze schakel optie om de versie nummers en paden te rapporteren van DLL-bestanden die vereist zijn voor bewaking.
Gebruik deze optie als u de versie van een DLL-bestand, inclusief de Application Insights SDK, moet identificeren.

### <a name="-inspectprocess"></a>-InspectProcess

**Optioneel**. Gebruik deze schakel optie om te rapporteren of IIS wordt uitgevoerd.
Ook worden er externe hulpprogram ma's gedownload om te bepalen of de benodigde DLL-bestanden in de IIS-runtime worden geladen.


Als dit proces om welke reden dan ook mislukt, kunt u deze opdrachten hand matig uitvoeren:
- IISReset. exe/status
- [handle64. exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p W3wp | findstr/I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64. exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) W3wp | findstr/I "InstrumentationEngine AI ApplicationInsights"


### <a name="-force"></a>-Force

**Optioneel**. Wordt alleen gebruikt met InspectProcess. Gebruik deze schakel optie om de prompt van de gebruiker over te slaan die wordt weer gegeven voordat extra hulpprogram ma's worden gedownload.


## <a name="next-steps"></a>Volgende stappen

 Meer doen met Status Monitor v2:
 - Gebruik onze hand leiding voor het [oplossen van problemen](status-monitor-v2-troubleshoot.md) status monitor v2.
