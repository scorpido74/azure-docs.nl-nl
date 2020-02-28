---
title: 'Application Insights: talen, platforms en integraties | Microsoft Docs'
description: Talen, platforms en integraties die beschikbaar zijn voor Application Insights
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: d87b1d0aa74263f249db2030f2366b93b8c2b421
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669825"
---
# <a name="supported-languages"></a>Ondersteunde talen

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)
* [Python (preview-versie)](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Ondersteunde platforms en frameworks

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentatie voor toepassingen die al zijn geïmplementeerd (zonder code, op basis van een agent)
* [Azure VM-en Azure virtual machine-schaal sets](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET - voor apps die al live zijn](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Azure Cloud Services](../../azure-monitor/app/cloudservices.md), inclusief de web-en werk rollen
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-monitoring)
### <a name="instrumentation-through-code-sdks"></a>Instrumentatie via code (Sdk's)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python (preview-versie)](../../azure-monitor/app/opencensus-python.md)
* [Universele Windows-app](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Windows-bureaubladtoepassingen, -services en -werkrollen](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Frameworks voor logboekregistratie
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog of System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J of Logback](../../azure-monitor/app/java-trace-logs.md)
* [LogStash-invoegtoepassing](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Exporteren en gegevens analyse
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>Niet-ondersteunde SDK's
Er is een aantal andere door de Community ondersteunde Sdk's aanwezig. Azure Monitor biedt echter alleen ondersteuning bij het gebruik van de ondersteunde Sdk's die op deze pagina worden weer gegeven. We evalueren voortdurend de mogelijkheden om onze ondersteuning voor andere talen uit te breiden. Volg daarom onze pagina met [github-meldingen](https://github.com/microsoft/ApplicationInsights-Announcements/issues) om het nieuwste SDK-nieuws te ontvangen. 
