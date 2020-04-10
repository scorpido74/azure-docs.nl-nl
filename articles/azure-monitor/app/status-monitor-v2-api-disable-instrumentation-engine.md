---
title: Azure Application Insights Agent Disable-InstrumentationEngine
description: Api-referentie van Application Insights Agent. Uitschakelen-InstrumentatieMotor. Monitor de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET web-apps die on-premises, in VM's of op Azure worden gehost.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 6c52d429b527c437db5bb8aaf7e3cc1f1c52af57
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998370"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>Application Insights Agent API: Disable-InstrumentationEngine

In dit artikel wordt een cmdlet beschreven die lid is van de [Az.ApplicationMonitor PowerShell-module.](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)

## <a name="description"></a>Beschrijving
Hiermee schakelt u de instrumentatie-engine uit door enkele registersleutels te verwijderen.
Start IIS opnieuw op om de wijzigingen van kracht te laten worden.

> [!IMPORTANT] 
> Deze cmdlet vereist een PowerShell-sessie met beheerdersmachtigingen.

## <a name="examples"></a>Voorbeelden

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Parameters 

### <a name="-verbose"></a>-Verbose
**Algemene parameter.** Gebruik deze schakelaar om gedetailleerde logboeken uit te schakelen.

## <a name="output"></a>Uitvoer


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Voorbeelduitvoer van het uitschakelen van de instrumentatie-engine

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Volgende stappen

 Doe meer met Application Insights Agent:
 - Gebruik onze gids om application insights agent op te [lossen.](status-monitor-v2-troubleshoot.md)
