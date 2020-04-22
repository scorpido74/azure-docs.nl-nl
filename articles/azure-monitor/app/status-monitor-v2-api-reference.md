---
title: Verwijzing naar Azure Application Insights .Net Agent API
description: Api-referentie van Application Insights Agent. Monitor de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET web-apps die on-premises, in VM's of op Azure worden gehost.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 02762c4b3af735eb0b4c19aaf450b2b3a416a2be
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733672"
---
# <a name="azure-monitor-application-insights-agent-api-reference"></a>Api-verwijzing naar Azure Monitor Application Insights Agent API

In dit artikel wordt een cmdlet beschreven die lid is van de [Az.ApplicationMonitor PowerShell-module.](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)

> [!NOTE] 
> - Om te beginnen heb je een instrumentatiesleutel nodig. Zie [Een resource maken](create-new-resource.md#copy-the-instrumentation-key)voor meer informatie.
> - Deze cmdlet vereist dat u onze licentie- en privacyverklaring beoordeelt en accepteert.

> [!IMPORTANT] 
> Deze cmdlet vereist een PowerShell-sessie met beheerdersmachtigingen en een verhoogd uitvoeringsbeleid. Zie [PowerShell uitvoeren als beheerder met een verhoogd uitvoeringsbeleid](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)voor meer informatie.
> - Deze cmdlet vereist dat u onze licentie- en privacyverklaring beoordeelt en accepteert.
> - De instrumentatie-engine voegt extra overhead toe en is standaard uitgeschakeld.


## <a name="enable-instrumentationengine"></a>Enable-InstrumentationEngine

Hiermee schakelt u de instrumentatie-engine in door enkele registersleutels in te stellen.
Start IIS opnieuw op om de wijzigingen van kracht te laten worden.

De instrumentatie-engine kan gegevens die door de .NET SDK's zijn verzameld, aanvullen.
Het verzamelt gebeurtenissen en berichten die de uitvoering van een beheerd proces beschrijven. Deze gebeurtenissen en berichten omvatten afhankelijkheidsresultaatcodes, HTTP-werkwoorden en [SQL-opdrachttekst](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Schakel de instrumentatiemotor in als:
- U hebt de bewaking al ingeschakeld met de enable cmdlet, maar u hebt de instrumentatiemotor niet ingeschakeld.
- U hebt uw app handmatig uitgerust met de .NET SDK's en wilt extra telemetrie verzamelen.

### <a name="examples"></a>Voorbeelden

```powershell
PS C:\> Enable-InstrumentationEngine
```

### <a name="parameters"></a>Parameters

#### <a name="-acceptlicense"></a>-AcceptLicense
**Optionele.** Gebruik deze schakelaar om de licentie- en privacyverklaring in headless-installaties te accepteren.

#### <a name="-verbose"></a>-Verbose
**Algemene parameter.** Gebruik deze schakelaar om gedetailleerde logboeken uit te schakelen.

### <a name="output"></a>Uitvoer


##### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Voorbeeldvan het inschakelen van de instrumentatiemotor

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="enable-applicationinsightsmonitoring"></a>Enable-ApplicationInsightsMonitoring

Hiermee u codeloze controle van IIS-apps op een doelcomputer inschakelen.

Deze cmdlet wijzigt de IIS-toepassingHost.config en stelt enkele registersleutels in.
Het zal ook een applicationinsights.ikey.config-bestand maken, dat de instrumentatiesleutel definieert die door elke app wordt gebruikt.
IIS zal de RedfieldModule laden bij het opstarten, die de Application Insights SDK zal injecteren in toepassingen als de toepassingen beginnen.
Start IIS opnieuw op om uw wijzigingen van kracht te laten worden.

Nadat u bewaking hebt ingeschakeld, raden we u aan [live statistieken](live-stream.md) te gebruiken om snel te controleren of uw app ons telemetrie stuurt.

### <a name="examples"></a>Voorbeelden

#### <a name="example-with-a-single-instrumentation-key"></a>Voorbeeld met één instrumentatietoets
In dit voorbeeld krijgen alle apps op de huidige computer één instrumentatiesleutel toegewezen.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Voorbeeld met een instrumentatiesleutelkaart
In dit voorbeeld:
- `MachineFilter`komt overeen met `'.*'` de huidige computer met behulp van de wildcard.
- `AppFilter='WebAppExclude'`biedt `null` een instrumentatiesleutel. De opgegeven app wordt niet geinstrumenteerd.
- `AppFilter='WebAppOne'`wijst de opgegeven app een unieke instrumentatiesleutel toe.
- `AppFilter='WebAppTwo'`wijst de opgegeven app een unieke instrumentatiesleutel toe.
- Tot `AppFilter` slot gebruikt `'.*'` u de wildcard ook om alle web-apps aan te passen die niet overeenkomen met de eerdere regels en een standaardinstrumentatiesleutel toe te wijzen.
- Spaties worden toegevoegd voor leesbaarheid.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


### <a name="parameters"></a>Parameters

#### <a name="-instrumentationkey"></a>-Instrumentatiesleutel
**Vereist.** Gebruik deze parameter om één instrumentatiesleutel te leveren voor gebruik door alle apps op de doelcomputer.

#### <a name="-instrumentationkeymap"></a>-InstrumentatieKeyMap
**Vereist.** Gebruik deze parameter om meerdere instrumentatietoetsen en een toewijzing van de instrumentatietoetsen die door elke app worden gebruikt, te leveren.
U één installatiescript voor meerdere `MachineFilter`computers maken door.

> [!IMPORTANT]
> Apps komen overeen met de regels in de volgorde waarin de regels worden verstrekt. U moet dus eerst de meest specifieke regels opgeven en de meest algemene regels duren.

##### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** is een vereiste C# regex van de computer of VM-naam.
    - '.*' komt overeen met alle
    - 'ComputerName' komt alleen overeen met computers met de exacte opgegeven naam.
- **AppFilter** is een vereiste C# regex van de IIS-sitenaam. U een lijst met sites op uw server krijgen door de [opdrachtget-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)uit te voeren.
    - '.*' komt overeen met alle
    - 'SiteName' komt alleen overeen met de IIS-site met de opgegeven exacte naam.
- **InstrumentationKey** is vereist om het controleren van apps die overeenkomen met de voorgaande twee filters mogelijk te maken.
    - Laat deze waarde nietig als u regels wilt definiëren om controle uit te sluiten.


#### <a name="-enableinstrumentationengine"></a>-Instrumentatiemotor inschakelen
**Optionele.** Gebruik deze schakelaar om de instrumentatie-engine in staat te stellen gebeurtenissen en berichten te verzamelen over wat er gebeurt tijdens de uitvoering van een beheerd proces. Deze gebeurtenissen en berichten omvatten afhankelijkheidsresultaatcodes, HTTP-werkwoorden en SQL-opdrachttekst.

De instrumentatie-engine voegt overhead toe en is standaard uitgeschakeld.

#### <a name="-acceptlicense"></a>-AcceptLicense
**Optionele.** Gebruik deze schakelaar om de licentie- en privacyverklaring in headless-installaties te accepteren.

#### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Wanneer u een cluster webservers hebt, gebruikt u mogelijk een [gedeelde configuratie.](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)
De HttpModule kan niet in deze gedeelde configuratie worden geïnjecteerd.
Dit script mislukt met het bericht dat extra installatiestappen vereist zijn.
Gebruik deze schakelaar om deze controle te negeren en doorgaan met het installeren van vereisten. Zie voor meer informatie [bekende conflict-met-iis-gedeelde configuratie](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

#### <a name="-verbose"></a>-Verbose
**Algemene parameter.** Gebruik deze schakelaar om gedetailleerde logboeken weer te geven.

#### <a name="-whatif"></a>-WhatIf 
**Algemene parameter.** Gebruik deze schakelaar om uw invoerparameters te testen en te valideren zonder daadwerkelijk bewaking in te schakelen.

### <a name="output"></a>Uitvoer

#### <a name="example-output-from-a-successful-enablement"></a>Voorbeelduitvoer van een succesvolle enablement

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="disable-instrumentationengine"></a>Engine uitschakelen-instrumentatie

Hiermee schakelt u de instrumentatie-engine uit door enkele registersleutels te verwijderen.
Start IIS opnieuw op om de wijzigingen van kracht te laten worden.

### <a name="examples"></a>Voorbeelden

```powershell
PS C:\> Disable-InstrumentationEngine
```

### <a name="parameters"></a>Parameters 

#### <a name="-verbose"></a>-Verbose
**Algemene parameter.** Gebruik deze schakelaar om gedetailleerde logboeken uit te schakelen.

### <a name="output"></a>Uitvoer


##### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Voorbeelduitvoer van het uitschakelen van de instrumentatie-engine

```powershell
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```

## <a name="disable-applicationinsightsmonitoring"></a>Disable-ApplicationInsightsMonitoring disable-ApplicationInsights

Schakelt de bewaking op de doelcomputer uit.
Deze cmdlet verwijdert bewerkingen naar de IIS-toepassingHost.config en verwijdert registersleutels.

### <a name="examples"></a>Voorbeelden

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

### <a name="parameters"></a>Parameters 

#### <a name="-verbose"></a>-Verbose
**Algemene parameter.** Gebruik deze schakelaar om gedetailleerde logboeken weer te geven.

### <a name="output"></a>Uitvoer


##### <a name="example-output-from-successfully-disabling-monitoring"></a>Voorbeelduitvoer van het uitschakelen van de bewaking

```powershell
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
```


## <a name="get-applicationinsightsmonitoringconfig"></a>Get-ApplicationInsightsMonitoringConfig

Hiermee wordt het config-bestand opgevraagd en worden de waarden naar de console afgedrukt.

### <a name="examples"></a>Voorbeelden

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

### <a name="parameters"></a>Parameters

Geen parameters vereist.

### <a name="output"></a>Uitvoer


##### <a name="example-output-from-reading-the-config-file"></a>Voorbeelduitvoer van het lezen van het config-bestand

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="get-applicationinsightsmonitoringstatus"></a>Status van get-applicationInsightsMonitoringStatus

Deze cmdlet geeft informatie over probleemoplossing over statusmonitor.
Gebruik deze cmdlet om de bewakingsstatus, versie van de PowerShell Module, te onderzoeken en om het loopproces te inspecteren.
Deze cmdlet rapporteert versie-informatie en informatie over belangrijke bestanden die nodig zijn voor monitoring.

### <a name="examples"></a>Voorbeelden

#### <a name="example-application-status"></a>Voorbeeld: Toepassingsstatus

Voer de `Get-ApplicationInsightsMonitoringStatus` opdracht uit om de bewakingsstatus van websites weer te geven.

```powershell

PS C:\Windows\system32> Get-ApplicationInsightsMonitoringStatus

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


#### <a name="example-powershell-module-information"></a>Voorbeeld: PowerShell-module-informatie

Voer de `Get-ApplicationInsightsMonitoringStatus -PowerShellModule` opdracht uit om informatie over de huidige module weer te geven:

```powershell

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

#### <a name="example-runtime-status"></a>Voorbeeld: Runtime-status

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

### <a name="parameters"></a>Parameters

#### <a name="no-parameters"></a>(Geen parameters)

Standaard rapporteert deze cmdlet de bewakingsstatus van webapplicaties.
Gebruik deze optie om te controleren of uw aanvraag is uitgevoerd.
U ook bekijken welke instrumentatiesleutel is gekoppeld aan uw site.


#### <a name="-powershellmodule"></a>-PowerShellModule
**Optioneel**. Gebruik deze schakelaar om de versienummers en paden van DLL's te rapporteren die nodig zijn voor controle.
Gebruik deze optie als u de versie van een DLL wilt identificeren, inclusief de Application Insights SDK.

#### <a name="-inspectprocess"></a>-Inspecteerproces

**Optioneel**. Gebruik deze schakelaar om te melden of IIS wordt uitgevoerd.
Het zal ook externe tools downloaden om te bepalen of de benodigde DLL's in de IIS-runtime worden geladen.


Als dit proces om welke reden dan ook mislukt, u deze opdrachten handmatig uitvoeren:
- iisreset.exe /status
- [handle64.exe](https://docs.microsoft.com/sysinternals/downloads/handle) -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
- [listdlls64.exe](https://docs.microsoft.com/sysinternals/downloads/listdlls) w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"


#### <a name="-force"></a>-Force

**Optioneel**. Alleen gebruikt met InspectProcess. Gebruik deze schakelaar om de gebruikersprompt over te slaan die wordt weergegeven voordat extra hulpprogramma's worden gedownload.


## <a name="set-applicationinsightsmonitoringconfig"></a>Set-ApplicationInsightsMonitoringConfig

Hiermee stelt u het config-bestand in zonder een volledige herinstallatie te doen.
Start IIS opnieuw op om uw wijzigingen van kracht te laten worden.

> [!IMPORTANT] 
> Deze cmdlet vereist een PowerShell-sessie met beheerdersmachtigingen.


### <a name="examples"></a>Voorbeelden

#### <a name="example-with-a-single-instrumentation-key"></a>Voorbeeld met één instrumentatietoets
In dit voorbeeld krijgen alle apps op de huidige computer één instrumentatiesleutel toegewezen.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

#### <a name="example-with-an-instrumentation-key-map"></a>Voorbeeld met een instrumentatiesleutelkaart
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

### <a name="parameters"></a>Parameters

#### <a name="-instrumentationkey"></a>-Instrumentatiesleutel
**Vereist.** Gebruik deze parameter om één instrumentatiesleutel te leveren voor gebruik door alle apps op de doelcomputer.

#### <a name="-instrumentationkeymap"></a>-InstrumentatieKeyMap
**Vereist.** Gebruik deze parameter om meerdere instrumentatietoetsen en een toewijzing van de instrumentatietoetsen die door elke app worden gebruikt, te leveren.
U één installatiescript voor meerdere `MachineFilter`computers maken door.

> [!IMPORTANT]
> Apps komen overeen met de regels in de volgorde waarin de regels worden verstrekt. U moet dus eerst de meest specifieke regels opgeven en de meest algemene regels duren.

##### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** is een vereiste C# regex van de computer of VM-naam.
    - '.*' komt overeen met alle
    - 'ComputerName' komt alleen overeen met computers met de opgegeven naam.
- **AppFilter** is een vereiste C# regex van de computer of VM-naam.
    - '.*' komt overeen met alle
    - 'ApplicationName' komt alleen overeen met IIS-apps met de opgegeven naam.
- **InstrumentationKey** is vereist om de bewaking van de apps die overeenkomen met de voorgaande twee filters mogelijk te maken.
    - Laat deze waarde nietig als u regels wilt definiëren om controle uit te sluiten.


#### <a name="-verbose"></a>-Verbose
**Algemene parameter.** Gebruik deze schakelaar om gedetailleerde logboeken weer te geven.


### <a name="output"></a>Uitvoer

Standaard geen uitvoer.

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Voorbeeld van verbose-uitvoer van het instellen van het config-bestand via -InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

##### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Voorbeeld van verbose-uitvoer van het instellen van het config-bestand via -InstrumentationKeyMap

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

## <a name="start-applicationinsightsmonitoringtrace"></a>Start-ApplicationInsightsMonitoringTrace

Verzamelt [ETW-gebeurtenissen](https://docs.microsoft.com/windows/desktop/etw/event-tracing-portal) van de codeloze runtime voor het toevoegen van codeloos. Deze cmdlet is een alternatief voor het uitvoeren van [PerfView.](https://github.com/microsoft/perfview)

Verzamelde gebeurtenissen worden in realtime op de console afgedrukt en opgeslagen in een ETL-bestand. Het uitvoer-ETL-bestand kan door [PerfView](https://github.com/microsoft/perfview) worden geopend voor verder onderzoek.

Deze cmdlet wordt uitgevoerd totdat deze de time-outduur heeft bereikt`Ctrl + C`(standaard 5 minuten) of handmatig wordt gestopt ( ).

### <a name="examples"></a>Voorbeelden

#### <a name="how-to-collect-events"></a>Evenementen verzamelen

Normaal gesproken vragen we u gebeurtenissen te verzamelen om te onderzoeken waarom uw aanvraag niet wordt uitgevoerd.

De codeloze runtime voor het koppelen van codeloos zendt ETW-gebeurtenissen uit wanneer IIS wordt opgestart en wanneer uw toepassing wordt opgestart.

Ga als het volgende van deze evenementen:
1. Voer IIS en alle web-apps uit in een cmd-console met beheerdersrechten. `iisreset /stop`
2. Deze cmdlet uitvoeren
3. Voer IIS uit in `iisreset /start` een cmd-console met beheerdersbevoegdheden.
4. Probeer naar uw app te bladeren.
5. Nadat uw app klaar is met laden,`Ctrl + C`u deze handmatig stoppen () of wachten op de time-out.

#### <a name="what-events-to-collect"></a>Welke evenementen te verzamelen

Je hebt drie opties bij het verzamelen van evenementen:
1. Gebruik de `-CollectSdkEvents` switch om gebeurtenissen te verzamelen die worden uitgezonden vanuit de Application Insights SDK.
2. Gebruik de `-CollectRedfieldEvents` schakelaar om gebeurtenissen te verzamelen die worden uitgezonden door Statusmonitor en de Redfield Runtime. Deze logboeken zijn handig bij het diagnosticeren van IIS en het opstarten van toepassingen.
3. Gebruik beide switches om beide gebeurtenistypen te verzamelen.
4. Als er geen switch is opgegeven, worden beide gebeurtenistypen verzameld.


### <a name="parameters"></a>Parameters

#### <a name="-maxdurationinminutes"></a>-MaxDurationInMinutes
**Optionele.** Gebruik deze parameter om in te stellen hoe lang dit script gebeurtenissen moet verzamelen. Standaard is dit 5 minuten.

#### <a name="-logdirectory"></a>-LogDirectory
**Optionele.** Gebruik deze schakelaar om de uitvoermap van het ETL-bestand in te stellen. Standaard wordt dit bestand gemaakt in de map PowerShell-modules. Het volledige pad wordt weergegeven tijdens de uitvoering van het script.


#### <a name="-collectsdkevents"></a>-CollectsdkEvents
**Optionele.** Gebruik deze schakelaar om APPLICATION Insights SDK-gebeurtenissen te verzamelen.

#### <a name="-collectredfieldevents"></a>-RedfieldEvents verzamelen
**Optionele.** Gebruik deze schakelaar om gebeurtenissen te verzamelen van Statusmonitor en de Redfield runtime.

#### <a name="-verbose"></a>-Verbose
**Algemene parameter.** Gebruik deze schakelaar om gedetailleerde logboeken uit te schakelen.



### <a name="output"></a>Uitvoer


#### <a name="example-of-application-startup-logs"></a>Voorbeeld van logboeken voor het opstarten van toepassingen
```powershell
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






