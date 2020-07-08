---
title: Azure Monitor Application Insights NuGet-pakketten
description: Azure Monitor Application Insights NuGet-pakket lijsten voor ASP.NET, ASP.NET Core, python
ms.topic: reference
ms.date: 10/16/2018
ms.openlocfilehash: 91b48aee64a6b6942719f7f4abfb444b894c728e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85807159"
---
# <a name="application-insights-nuget-packages"></a>Application Insights NuGet-pakketten

Hieronder ziet u de huidige lijst met stabiele release NuGet-pakketten voor Application Insights.

## <a name="common-packages-for-aspnet"></a>Algemene pakketten voor ASP.NET

| Pakketnaam | Stabiele versie | Description | Downloaden |
|-------------------------------|-----------------------|------------|----|
| Micro soft. ApplicationInsights | 2.12.0 | Biedt kern functionaliteit voor het verzenden van alle Application Insights Telemetry typen en is een afhankelijk pakket voor alle andere Application Insights-pakketten | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Micro soft. ApplicationInsights. agent. interceptie | 2.4.0 | Hiermee schakelt u de interceptie van methode aanroepen in | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Micro soft. ApplicationInsights. DependencyCollector | 2.12.0 | Application Insights afhankelijkheids verzamelaar voor .NET-toepassingen. Dit is een afhankelijk pakket voor Application Insights platformspecifieke pakketten en biedt automatische verzameling van afhankelijkheids-telemetrie. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Micro soft. ApplicationInsights. PerfCounterCollector | 2.12.0 | Met Application Insights prestatie meters Collector kunt u gegevens die zijn verzameld door prestatie meter items verzenden naar Application Insights. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Micro soft. ApplicationInsights. Web | 2.12.0 | Application Insights voor .NET-webtoepassingen | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Micro soft. ApplicationInsights. WindowsServer | 2.12.0 | Application Insights Windows Server NuGet-pakket biedt automatische verzameling van Application Insights-telemetrie voor .NET-toepassingen. Dit pakket kan worden gebruikt als een afhankelijk pakket voor Application Insights platformspecifieke pakketten of als een zelfstandig pakket voor .NET-toepassingen die niet worden gedekt door platformspecifieke pakketten (zoals voor .NET-werk rollen). | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Micro soft. ApplicationInsights. WindowsServer. TelemetryChannel | 2.12.0 | Voorziet in een telemetrie-kanaal om een Windows Server-SDK te Application Insights die telemetrie in offline scenario's zal behouden. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Algemene pakketten voor ASP.NET Core

