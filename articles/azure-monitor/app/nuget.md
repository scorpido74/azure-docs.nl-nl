---
title: Azure Monitor Application Insights NuGet-pakketten
description: Azure Monitor Application Insights NuGet-pakkettenlijsten voor ASP.NET, ASP.NET Core, Python
ms.topic: reference
ms.date: 10/16/2018
ms.openlocfilehash: 5675c0fad61f1b75aa7e93db02246eed43967ec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669995"
---
# <a name="application-insights-nuget-packages"></a>Application Insights NuGet-pakketten

Hieronder vindt u de huidige lijst met stabiele release NuGet-pakketten voor Application Insights.

## <a name="common-packages-for-aspnet"></a>Gemeenschappelijke pakketten voor ASP.NET

| Pakketnaam | Stabiele versie | Beschrijving | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights | 2.12.0 | Biedt kernfunctionaliteit voor de overdracht van alle telemetrietypen van Application Insights en is een afhankelijk pakket voor alle andere Application Insights-pakketten | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft.ApplicationInsights.Agent.Intercept | 2.4.0 | Maakt onderschepping van methodeaanroepen mogelijk | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Application Insights Dependency Collector voor .NET-toepassingen. Dit is een afhankelijk pakket voor Application Insights platform-specifieke pakketten en biedt automatische verzameling van afhankelijkheid telemetrie. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Met Application Insights Performance Counters Collector u gegevens die door prestatiemeteritems zijn verzameld, naar Application Insights sturen. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.Web | 2.12.0 | Application Insights voor .NET-webtoepassingen | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Application Insights Windows Server NuGet-pakket biedt automatische verzameling van telemetrie van toepassingsinzichten voor .NET-toepassingen. Dit pakket kan worden gebruikt als een afhankelijk pakket voor Application Insights-platformspecifieke pakketten of als een standalone pakket voor .NET-toepassingen die niet worden gedekt door platformspecifieke pakketten (zoals voor .NET-werknemersrollen). | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.12.0 | Biedt een telemetriekanaal naar Application Insights Windows Server SDK dat telemetrie in offline scenario's behoudt. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Algemene pakketten voor ASP.NET Core

