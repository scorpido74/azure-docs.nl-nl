---
title: Haal meer uit Azure-toepassing inzichten | Microsoft Docs
description: Wanneer u aan de slag gaat met Application Insights, ziet u hier een overzicht van de functies die u kunt verkennen.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 6d30833840b62412ae03c761baacf54c939c9d33
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540023"
---
# <a name="more-telemetry-from-application-insights"></a>Meer telemetrie van Application Insights
Nadat u [Application Insights aan uw ASP.net-code hebt toegevoegd](../../azure-monitor/app/asp-net.md), zijn er enkele dingen die u kunt doen om nog meer telemetrie te krijgen. 

| Actie | Wat u krijgt|
|---|---|
|(IIS-servers) [Installeer status monitor](https://go.microsoft.com/fwlink/?LinkId=506648) op elke server machine.<br/>(Azure web apps) Open in het configuratie scherm van Azure voor de Web-App de Blade Application Insights.| [**Prestatiemeteritems**](../../azure-monitor/app/performance-counters.md)<br/>[**Uitzonde ringen**](asp-net-exceptions.md) -gedetailleerde stack traceringen<br/>[**Afhankelijkheden**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Voeg het Java script-fragment toe aan uw webpagina's](../../azure-monitor/app/javascript.md)|[Pagina prestaties](../../azure-monitor/app/usage-overview.md), browser uitzonderingen, Ajax-prestaties. Aangepaste telemetrie aan de client zijde.|
|[Beschik baarheid webtests maken](../../azure-monitor/app/monitor-web-app-availability.md)|Ontvang waarschuwingen als uw site niet beschikbaar is|
|[Zorg ervoor dat buildinfo.config](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015) is gegenereerd door MSBuild|[Aantekeningen maken in metrische grafieken](./annotations.md)
|[Aangepaste gebeurtenissen en metrische gegevens schrijven](../../azure-monitor/app/api-custom-events-metrics.md)|Zakelijke gebeurtenissen en metrische gegevens tellen, gedetailleerd gebruik bijhouden en meer.|
|[Uw live site profileren](https://aka.ms/AIProfilerPreview)|Gedetailleerde functie timing van uw Live Web-app|
