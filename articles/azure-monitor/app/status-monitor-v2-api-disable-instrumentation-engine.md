---
title: 'API-naslag informatie over Azure-toepassing Insights-agent: instrumentatie-engine uitschakelen | Microsoft Docs'
description: Application Insights agent API-verwijzing. Disable-InstrumentationEngine. Bewaak de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET-Web-apps die on-premises worden gehost, in Vm's of op Azure.
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
ms.openlocfilehash: aaf493b5f2075ead62087824773f21643a1246e4
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388310"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>Application Insights agent-API: Disable-InstrumentationEngine

In dit artikel wordt een cmdlet beschreven die lid is van de [Power shell-module AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beschrijving
Hiermee schakelt u de instrumentatie-engine uit door enkele register sleutels te verwijderen.
Start IIS opnieuw op om de wijzigingen van kracht te laten worden.

> [!IMPORTANT] 
> Voor deze cmdlet is een Power shell-sessie met beheerders machtigingen vereist.

## <a name="examples"></a>Voorbeelden

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Parameters 

### <a name="-verbose"></a>-Verbose
**Algemene para meter.** Gebruik deze optie om gedetailleerde logboeken uit te voeren.

## <a name="output"></a>Uitvoer


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Voorbeeld uitvoer van het uitschakelen van de instrumentatie-engine

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Volgende stappen

 Meer doen met Application Insights agent:
 - Gebruik onze hand leiding om Application Insights-agent op te [lossen](status-monitor-v2-troubleshoot.md) .