| Pakketnaam | Stabiele versie | Description | Downloaden |
|-------------------------------|-----------------------|------------|----|
| Micro soft. ApplicationInsights. AspNetCore | 2.5.0 | Application Insights voor ASP.NET Core webtoepassingen. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Micro soft. ApplicationInsights | 2.12.0 | Dit pakket biedt kern functionaliteit voor het verzenden van alle Application Insights Telemetry typen en is een afhankelijk pakket voor alle andere Application Insights-pakketten | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Micro soft. ApplicationInsights. DependencyCollector | 2.12.0 | Application Insights afhankelijkheids verzamelaar voor .NET-toepassingen. Dit is een afhankelijk pakket voor Application Insights platformspecifieke pakketten en biedt automatische verzameling van afhankelijkheids-telemetrie. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Micro soft. ApplicationInsights. PerfCounterCollector | 2.12.0 | Met Application Insights prestatie meters Collector kunt u gegevens die zijn verzameld door prestatie meter items verzenden naar Application Insights. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Micro soft. ApplicationInsights. WindowsServer | 2.12.0 | Application Insights Windows Server NuGet-pakket biedt automatische verzameling van Application Insights-telemetrie voor .NET-toepassingen. Dit pakket kan worden gebruikt als een afhankelijk pakket voor Application Insights platformspecifieke pakketten of als een zelfstandig pakket voor .NET-toepassingen die niet worden gedekt door platformspecifieke pakketten (zoals voor .NET-werk rollen). | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Micro soft. ApplicationInsights. WindowsServer. TelemetryChannel | 2.12.0 | Voorziet in een telemetrie-kanaal om een Windows Server-SDK te Application Insights die telemetrie in offline scenario's zal behouden. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>Algemene pakketten voor python met opentellingen
| Pakketnaam | Stabiele versie | Description | Downloaden |
|-------------------------------|-----------------------|------------|----|
| opentelling-ext-Azure | 1.0.0 | Application Insights voor python-toepassingen onder Azure Monitor via opentellingen. | [Pakket downloaden](https://pypi.org/project/opencensus-ext-azure/) |
| opentelling-ext-django | 0.7.2 | Dit pakket biedt integratie met de python [Django](https://pypi.org/project/django/) -bibliotheek. | [Pakket downloaden](https://pypi.org/project/opencensus-ext-django/) |
| opentelling-ext-kolf | 0.7.3 | Dit pakket biedt integratie met de python- [fles](https://pypi.org/project/flask/) bibliotheek. | [Pakket downloaden](https://pypi.org/project/opencensus-ext-flask/) |
| opentelling-ext-httplib | 0.7.2 | Dit pakket biedt integratie met de python [http. client](https://docs.python.org/3/library/http.client.html) bibliotheek voor Python3 en [httplib](https://docs.python.org/2/library/httplib.html) voor Python2. | [Pakket downloaden](https://pypi.org/project/opencensus-ext-httplib/) |
| opentelling-ext-logboek registratie | 0.1.0 | Met dit pakket worden logboek records met tracerings gegevens verrijkt. | [Pakket downloaden](https://pypi.org/project/opencensus-ext-logging/) |
| opentelling-ext-mysql | 0.1.2 | Dit pakket biedt integratie met de python [mysql-connector-](https://pypi.org/project/mysql-connector/) bibliotheek. | [Pakket downloaden](https://pypi.org/project/opencensus-ext-mysql/) |
| opentelling-ext-postgresql | 0.1.2 | Dit pakket biedt integratie met de python [psycopg2](https://pypi.org/project/psycopg2/) -bibliotheek. | [Pakket downloaden](https://pypi.org/project/opencensus-ext-postgresql/) |
| opentelling-ext-pymongo | 0.7.1 | Dit pakket biedt integratie met de python [pymongo](https://pypi.org/project/pymongo/) -bibliotheek. | [Pakket downloaden](https://pypi.org/project/opencensus-ext-pymongo/) |
| opentelling-ext-pymysql | 0.1.2 | Dit pakket biedt integratie met de python [PyMySQL](https://pypi.org/project/PyMySQL/) -bibliotheek. | [Pakket downloaden](https://pypi.org/project/opencensus-ext-pymysql/) |
| opentelling-ext-piramide | 0.7.1 | Dit pakket biedt integratie met de python- [piramide](https://pypi.org/project/pyramid/) bibliotheek. | [Pakket downloaden](https://pypi.org/project/opencensus-ext-pyramid/) |
| opentelling-uitbrei ding van aanvragen | 0.7.2 | Dit pakket biedt integratie met de bibliotheek met python- [aanvragen](https://pypi.org/project/requests/) . | [Pakket downloaden](https://pypi.org/project/opencensus-ext-requests/) |
| opentelling-ext-sqlalchemy | 0.1.2 | Dit pakket biedt integratie met de python [sqlalchemy](https://pypi.org/project/SQLAlchemy/) -bibliotheek. | [Pakket downloaden](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Listeners/Verzamel aars/toevoegers

| Pakketnaam | Stabiele versie | Description | Downloaden |
|-------------------------------|-----------------------|------------|----|
| Micro soft. ApplicationInsights. DiagnosticSourceListener | 2.7.2 |  Hiermee is het door sturen van gebeurtenissen van DiagnosticSource naar Application Insights toegestaan. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Micro soft. ApplicationInsights. EventSourceListener | 2.7.2 | Met Application Insights EventSourceListener kunnen gegevens van Event source-gebeurtenissen worden verzonden naar Application Insights. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Micro soft. ApplicationInsights. EtwCollector | 2.7.2 | Met Application Insights EtwCollector kan het verzenden van gegevens van Event Tracing for Windows (ETW) naar Application Insights worden verzonden. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Micro soft. ApplicationInsights. TraceListener | 2.7.2 | Een aangepaste TraceListener waarmee u traceer logboek berichten kunt verzenden naar Application Insights. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Micro soft. ApplicationInsights. Log4NetAppender | 2.7.2 | Een aangepaste toevoegder waarmee u Log4Net-logboek berichten kunt verzenden naar Application Insights. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Micro soft. ApplicationInsights. NLogTarget | 2.7.2 |  een aangepast doel waarmee u NLog-logboek berichten kunt verzenden naar Application Insights. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Micro soft. ApplicationInsights. SnapshotCollector | 1.3.1 | Hiermee worden uitzonde ringen in uw toepassing gecontroleerd en worden automatisch moment opnamen voor offline analyse verzameld. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Pakketnaam | Stabiele versie | Description | Downloaden |
|-------------------------------|-----------------------|------------|----|
| Micro soft. ApplicationInsights. ServiceFabric | 2.2.0 | Dit pakket biedt automatische decoratie van telemetrie met de service Fabric-context waarin de toepassing wordt uitgevoerd. Gebruik deze NuGet niet voor systeem eigen Service Fabric-toepassingen. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Micro soft. ApplicationInsights. ServiceFabric. native | 2.2.0 | Application Insights module voor service Fabric-toepassingen. Gebruik deze NuGet alleen voor systeem eigen Service Fabric-toepassingen. Voor toepassingen die in containers worden uitgevoerd, gebruikt u het pakket micro soft. ApplicationInsights. ServiceFabric. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Status monitor

| Pakketnaam | Stabiele versie | Description | Downloaden |
|-------------------------------|-----------------------|------------|----|
| Micro soft. ApplicationInsights. Agent_x64 | 2.2.1 |  Hiermee wordt het verzamelen van runtime gegevens voor x64-toepassingen ingeschakeld | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Micro soft. ApplicationInsights. Agent_x86 | 2.2.1 |  Hiermee wordt het verzamelen van runtime gegevens voor x86-toepassingen ingeschakeld. | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Deze pakketten vormen een deel van de kern functionaliteit van de runtime bewaking in [status monitor](../../azure-monitor/app/monitor-performance-live-website-now.md). U hoeft deze pakketten niet rechtstreeks te downloaden. gebruik gewoon het Status Monitor-installatie programma. Als u meer wilt weten over hoe deze pakketten werken onder de motorkap, kan dit [blog bericht](https://apmtips.com/posts/2016-11-18-how-application-insights-status-monitor-not-monitors-dependencies/) van een van onze ontwikkel aars goed worden gestart.

## <a name="additional-packages"></a>Aanvullende pakketten

| Pakketnaam | Stabiele versie | Description | Downloaden |
|-------------------------------|-----------------------|------------|----|
| Micro soft. ApplicationInsights. AzureWebSites | beschreven | Met deze extensie wordt de Application Insights bewaking op een Azure App Service ingeschakeld. SDK-versie 2.6.1. Instructies: Voeg de toepassings instellingen APPINSIGHTS_INSTRUMENTATIONKEY toe aan uw iKey en start de webapp opnieuw om een effect te krijgen.| [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Micro soft. ApplicationInsights. Injecter | 2.6.7 | Dit pakket bevat bestanden die vereist zijn voor codeloze Application Insights injectie | [Pakket downloaden](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Volgende stappen

- [ASP.net core](../../azure-monitor/app/asp-net-core.md)bewaken.
- Profiel ASP.NET Core [Azure Linux-web-apps](../../azure-monitor/app/profiler-aspnetcore-linux.md).
- Debug ASP.NET- [moment opnamen](../../azure-monitor/app/snapshot-debugger.md).
