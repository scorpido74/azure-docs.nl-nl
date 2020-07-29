---
title: Haal meer uit Azure-toepassing inzichten | Microsoft Docs
description: Wanneer u aan de slag gaat met Application Insights, ziet u hier een overzicht van de functies die u kunt verkennen.
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: c0be377636159dbb2bdb628af6a0bc9077942397
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321340"
---
# <a name="more-telemetry-from-application-insights"></a>Meer telemetrie van Application Insights
Nadat u [Application Insights aan uw ASP.net-code hebt toegevoegd](./asp-net.md), zijn er enkele dingen die u kunt doen om nog meer telemetrie te krijgen. 

| Bewerking | Wat u krijgt|
|---|---|
|(IIS-servers) [Installeer status monitor](https://go.microsoft.com/fwlink/?LinkId=506648) op elke server machine.<br/>(Azure web apps) Open in het configuratie scherm van Azure voor de Web-App de Blade Application Insights.| [**Prestatiemeteritems**](./performance-counters.md)<br/>[**Uitzonde ringen**](asp-net-exceptions.md) -gedetailleerde stack traceringen<br/>[**Afhankelijkheden**](./asp-net-dependencies.md)|
|[Voeg het Java script-fragment toe aan uw webpagina's](./javascript.md)|[Pagina prestaties](./usage-overview.md), browser uitzonderingen, Ajax-prestaties. Aangepaste telemetrie aan de client zijde.|
|[Beschik baarheid webtests maken](./monitor-web-app-availability.md)|Ontvang waarschuwingen als uw site niet beschikbaar is|
|[Zorg ervoor dat buildinfo.config](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015) is gegenereerd door MSBuild|[Aantekeningen maken in metrische grafieken](./annotations.md)
|[Aangepaste gebeurtenissen en metrische gegevens schrijven](./api-custom-events-metrics.md)|Zakelijke gebeurtenissen en metrische gegevens tellen, gedetailleerd gebruik bijhouden en meer.|
|[Uw live site profileren](https://aka.ms/AIProfilerPreview)|Gedetailleerde functie timing van uw Live Web-app|

