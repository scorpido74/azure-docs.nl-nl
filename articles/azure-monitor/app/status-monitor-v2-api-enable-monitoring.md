---
title: API-naslag informatie over Azure-toepassing Insights-agent
description: Application Insights agent API-verwijzing. Enable-ApplicationInsightsMonitoring. Bewaak de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET-Web-apps die on-premises worden gehost, in Vm's of op Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 8bbdc96a49fffc91f80d24a9eb0926766f86ee16
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671304"
---
# <a name="application-insights-agent-api-enable-applicationinsightsmonitoring"></a>Application Insights agent-API: Enable-ApplicationInsightsMonitoring

In dit artikel wordt een cmdlet beschreven die lid is van de [Power shell-module AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beschrijving

Hiermee wordt de bewaking van de IIS-apps op een doel computer gecodeerd.

Met deze cmdlet wordt de IIS-functie applicationHost. config gewijzigd en worden enkele register sleutels ingesteld.
Er wordt ook een bestand applicationinsights. iKey. config gemaakt, waarmee de instrumentatie sleutel wordt gedefinieerd die door elke app wordt gebruikt.
IIS laadt de RedfieldModule bij het opstarten, waardoor de SDK van Application Insights in toepassingen wordt ingevoegd wanneer de toepassingen worden gestart.
Start IIS opnieuw op om de wijzigingen van kracht te laten worden.

Nadat u bewaking hebt ingeschakeld, raden we u aan [Live metrische gegevens](live-stream.md) te gebruiken om snel te controleren of uw app een telemetrie verzendt.


> [!NOTE] 
> - U hebt een instrumentatie sleutel nodig om aan de slag te gaan. Zie [een resource maken](create-new-resource.md#copy-the-instrumentation-key)voor meer informatie.
> - Voor deze cmdlet moet u onze licentie en privacyverklaring bekijken en accepteren.

> [!IMPORTANT] 
> Voor deze cmdlet is een Power shell-sessie met beheerders machtigingen en een verhoogd uitvoerings beleid vereist. Zie [Power shell uitvoeren als Administrator met een verhoogd uitvoerings beleid](status-monitor-v2-detailed-instructions.md#run-powershell-as-admin-with-an-elevated-execution-policy)voor meer informatie.

## <a name="examples"></a>Voorbeelden

### <a name="example-with-a-single-instrumentation-key"></a>Voor beeld met één instrumentatie sleutel
In dit voor beeld worden alle apps op de huidige computer toegewezen aan één instrumentatie sleutel.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-an-instrumentation-key-map"></a>Voor beeld met een instrumentatie sleutel toewijzing
In dit voorbeeld:
- `MachineFilter` komt overeen met de huidige computer met behulp van het `'.*'` Joker teken.
- `AppFilter='WebAppExclude'` biedt een `null` instrumentatie sleutel. De opgegeven app wordt niet geinstrumenteerd.
- `AppFilter='WebAppOne'` wijst de opgegeven app een unieke instrumentatie sleutel toe.
- `AppFilter='WebAppTwo'` wijst de opgegeven app een unieke instrumentatie sleutel toe.
- Ten slotte gebruikt `AppFilter` ook het Joker teken `'.*'` om alle web-apps te zoeken die niet overeenkomen met de eerdere regels en een standaard instrumentatie sleutel toe te wijzen.
- Spaties worden toegevoegd voor de Lees baarheid.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'}},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'}},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'}})

```


## <a name="parameters"></a>Parameters

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Vereist.** Gebruik deze para meter om één instrumentatie sleutel op te geven voor gebruik door alle apps op de doel computer.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Vereist.** Gebruik deze para meter om meerdere instrumentatie sleutels en een toewijzing van de instrumentatie sleutels voor elke app op te geven.
U kunt één installatie script maken voor verschillende computers door `MachineFilter`in te stellen.

> [!IMPORTANT]
> Apps komen overeen met regels in de volg orde waarin de regels worden opgegeven. Daarom moet u eerst de meest specifieke regels opgeven en de meest algemene regels als laatste.

#### <a name="schema"></a>Schema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationSettings=@{InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'}})`

- **MachineFilter** is een vereiste C# regex van de naam van de computer of virtuele machine.
    - '. * ' komt overeen met alles
    - ComputerName komt alleen overeen met computers met de exacte naam opgegeven.
- **AppFilter** is een vereiste C# regex van de IIS-site naam. U kunt een lijst met sites op uw server ophalen door de opdracht [Get-iissite](https://docs.microsoft.com/powershell/module/iisadministration/get-iissite)uit te voeren.
    - '. * ' komt overeen met alles
    - ' Site naam ' komt alleen overeen met de IIS-site met de exacte naam die is opgegeven.
- **InstrumentationKey** is vereist om de bewaking van apps die overeenkomen met de voor gaande twee filters in te scha kelen.
    - Laat deze waarde leeg als u regels wilt definiëren om bewaking uit te sluiten.


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Beschrijving.** Gebruik deze schakel optie om de instrumentatie-engine in te scha kelen voor het verzamelen van gebeurtenissen en berichten over wat er gebeurt tijdens de uitvoering van een beheerd proces. Deze gebeurtenissen en berichten bevatten afhankelijkheids resultaat codes, HTTP-woorden en SQL-opdracht tekst.

De instrumentatie-engine voegt overhead toe en is standaard uitgeschakeld.

### <a name="-acceptlicense"></a>-AcceptLicense
**Beschrijving.** Gebruik deze schakel optie om de licentie en privacyverklaring in headless installaties te accepteren.

### <a name="-ignoresharedconfig"></a>-IgnoreSharedConfig
Wanneer u een cluster van webservers hebt, kunt u gebruikmaken van een [gedeelde configuratie](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211).
De HTTP module kan niet worden ingevoegd in deze gedeelde configuratie.
Dit script werkt niet met het bericht dat er extra installatie stappen zijn vereist.
Gebruik deze schakel optie om deze controle te negeren en door te gaan met de installatie van vereisten. Zie [bekende conflict-with-IIS-Shared-Configuration](status-monitor-v2-troubleshoot.md#conflict-with-iis-shared-configuration) (Engelstalig) voor meer informatie

### <a name="-verbose"></a>-Verbose
**Algemene para meter.** Gebruik deze schakel optie om gedetailleerde logboeken weer te geven.

### <a name="-whatif"></a>-WhatIf 
**Algemene para meter.** Gebruik deze schakel optie om de invoer parameters te testen en te valideren zonder dat de bewaking werkelijk wordt ingeschakeld.

## <a name="output"></a>Uitvoer


#### <a name="example-output-from-a-successful-enablement"></a>Voorbeeld uitvoer van een geslaagde activering

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
