---
title: Overzicht van Azure Application Insights Agent | Microsoft Documenten
description: Een overzicht van Application Insights Agent. Monitor de prestaties van de website zonder de website opnieuw te implementeren. Werkt met ASP.NET web-apps die on-premises, in VM's of op Azure worden gehost.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 626a8c96fdc247db174e2cbcd2e5c99cec43d2ea
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770968"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>Azure Monitor Application Insights Agent implementeren voor on-premises servers

> [!IMPORTANT]
> Deze richtlijnen worden aanbevolen voor on-premises en niet-Azure-cloudimplementaties van Application Insights Agent. Hier is de aanbevolen aanpak voor [Azure virtuele machine en virtuele machine schaal set implementaties](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps).

Application Insights Agent (voorheen Status Monitor V2 genoemd) is een PowerShell-module die is gepubliceerd in de [PowerShell Gallery.](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)
Het vervangt [statusmonitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now).
Telemetrie wordt verzonden naar de Azure-portal, waar u uw app [controleren.](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

> [!NOTE]
> De module ondersteunt momenteel alleen codeloze instrumentatie van .NET-webapps die worden gehost met IIS. Gebruik een SDK om ASP.NET Core-, Java- en Node.js-toepassingen te gebruiken.

## <a name="powershell-gallery"></a>PowerShell Gallery

Application Insights Agent bevindt zich hier: https://www.powershellgallery.com/packages/Az.ApplicationMonitor.

![PowerShell Gallery](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instructies
- Zie de [instructies voor het starten](status-monitor-v2-get-started.md) om te beginnen met beknopte codevoorbeelden.
- Zie de [gedetailleerde instructies](status-monitor-v2-detailed-instructions.md) voor een diepe duik over hoe aan de slag te gaan.

## <a name="powershell-api-reference"></a>PowerShell API-verwijzing
- [Disable-ApplicationInsightsMonitoring disable-ApplicationInsights](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-applicationinsightsmonitoring)
- [Engine uitschakelen-instrumentatie](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#disable-instrumentationengine)
- [Enable-ApplicationInsightsMonitoring](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-applicationinsightsmonitoring)
- [Enable-InstrumentationEngine](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#enable-instrumentationengine)
- [Get-ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringconfig)
- [Status van get-applicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus)
- [Set-ApplicationInsightsMonitoringConfig](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#set-applicationinsightsmonitoringconfig)
- [Start-ApplicationInsightsMonitoringTrace](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#start-applicationinsightsmonitoringtrace)

## <a name="troubleshooting"></a>Problemen oplossen
- [Problemen oplossen](status-monitor-v2-troubleshoot.md)
- [Bekende problemen](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Veelgestelde vragen

- Ondersteunt Application Insights Agent proxy-installaties?

  *Ja.* Er zijn meerdere manieren om Application Insights Agent te downloaden. Als uw computer toegang tot internet heeft, `-Proxy` u aan boord gaan van de PowerShell Gallery met behulp van parameters.
U de module ook handmatig downloaden en op uw computer installeren of direct gebruiken.
Elk van deze opties wordt beschreven in de [gedetailleerde instructies](status-monitor-v2-detailed-instructions.md).

- Ondersteunt Status Monitor v2 ASP.NET Core-toepassingen?

  *Nee.* Zie [Application Insights for ASP.NET Core-toepassingen voor](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)instructies om het monitoren van ASP.NET Core-toepassingen mogelijk te maken. Het is niet nodig om StatusMonitor te installeren voor een ASP.NET Core-toepassing. Dit geldt zelfs als ASP.NET Core-toepassing wordt gehost in IIS.

- Hoe kan ik controleren of de enablement is geslaagd?

  - De cmdlet [Get-ApplicationInsightsMonitoringStatus](https://docs.microsoft.com/azure/azure-monitor/app/status-monitor-v2-api-reference#get-applicationinsightsmonitoringstatus) kan worden gebruikt om te controleren of de enablement is geslaagd.
  - We raden je aan [live statistieken](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) te gebruiken om snel te bepalen of je app telemetrie verzendt.

  - U [Log Analytics](../log-query/get-started-portal.md) ook gebruiken om alle cloudrollen weer te geven die momenteel telemetrie verzenden:
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>Volgende stappen

Uw telemetrie weergeven:

* [Bekijk statistieken](../../azure-monitor/platform/metrics-charts.md) om de prestaties en het gebruik te controleren.
* [Zoek gebeurtenissen en logboeken](../../azure-monitor/app/diagnostic-search.md) om problemen te diagnosticeren.
* [Gebruik Analytics](../../azure-monitor/app/analytics.md) voor meer geavanceerde query's.
* [Dashboards maken](../../azure-monitor/app/overview-dashboard.md).

Meer telemetrie toevoegen:

* [Maak webtests](monitor-web-app-availability.md) om ervoor te zorgen dat uw site actief blijft.
* [Voeg telemetrie van webclient toe](../../azure-monitor/app/javascript.md) om uitzonderingen van webpaginacode te bekijken en tracecalls in te schakelen.
* [Voeg de Application Insights SDK toe aan uw code,](../../azure-monitor/app/asp-net.md) zodat u trace- en logoproepen invoegen.

