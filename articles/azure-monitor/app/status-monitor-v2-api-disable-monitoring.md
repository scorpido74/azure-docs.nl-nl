---
title: Api-verwijzing naar Azure Application Insights Agent-api
description: Api-referentie van Application Insights Agent. disable-applicationInsightsMonitoring. Monitor de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET web-apps die on-premises, in VM's of op Azure worden gehost.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 0678186012678a68fa80a23685f8b346c8c5b859
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671287"
---
# <a name="application-insights-agent-api-disable-applicationinsightsmonitoring"></a>Application Insights Agent API: Disable-ApplicationInsightsMonitoring

In dit artikel wordt een cmdlet beschreven die lid is van de [Az.ApplicationMonitor PowerShell-module.](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)

## <a name="description"></a>Beschrijving

Schakelt de bewaking op de doelcomputer uit.
Deze cmdlet verwijdert bewerkingen naar de IIS-toepassingHost.config en verwijdert registersleutels.

> [!IMPORTANT] 
> Deze cmdlet vereist een PowerShell-sessie met beheerdersmachtigingen.

## <a name="examples"></a>Voorbeelden

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

## <a name="parameters"></a>Parameters 

### <a name="-verbose"></a>-Verbose
**Algemene parameter.** Gebruik deze schakelaar om gedetailleerde logboeken weer te geven.

## <a name="output"></a>Uitvoer


#### <a name="example-output-from-successfully-disabling-monitoring"></a>Voorbeelduitvoer van het uitschakelen van de bewaking

```
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


## <a name="next-steps"></a>Volgende stappen

 Doe meer met Application Insights Agent:
 - Gebruik onze gids om application insights agent op te [lossen.](status-monitor-v2-troubleshoot.md)
