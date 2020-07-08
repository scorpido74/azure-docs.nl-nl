---
title: 'Application Insights: talen, platforms en integraties | Microsoft Docs'
description: Talen, platforms en integraties die beschikbaar zijn voor Application Insights
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: 153d4ad3d95c182dcc4f2aa3bad857d7e1984cc2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82891114"
---
# <a name="supported-languages"></a>Ondersteunde talen

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)

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
* [Python](../../azure-monitor/app/opencensus-python.md)
* [Universele Windows-app](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Windows-bureaubladtoepassingen, -services en -werkrollen](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Frameworks voor logboekregistratie
* [ILogger](https://docs.microsoft.com/azure/azure-monitor/app/ilogger)
* [Log4Net, NLog of System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J of Logback](../../azure-monitor/app/java-trace-logs.md)
* [LogStash-invoegtoepassing](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)

## <a name="export-and-data-analysis"></a>Exporteren en gegevens analyse
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>Niet-ondersteunde SDK's
Er is een aantal andere door de Community ondersteunde Sdk's aanwezig. Azure Monitor biedt echter alleen ondersteuning bij het gebruik van de ondersteunde Sdk's die op deze pagina worden weer gegeven. We evalueren voortdurend de mogelijkheden om onze ondersteuning voor andere talen uit te breiden. Volg daarom onze pagina met [github-meldingen](https://github.com/microsoft/ApplicationInsights-Announcements/issues) om het nieuwste SDK-nieuws te ontvangen. 
