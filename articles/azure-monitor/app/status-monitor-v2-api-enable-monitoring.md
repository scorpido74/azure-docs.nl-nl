---
title: Api-verwijzing naar Azure Application Insights Agent-api
description: Api-referentie van Application Insights Agent. enable-applicationInsightsmonitoring. Monitor de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET web-apps die on-premises, in VM's of op Azure worden gehost.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 8bbdc96a49fffc91f80d24a9eb0926766f86ee16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671304"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>Application Insights Agent API: Enable-ApplicationInsightsMonitoring

In dit artikel wordt een cmdlet beschreven die lid is van de [Az.ApplicationMonitor PowerShell-module.](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)

## <a name="description"></a>Beschrijving

Hiermee u codeloze controle van IIS-apps op een doelcomputer inschakelen.

Deze cmdlet wijzigt de IIS-toepassingHost.config en stelt enkele registersleutels in.
Het zal ook een applicationinsights.ikey.config-bestand maken, dat de instrumentatiesleutel definieert die door elke app wordt gebruikt.
IIS zal de RedfieldModule laden bij het opstarten, die de Application Insights SDK zal injecteren in toepassingen als de toepassingen beginnen.
Start IIS opnieuw op om uw wijzigingen van kracht te laten worden.

Nadat u bewaking hebt ingeschakeld, raden we u aan [live statistieken](live-stream.md) te gebruiken om snel te controleren of uw app ons telemetrie stuurt.


> [!NOTE] 
> - Om te beginnen heb je een instrumentatiesleutel nodig. Zie [Een resource maken](create-new-resource.md#copy-the-instrumentation-key)voor meer informatie.
> - Deze cmdlet vereist dat u onze licentie- en privacyverklaring beoordeelt en accepteert.

> [!IMPORTANT] 
> Deze cmdlet vereist een PowerShell-sessie met beheerdersmachtigingen en een verhoogd uitvoeringsbeleid. Zie [PowerShell uitvoeren als beheerder met een verhoogd uitvoeringsbeleid](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)voor meer informatie.

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
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
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
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** is een vereiste C# regex van de computer of VM-naam.
    - '.*' komt overeen met alle
    - 'ComputerName' komt alleen overeen met computers met de exacte opgegeven naam.
- **AppFilter** is een vereiste C# regex van de IIS-sitenaam. U een lijst met sites op uw server krijgen door de [opdrachtget-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)uit te voeren.
    - '.*' komt overeen met alle
    - 'SiteName' komt alleen overeen met de IIS-site met de opgegeven exacte naam.
- **InstrumentationKey** is vereist om het controleren van apps die overeenkomen met de voorgaande twee filters mogelijk te maken.
    - Laat deze waarde nietig als u regels wilt definiëren om controle uit te sluiten.


### <a name="-enableinstrumentationengine"></a>-Instrumentatiemotor inschakelen
**Optionele.** Gebruik deze schakelaar om de instrumentatie-engine in staat te stellen gebeurtenissen en berichten te verzamelen over wat er gebeurt tijdens de uitvoering van een beheerd proces. Deze gebeurtenissen en berichten omvatten afhankelijkheidsresultaatcodes, HTTP-werkwoorden en SQL-opdrachttekst.

De instrumentatie-engine voegt overhead toe en is standaard uitgeschakeld.

### <a name="-acceptlicense"></a>-AcceptLicense
**Optionele.** Gebruik deze schakelaar om de licentie- en privacyverklaring in headless-installaties te accepteren.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Wanneer u een cluster webservers hebt, gebruikt u mogelijk een [gedeelde configuratie.](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211)
De HttpModule kan niet in deze gedeelde configuratie worden geïnjecteerd.
Dit script mislukt met het bericht dat extra installatiestappen vereist zijn.
Gebruik deze schakelaar om deze controle te negeren en doorgaan met het installeren van vereisten. Zie voor meer informatie [bekende conflict-met-iis-gedeelde configuratie](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration)

### <a name="-verbose"></a>-Verbose
**Algemene parameter.** Gebruik deze schakelaar om gedetailleerde logboeken weer te geven.

### <a name="-whatif"></a>-WhatIf 
**Algemene parameter.** Gebruik deze schakelaar om uw invoerparameters te testen en te valideren zonder daadwerkelijk bewaking in te schakelen.

## <a name="output"></a>Uitvoer


#### <a name="example-output-from-a-successful-enablement"></a>Voorbeelduitvoer van een succesvolle enablement

```
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

## <a name="next-steps"></a>Volgende stappen

  Uw telemetrie weergeven:
 - [Bekijk statistieken](../../azure-monitor/app/metrics-explorer.md) om de prestaties en het gebruik te controleren.
- [Zoek gebeurtenissen en logboeken](../../azure-monitor/app/diagnostic-search.md) om problemen te diagnosticeren.
- [Gebruik Analytics](../../azure-monitor/app/analytics.md) voor meer geavanceerde query's.
- [Dashboards maken](../../azure-monitor/app/overview-dashboard.md).
 
 Meer telemetrie toevoegen:
 - [Maak webtests](monitor-web-app-availability.md) om ervoor te zorgen dat uw site actief blijft.
- [Voeg telemetrie van webclient toe](../../azure-monitor/app/javascript.md) om uitzonderingen van webpaginacode te bekijken en tracecalls in te schakelen.
- [Voeg de Application Insights SDK toe aan uw code,](../../azure-monitor/app/asp-net.md) zodat u trace- en logoproepen invoegen.
 
 Doe meer met Application Insights Agent:
 - Gebruik onze gids om application insights agent op te [lossen.](status-monitor-v2-troubleshoot.md)
 - [Laat de config](status-monitor-v2-api-get-config.md) bevestigen dat uw instellingen correct zijn geregistreerd.
 - [Zorg dat de status de](status-monitor-v2-api-get-status.md) controle inspecteert.
