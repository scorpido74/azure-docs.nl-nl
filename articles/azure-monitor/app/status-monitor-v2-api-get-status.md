---
title: Api-verwijzing naar Azure Application Insights Agent-api
description: Api-referentie van Application Insights Agent. Get-ApplicationInsightsMonitoringStatus. Monitor de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET-web-apps die on-premises worden gehost, die in virtuele machines worden gehost en die via Azure worden gehost.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 159dab4a228c822ef62c45c9ccceff638a9bea45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671253"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringstatus"></a>Application Insights Agent API: Get-ApplicationInsightsMonitoringStatus

In dit artikel wordt een cmdlet beschreven die lid is van de [Az.ApplicationMonitor PowerShell-module.](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)

## <a name="description"></a>Beschrijving

Deze cmdlet geeft informatie over probleemoplossing over statusmonitor.
Gebruik deze cmdlet om de bewakingsstatus, versie van de PowerShell Module, te onderzoeken en om het loopproces te inspecteren.
Deze cmdlet rapporteert versie-informatie en informatie over belangrijke bestanden die nodig zijn voor monitoring.

> [!IMPORTANT] 
> Deze cmdlet vereist een PowerShell-sessie met beheerdersmachtigingen.

## <a name="examples"></a>Voorbeelden

### <a name="example-application-status"></a>Voorbeeld: Toepassingsstatus

Voer de `Get-ApplicationInsightsMonitoringStatus` opdracht uit om de bewakingsstatus van websites weer te geven.

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

In dit voorbeeld;
- **Machine Identifier** is een anonieme id die wordt gebruikt om uw server op unieke wijze te identificeren. Als u een ondersteuningsverzoek maakt, hebben we deze id nodig om logboeken voor uw server te vinden.
- **Standaardwebsite** wordt gestopt in IIS
- **DemoWebApp111** is gestart in IIS, maar heeft geen verzoeken ontvangen. Dit rapport laat zien dat er geen lopend proces is (ProcessId: niet gevonden).
- **DemoWebApp222** is actief en wordt gemonitord (Instrumented: true). Op basis van de gebruikersconfiguratie werd Instrumentation Key xxxxxxxx-xxxx-xxxx-xxxxxxxxxxxx123 gematcht voor deze site.
- **DemoWebApp333** is handmatig uitgerust met de Application Insights SDK. Status monitor gedetecteerd de SDK en zal niet controleren van deze site.


### <a name="example-powershell-module-information"></a>Voorbeeld: PowerShell-module-informatie

Voer de `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` opdracht uit om informatie over de huidige module weer te geven:

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

### <a name="example-runtime-status"></a>Voorbeeld: Runtime-status

U het proces op de geinstrumenteerde computer inspecteren om te zien of alle DLL's zijn geladen. Als de bewaking werkt, moeten ten minste 12 DLL's worden geladen.

Voer de `Get-ApplicationInsightsMonitoringStatus -InspectProcess`opdracht uit:


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

### <a name="no-parameters"></a>(Geen parameters)

Standaard rapporteert deze cmdlet de bewakingsstatus van webapplicaties.
Gebruik deze optie om te controleren of uw aanvraag is uitgevoerd.
U ook bekijken welke instrumentatiesleutel is gekoppeld aan uw site.


### <a name="-powershellmodule"></a>-PowerShellModule
**Optioneel**. Gebruik deze schakelaar om de versienummers en paden van DLL's te rapporteren die nodig zijn voor controle.
Gebruik deze optie als u de versie van een DLL wilt identificeren, inclusief de Application Insights SDK.

### <a name="-inspectprocess"></a>-Inspecteerproces

**Optioneel**. Gebruik deze schakelaar om te melden of IIS wordt uitgevoerd.
Het zal ook externe tools downloaden om te bepalen of de benodigde DLL's in de IIS-runtime worden geladen.


Als dit proces om welke reden dan ook mislukt, u deze opdrachten handmatig uitvoeren:
- iisreset.exe /status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"


### <a name="-force"></a>-Force

**Optioneel**. Alleen gebruikt met InspectProcess. Gebruik deze schakelaar om de gebruikersprompt over te slaan die wordt weergegeven voordat extra hulpprogramma's worden gedownload.


## <a name="next-steps"></a>Volgende stappen

 Doe meer met Application Insights Agent:
 - Gebruik onze gids om application insights agent op te [lossen.](status-monitor-v2-troubleshoot.md)