| Pakketnaam | Stabiele versie | Beschrijving | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AspNetCore | 2.5.0 | Application Insights voor ASP.NET Core webapplicaties. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft.ApplicationInsights | 2.12.0 | Dit pakket biedt kernfunctionaliteit voor de overdracht van alle Telemetrietypen van Application Insights en is een afhankelijk pakket voor alle andere Application Insights-pakketten | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft.ApplicationInsights.DependencyCollector | 2.12.0 | Application Insights Dependency Collector voor .NET-toepassingen. Dit is een afhankelijk pakket voor Application Insights platform-specifieke pakketten en biedt automatische verzameling van afhankelijkheid telemetrie. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft.ApplicationInsights.PerfCounterCollector | 2.12.0 | Met Application Insights Performance Counters Collector u gegevens die door prestatiemeteritems zijn verzameld, naar Application Insights sturen. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft.ApplicationInsights.WindowsServer | 2.12.0 | Application Insights Windows Server NuGet-pakket biedt automatische verzameling van telemetrie van toepassingsinzichten voor .NET-toepassingen. Dit pakket kan worden gebruikt als een afhankelijk pakket voor Application Insights-platformspecifieke pakketten of als een standalone pakket voor .NET-toepassingen die niet worden gedekt door platformspecifieke pakketten (zoals voor .NET-werknemersrollen). | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel | 2.12.0 | Biedt een telemetriekanaal naar Application Insights Windows Server SDK dat telemetrie in offline scenario's behoudt. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>Algemene pakketten voor Python met OpenCensus
| Pakketnaam | Stabiele versie | Beschrijving | Download |
|-------------------------------|-----------------------|------------|----|
| opencensus-ext-azure | 1.0.0 | Toepassingsinzichten voor Python-toepassingen onder Azure Monitor via OpenCensus. | [Downloadpakket](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus-ext-django | 0.7.2 | Dit pakket biedt integratie met de Python [django](https://pypi.org/project/django/) bibliotheek. | [Downloadpakket](https://pypi.org/project/opencensus-ext-django/) |
| opencensus-ext-kolf | 0.7.3 | Dit pakket biedt integratie met de [Python-kolfbibliotheek.](https://pypi.org/project/flask/) | [Downloadpakket](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus-ext-httplib | 0.7.2 | Dit pakket biedt integratie met de Python [http.client](https://docs.python.org/3/library/http.client.html) bibliotheek voor Python3 en [httplib](https://docs.python.org/2/library/httplib.html) voor Python2. | [Downloadpakket](https://pypi.org/project/opencensus-ext-httplib/) |
| opencensus-ext-logging | 0.1.0 | Dit pakket verrijkt logboekrecords met traceergegevens. | [Downloadpakket](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus-ext-mysql | 0.1.2 | Dit pakket biedt integratie met de Python [mysql-connector-bibliotheek.](https://pypi.org/project/mysql-connector/) | [Downloadpakket](https://pypi.org/project/opencensus-ext-mysql/) |
| opencensus-ext-postgresql | 0.1.2 | Dit pakket biedt integratie met de Python [psycopg2](https://pypi.org/project/psycopg2/) bibliotheek. | [Downloadpakket](https://pypi.org/project/opencensus-ext-postgresql/) |
| opencensus-ext-pymongo | 0.7.1 | Dit pakket biedt integratie met de Python [pymongo-bibliotheek.](https://pypi.org/project/pymongo/) | [Downloadpakket](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus-ext-pymysql | 0.1.2 | Dit pakket biedt integratie met de Python [PyMySQL-bibliotheek.](https://pypi.org/project/PyMySQL/) | [Downloadpakket](https://pypi.org/project/opencensus-ext-pymysql/) |
| opencensus-ext-piramide | 0.7.1 | Dit pakket biedt integratie met de [Python-piramidebibliotheek.](https://pypi.org/project/pyramid/) | [Downloadpakket](https://pypi.org/project/opencensus-ext-pyramid/) |
| opencensus-ext-requests | 0.7.2 | Dit pakket biedt integratie met de [Python-aanvragenbibliotheek.](https://pypi.org/project/requests/) | [Downloadpakket](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus-ext-sqlalchemy | 0.1.2 | Dit pakket biedt integratie met de Python [SQLAlchemy-bibliotheek.](https://pypi.org/project/SQLAlchemy/) | [Downloadpakket](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Luisteraars/verzamelaars/appenders

| Pakketnaam | Stabiele versie | Beschrijving | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.DiagnosticSourceListener | 2.7.2 |  Hiermee u gebeurtenissen doorsturen van DiagnosticSource naar Application Insights. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft.ApplicationInsights.EventSourceListener | 2.7.2 | Application Insights EventSourceListener maakt het mogelijk om gegevens van EventSource-gebeurtenissen naar Application Insights te verzenden. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft.ApplicationInsights.EtwCollector | 2.7.2 | Application Insights EtwCollector maakt het mogelijk om gegevens van Event Tracing for Windows (ETW) naar Application Insights te verzenden. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft.ApplicationInsights.TraceListener | 2.7.2 | Een aangepaste TraceListener waarmee u tracelogberichten naar Application Insights verzenden. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft.ApplicationInsights.Log4NetAppender | 2.7.2 | Een aangepaste appender waarmee u Log4Net-logboekberichten naar Application Insights verzenden. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft.ApplicationInsights.NLogTarget | 2.7.2 |  een aangepast doel waarmee u NLog-logboekberichten naar Application Insights verzenden. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft.ApplicationInsights.SnapshotCollector | 1.3.1 | Controleert uitzonderingen in uw toepassing en verzamelt automatisch momentopnamen voor offline analyse. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Pakketnaam | Stabiele versie | Beschrijving | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.ServiceFabric | 2.2.0 | Dit pakket biedt automatische decoratie van telemetrie met de service fabric context waarin de toepassing wordt uitgevoerd. Gebruik deze NuGet niet voor Native Service Fabric-toepassingen. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft.ApplicationInsights.ServiceFabric.native | 2.2.0 | Application Insights-module voor servicefabrictoepassingen. Gebruik deze NuGet alleen voor Native Service Fabric-toepassingen. Voor toepassingen die in containers worden uitgevoerd, gebruikt u het Microsoft.ApplicationInsights.ServiceFabric-pakket. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Statusmonitor

| Pakketnaam | Stabiele versie | Beschrijving | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.Agent_x64 | 2.2.1 |  Runtime-gegevensverzameling voor x64-toepassingen mogelijk maken | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft.ApplicationInsights.Agent_x86 | 2.2.1 |  Hiermee u runtime-gegevens verzamelen voor x86-toepassingen. | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Deze pakketten maken deel uit van de kernfunctionaliteit van de runtime monitoring in [Status Monitor.](../../azure-monitor/app/monitor-performance-live-website-now.md) U hoeft deze pakketten niet direct te downloaden, gebruik gewoon de Status Monitor installer. Als u meer wilt begrijpen over hoe deze pakketten werken onder de motorkap deze [blog post](https://apmtips.com/blog/2016/11/18/how-application-insights-status-monitor-not-monitors-dependencies/) van een van onze ontwikkelaars is een goed begin.

## <a name="additional-packages"></a>Aanvullende pakketten

| Pakketnaam | Stabiele versie | Beschrijving | Download |
|-------------------------------|-----------------------|------------|----|
| Microsoft.ApplicationInsights.AzureWebSites | 2.6.5 | Met deze extensie u de monitor Application Insights op een Azure App-service inschakelen. SDK versie 2.6.1. Instructies: Voeg 'APPINSIGHTS_INSTRUMENTATIONKEY' toepassingsinstellingen toe met je ikey en start de webapp opnieuw om effect te sorteren.| [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft.ApplicationInsights.Injector | 2.6.7 | Dit pakket bevat bestanden die nodig zijn voor codeloze Application Insights-injectie | [Downloadpakket](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Volgende stappen

- Monitor [ASP.NET Kern](../../azure-monitor/app/asp-net-core.md).
- Profiel ASP.NET Core [Azure Linux-webapps](../../azure-monitor/app/profiler-aspnetcore-linux.md).
- Foutopsporing ASP.NET [momentopnamen](../../azure-monitor/app/snapshot-debugger.md).
