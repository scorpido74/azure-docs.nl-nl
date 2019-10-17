---
title: Overzicht van Azure-toepassing Insights-agents | Microsoft Docs
description: Een overzicht van Application Insights agent. Bewaak de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET-Web-apps die on-premises worden gehost, in Vm's of op Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: tilee
ms.openlocfilehash: 294b0d2d91650f33f0b92179a069a8c7cd845525
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389838"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Azure Monitor Application Insights-agent implementeren voor on-premises servers

> [!IMPORTANT]
> Deze richt lijnen worden aanbevolen voor on-premises en niet-Azure Cloud implementaties van Application Insights agent. Hier volgt de aanbevolen benadering voor [Azure virtual machine en implementaties van virtuele-machine schaal sets](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps).

Application Insights-agent (voorheen met de naam Status Monitor v2) is een Power shell-module gepubliceerd op de [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor).
Het vervangt [status monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Telemetrie wordt verzonden naar de Azure Portal, waar u uw app kunt [bewaken](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) .

> [!NOTE]
> De module ondersteunt momenteel alleen codeloze instrumentatie van .NET-Web-apps die worden gehost met IIS. Gebruik een SDK om ASP.NET Core-, Java-en node. js-toepassingen te instrumenteren.

## <a name="powershell-gallery"></a>PowerShell Gallery

Application Insights-agent bevindt zich hier: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![PowerShell Gallery](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instructies
- Zie de [aan](status-monitor-v2-get-started.md) de slag-instructies om een begin te krijgen met beknopte code voorbeelden.
- Raadpleeg de [gedetailleerde instructies](status-monitor-v2-detailed-instructions.md) voor een grondige uitleg over hoe u aan de slag kunt gaan.

## <a name="powershell-api-reference"></a>Power shell API-naslag informatie
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Start-ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>Problemen oplossen
- [Problemen oplossen](status-monitor-v2-troubleshoot.md)
- [Bekende problemen](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Veelgestelde vragen

- Ondersteunt Application Insights agent proxy installaties?

  *Ja*. Er zijn meerdere manieren om Application Insights-agent te downloaden. Als uw computer toegang heeft tot internet, kunt u de PowerShell Gallery onboarden met behulp van `-Proxy`-para meters.
U kunt de module ook hand matig downloaden en installeren op uw computer of rechtstreeks gebruiken.
Elk van deze opties wordt beschreven in de [gedetailleerde instructies](status-monitor-v2-detailed-instructions.md).

- Ondersteunt Status Monitor v2 ASP.NET Core toepassingen?

  *Nee*. Zie [Application Insights voor ASP.net core toepassingen](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)voor instructies om de bewaking van ASP.net core-toepassingen in te scha kelen. Het is niet nodig om StatusMonitor te installeren voor een ASP.NET Core-toepassing. Dit geldt ook als ASP.NET Core toepassing wordt gehost in IIS.

- Hoe kan ik controleren of de activering is geslaagd?

  - De cmdlet [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) kan worden gebruikt om te controleren of de activering is geslaagd.
  - We raden u aan [Live metrische gegevens](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) te gebruiken om snel te bepalen of uw app telemetrie verzendt.

  - U kunt [log Analytics](../log-query/get-started-portal.md) ook gebruiken om alle Cloud rollen weer te geven die momenteel telemetrie verzenden:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Volgende stappen

Uw telemetrie weergeven:

* [Bekijk metrische gegevens](../../azure-monitor/app/metrics-explorer.md) om de prestaties en het gebruik te bewaken.
* [Zoek gebeurtenissen en logboeken](../../azure-monitor/app/diagnostic-search.md) om problemen op te sporen.
* [Gebruik analyses](../../azure-monitor/app/analytics.md) voor meer geavanceerde query's.
* [Dash boards maken](../../azure-monitor/app/overview-dashboard.md).

Meer telemetrie toevoegen:

* [Maak webtests](monitor-web-app-availability.md) om ervoor te zorgen dat uw site actief blijft.
* [Voeg de telemetrie van de webclient](../../azure-monitor/app/javascript.md) toe om uitzonde ringen van webpagina code te bekijken en tracerings aanroepen in te scha kelen.
* [Voeg de Application INSIGHTS SDK toe aan uw code](../../azure-monitor/app/asp-net.md) zodat u tracerings-en logboek aanroepen kunt invoegen.

