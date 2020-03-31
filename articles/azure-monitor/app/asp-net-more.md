---
title: Haal meer uit Azure Application Insights | Microsoft Documenten
description: Nadat je bent begonnen met Application Insights, vind je hier een overzicht van de functies die je verkennen.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 3c4a9a736f34d4f7dbfeb004d0837f1f2efa55dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77666021"
---
# <a name="more-telemetry-from-application-insights"></a>Meer telemetrie van Application Insights
Nadat u Application Insights aan uw ASP.NET code hebt [toegevoegd,](../../azure-monitor/app/asp-net.md)zijn er een paar dingen die u doen om nog meer telemetrie te krijgen. 

| Actie | Wat u krijgt|
|---|---|
|(IIS-servers) [Installeer statusmonitor](https://go.microsoft.com/fwlink/?LinkId=506648) op elke servermachine.<br/>(Azure-webapps) Open het blade Application Insights in het Azure-configuratiescherm voor de web-app.| [**Prestatiemeteritems**](../../azure-monitor/app/performance-counters.md)<br/>[**Uitzonderingen**](asp-net-exceptions.md) - gedetailleerde stapelsporen<br/>[**Afhankelijkheden**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Het JavaScript-fragment toevoegen aan uw webpagina's](../../azure-monitor/app/javascript.md)|[Paginaprestaties](../../azure-monitor/app/usage-overview.md), browseruitzonderingen, AJAX-prestaties. Aangepaste client-side telemetrie.|
|[Webtests voor beschikbaarheid maken](../../azure-monitor/app/monitor-web-app-availability.md)|Meldingen ontvangen als uw site niet beschikbaar is|
|[Zorg ervoor dat buildinfo.config](https://msdn.microsoft.com/library/dn449058.aspx) wordt gegenereerd door MSBuild|[Annotaties maken in metrische grafieken](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[Aangepaste gebeurtenissen en statistieken schrijven](../../azure-monitor/app/api-custom-events-metrics.md)|Tel zakelijke evenementen en statistieken, houd gedetailleerd gebruik bij en meer.|
|[Profiel van uw live site](https://aka.ms/AIProfilerPreview)|Gedetailleerde functietimings van uw live web-app|






