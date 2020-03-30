---
title: Azure Application Insights - Afhankelijkheidautoverzameling | Microsoft Documenten
description: Application Insights verzamelt en visualiseert automatisch afhankelijkheden
ms.topic: reference
author: nikmd23
ms.author: nimolnar
ms.date: 04/29/2019
ms.reviewer: mbullwin
ms.openlocfilehash: eaafe19f5112b433d50a34aa551aa84d196726a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665813"
---
# <a name="dependency-auto-collection"></a>Afhankelijkheden automatisch verzamelen

Hieronder vindt u de momenteel ondersteunde lijst met afhankelijkheidsoproepen die automatisch worden gedetecteerd als afhankelijkheden zonder dat de code van uw toepassing hoeft te worden gewijzigd. Deze afhankelijkheden worden gevisualiseerd in de [toepassingskaart voor toepassingsinzichten](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) van toepassing en [transactiediagnoseweergaven.](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) Als uw afhankelijkheid niet op de onderstaande lijst staat, u deze nog steeds handmatig bijhouden met een [oproep voor spoorafhankelijkheid.](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency)

## <a name="net"></a>.NET

| App-frameworks| Versies |
| ------------------------|----------|
| Webformulieren ASP.NET | 4,5+ |
| ASP.NET MVC | 4+ |
| ASP.NET WebAPI | 4,5+ |
| ASP.NET Core | 1,1+ |
| <b>Communicatiebibliotheken</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4,5+, .NET Core 1.1+ |
| [SqlClient SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0+, NuGet 4.3.0 |
| [EventHubs-client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [ServiceBus-client SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Opslagclients</b>|  |
| ADO.NET | 4,5+ |

## <a name="java"></a>Java
| App-servers | Versies |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>App-frameworks</b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1,5,9+<sup>*</sup> |
| Java Servlet | 3.1+ |
| <b>Communicatiebibliotheken</b> |  |
| [Apache Http-client](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4,3+<sup>†</sup> |
| <b>Opslagclients</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1+<sup>†</sup> |
| [PostgreSQL (Bètaondersteuning)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [Mysql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>Logboeken bibliotheken</b> | |
| [Aanmelding](https://logback.qos.ch/) | 1+ |
| [Log4j](https://logging.apache.org/log4j/) | 1,2+ |
| <b>Statistiekenbibliotheken</b> |  |
| Jmx | 1,0+ |

> [!NOTE]
> *Behalve reactieve ondersteuning voor het programmeren.
> <br>†Voor de installatie van [JVM-agent](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java)is het een vereist .

## <a name="nodejs"></a>Node.js

| Communicatiebibliotheken | Versies |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0,10+ |
| <b>Opslagclients</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb Kern](https://www.npmjs.com/package/mongodb-core) | 2.x - 3.x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 - 2.16.x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6.x - 7.x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1.x - 2.x |
| <b>Logboeken bibliotheken</b> | |
| [Console](https://nodejs.org/api/console.html) | 0,10+ |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x - 3.x |

## <a name="javascript"></a>JavaScript

| Communicatiebibliotheken | Versies |
| ------------------------|----------|
| [XMLhttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Alle |

## <a name="next-steps"></a>Volgende stappen

- Aangepaste afhankelijkheidstracking instellen voor [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Aangepaste afhankelijkheidstracking instellen voor [Java.](../../azure-monitor/app/java-agent.md)
- Aangepaste afhankelijkheidstracking instellen voor [OpenCensus Python](../../azure-monitor/app/opencensus-python-dependency.md).
- [Aangepaste afhankelijkheidstelemetrie schrijven](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Zie [gegevensmodel](../../azure-monitor/app/data-model.md) voor toepassingsinzichten en gegevensmodel.
- Bekijk [platforms](../../azure-monitor/app/platforms.md) die worden ondersteund door Application Insights.
