---
title: Api-verwijzing naar Azure Application Insights Agent-api
description: Api-referentie van Application Insights Agent. Enable-InstrumentationEngine. Monitor de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET web-apps die on-premises, in VM's of op Azure worden gehost.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: b3f298ac31cc584cd16553186359c87f69f27aad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671350"
---
# <a name="application-insights-agent-api-enable-instrumentationengine"></a>Application Insights Agent API: Enable-InstrumentationEngine

In dit artikel wordt een cmdlet beschreven die lid is van de [Az.ApplicationMonitor PowerShell-module.](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)

## <a name="description"></a>Beschrijving

Hiermee schakelt u de instrumentatie-engine in door enkele registersleutels in te stellen.
Start IIS opnieuw op om de wijzigingen van kracht te laten worden.

De instrumentatie-engine kan gegevens die door de .NET SDK's zijn verzameld, aanvullen.
Het verzamelt gebeurtenissen en berichten die de uitvoering van een beheerd proces beschrijven. Deze gebeurtenissen en berichten omvatten afhankelijkheidsresultaatcodes, HTTP-werkwoorden en [SQL-opdrachttekst](asp-net-dependencies.md#advanced-sql-tracking-to-get-full-sql-query).

Schakel de instrumentatiemotor in als:
- U hebt de bewaking al ingeschakeld met de enable cmdlet, maar u hebt de instrumentatiemotor niet ingeschakeld.
- U hebt uw app handmatig uitgerust met de .NET SDK's en wilt extra telemetrie verzamelen.

> [!IMPORTANT] 
> Deze cmdlet vereist een PowerShell-sessie met beheerdersmachtigingen.

> [!NOTE] 
> - Deze cmdlet vereist dat u onze licentie- en privacyverklaring beoordeelt en accepteert.
> - De instrumentatie-engine voegt extra overhead toe en is standaard uitgeschakeld.

## <a name="examples"></a>Voorbeelden

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parameters

### <a name="-acceptlicense"></a>-AcceptLicense
**Optionele.** Gebruik deze schakelaar om de licentie- en privacyverklaring in headless-installaties te accepteren.

### <a name="-verbose"></a>-Verbose
**Algemene parameter.** Gebruik deze schakelaar om gedetailleerde logboeken uit te schakelen.

## <a name="output"></a>Uitvoer


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Voorbeeldvan het inschakelen van de instrumentatiemotor

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
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
 - [Laat de config](status-monitor-v2-api-get-config.md) bevestigen dat uw instellingen correct zijn geregistreerd.
 - [Zorg dat de status de](status-monitor-v2-api-get-status.md) controle inspecteert.
