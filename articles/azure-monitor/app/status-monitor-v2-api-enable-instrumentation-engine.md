---
title: 'Azure-toepassing Insights API-referentie voor agent: instrumentatie-engine inschakelen | Microsoft Docs'
description: Application Insights agent API-verwijzing. Enable-InstrumentationEngine. Bewaak de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET-Web-apps die on-premises worden gehost, in Vm's of op Azure.
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
ms.openlocfilehash: 9958121c38b96dc9cfa4dda2812fa9ce2b18d785
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388284"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>Application Insights agent-API: Enable-InstrumentationEngine

In dit artikel wordt een cmdlet beschreven die lid is van de [Power shell-module AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Beschrijving

Hiermee schakelt u de instrumentatie-engine in door een aantal register sleutels in te stellen.
Start IIS opnieuw op om de wijzigingen van kracht te laten worden.

De instrumentatie-engine kan gegevens aanvullen die worden verzameld door de .NET-Sdk's.
Er worden gebeurtenissen en berichten verzameld waarmee de uitvoering van een beheerd proces wordt beschreven. Deze gebeurtenissen en berichten bevatten afhankelijkheids resultaat codes, HTTP-woorden en [SQL-opdracht tekst](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Schakel de instrumentatie-engine in als:
- U hebt bewaking al ingeschakeld met de cmdlet Enable, maar de instrumentatie-engine is niet ingeschakeld.
- U hebt uw app hand matig geinstrumenteerd met de .NET-Sdk's en wilt extra telemetrie verzamelen.

> [!IMPORTANT] 
> Voor deze cmdlet is een Power shell-sessie met beheerders machtigingen vereist.

> [!NOTE] 
> - Voor deze cmdlet moet u onze licentie en privacyverklaring bekijken en accepteren.
> - De instrumentatie-engine voegt extra overhead toe en is standaard uitgeschakeld.

## <a name="examples"></a>Voorbeelden

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parameters

### <a name="-acceptlicense"></a>-AcceptLicense
**Beschrijving.** Gebruik deze schakel optie om de licentie en privacyverklaring in headless installaties te accepteren.

### <a name="-verbose"></a>-Verbose
**Algemene para meter.** Gebruik deze optie om gedetailleerde logboeken uit te voeren.

## <a name="output"></a>Uitvoer


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Voorbeeld uitvoer van het inschakelen van de instrumentatie-engine

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>Volgende stappen

  Uw telemetrie weergeven:
 - [Bekijk metrische gegevens](../../azure-monitor/app/metrics-explorer.md) om de prestaties en het gebruik te bewaken.
- [Zoek gebeurtenissen en logboeken](../../azure-monitor/app/diagnostic-search.md) om problemen op te sporen.
- Gebruik [analyses](../../azure-monitor/app/analytics.md) voor meer geavanceerde query's.
- [Dash boards maken](../../azure-monitor/app/overview-dashboard.md).
 
 Meer telemetrie toevoegen:
 - [Maak webtests](monitor-web-app-availability.md) om ervoor te zorgen dat uw site actief blijft.
- [Voeg de telemetrie van de webclient](../../azure-monitor/app/javascript.md) toe om uitzonde ringen van webpagina code te bekijken en tracerings aanroepen in te scha kelen.
- [Voeg de Application INSIGHTS SDK toe aan uw code](../../azure-monitor/app/asp-net.md) zodat u tracerings-en logboek aanroepen kunt invoegen.
 
 Meer doen met Application Insights agent:
 - Gebruik onze hand leiding om Application Insights-agent op te [lossen](status-monitor-v2-troubleshoot.md) .
 - Stel [de configuratie](status-monitor-v2-api-get-config.md) in om te bevestigen dat de instellingen correct zijn geregistreerd.
 - [De status ophalen om de](status-monitor-v2-api-get-status.md) bewaking te controleren.
