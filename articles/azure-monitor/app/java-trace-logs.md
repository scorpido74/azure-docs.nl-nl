---
title: Java-traceer logboeken verkennen in Azure-toepassing Insights | Microsoft Docs
description: Log4J-of logback-traceringen zoeken in Application Insights
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: fc0a9e2f-3beb-4f47-a9fe-3f86cd29d97a
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/18/2019
ms.author: mbullwin
ms.openlocfilehash: ac9bd6021b5fcec36e3aadfdf4c30020971f3be5
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299252"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Java-traceer logboeken verkennen in Application Insights
Als u logback of Log4J (v 1.2 of v 2.0) gebruikt voor tracering, kunt u uw traceer logboeken automatisch laten verzenden naar Application Insights waar u ze kunt verkennen en doorzoeken.

## <a name="using-the-application-insights-java-agent"></a>De Application Insights Java-Agent gebruiken

U kunt de Application Insights Java-agent zo configureren dat uw logboeken automatisch worden vastgelegd door de functie `AI-Agent.xml` in het bestand in te scha kelen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">
         <Logging enabled="true" />
      </BuiltIn>
   </Instrumentation>
   <AgentLogger />
</ApplicationInsightsAgent>
```

U kunt ook de onderstaande instructies volgen.

## <a name="install-the-java-sdk"></a>De Java-SDK installeren

Volg de instructies voor het installeren [van Application INSIGHTS SDK voor Java][java], als u dat nog niet hebt gedaan.

## <a name="add-logging-libraries-to-your-project"></a>Logboek registratie bibliotheken toevoegen aan uw project
*Kies de juiste methode voor uw project.*

#### <a name="if-youre-using-maven"></a>Als u Maven gebruikt...
Als uw project al is ingesteld voor het gebruik van Maven voor Build, moet u een van de volgende code fragmenten in het bestand pom. XML samen voegen.

Vernieuw vervolgens de Project afhankelijkheden om de binaire bestanden te downloaden.

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Als u Gradle gebruikt...
Als uw project al is ingesteld voor het gebruik van Gradle voor Build, voegt u een van de volgende regels `dependencies` toe aan de groep in uw build. Gradle-bestand:

Vernieuw vervolgens de Project afhankelijkheden om de binaire bestanden te downloaden.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>Of...
Volg de richt lijnen om Application Insights Java SDK hand matig te installeren, down load het jar (nadat u op de Maven-centrale pagina hebt geklikt op de koppeling ' jar ' in de sectie downloaden) voor de juiste appender en voeg de gedownloade toevoegder jar toe aan het project.

| Logger | Downloaden | Bibliotheek |
| --- | --- | --- |
| Logback |[Logback-toevoeger jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v 2.0 |[Log4J v2-toevoeger jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4j v1.2 |[Log4J v 1.2-toevoeger jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>Voeg de toevoegder toe aan uw Framework voor logboek registratie
Als u wilt beginnen met het ophalen van traceringen, voegt u het relevante code fragment uit in het configuratie bestand Log4J of logback: 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

De Application Insights-toevoegers kunnen worden verwezen door een geconfigureerde logger en niet noodzakelijkerwijs door de hoofd logboek registratie (zoals weer gegeven in de bovenstaande code voorbeelden).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Verken uw traceringen in de Application Insights Portal
Nu u het project zodanig hebt geconfigureerd dat traceringen naar Application Insights worden verzonden, kunt u deze traceringen bekijken en zoeken in de Application Insights Portal, op de Blade [zoeken][diagnostic] .

Uitzonde ringen die via Logboeken worden verzonden, worden in de portal weer gegeven als een telemetrie-uitzonde ring.

![Open in de Application Insights Portal zoek opdracht](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Volgende stappen
[Diagnostische gegevens zoeken][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


