---
title: Java-traceerlogboeken verkennen in Azure Application Insights
description: Zoek naar Log4J- of Logback-sporen in Application Insights
ms.topic: conceptual
ms.date: 05/18/2019
ms.openlocfilehash: da1b76d52ab93f4d1be7196d6eb7286579481119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657211"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Java trace logs verkennen in Application Insights
Als u Logback of Log4J (v1.2 of v2.0) gebruikt voor tracering, u uw traceringslogboeken automatisch laten verzenden naar Application Insights, waar u ze verkennen en zoeken.

> [!TIP]
> U hoeft de instrumentatiesleutel voor application Insights slechts één keer in te stellen voor uw toepassing. Als u een framework als Java Spring gebruikt, hebt u de sleutel mogelijk al elders in de configuratie van uw app geregistreerd.

## <a name="using-the-application-insights-java-agent"></a>De Application Insights Java-agent gebruiken

Standaard legt de Java-agent Application Insights `WARN` automatisch logboekregistratievast die op niveau en hoger wordt uitgevoerd.

U de drempelwaarde voor logboekregistratie wijzigen die met het `AI-Agent.xml` bestand is vastgelegd:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging threshold="info"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

U de logboekopname van de `AI-Agent.xml` Java-agent uitschakelen met behulp van het bestand:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging enabled="false"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="alternatively-as-opposed-to-using-the-java-agent-you-can-follow-the-instructions-below"></a>Als alternatief (in tegenstelling tot het gebruik van de Java-agent), u de onderstaande instructies volgen

### <a name="install-the-java-sdk"></a>De Java SDK installeren

Volg de instructies om [Application Insights SDK voor Java][java]te installeren, als je dat nog niet hebt gedaan.

### <a name="add-logging-libraries-to-your-project"></a>Logboekbibliotheken toevoegen aan uw project
*Kies de juiste methode voor uw project.*

#### <a name="if-youre-using-maven"></a>Als u Maven gebruikt...
Als uw project al is ingesteld om Maven te gebruiken voor build, voegt u een van de volgende codefragmenten samen in uw pom.xml-bestand.

Vernieuw vervolgens de projectafhankelijkheden om de binaire bestanden te downloaden.

*Aanmelding*

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
Als uw project al is ingesteld om Gradle te gebruiken voor `dependencies` build, voegt u een van de volgende regels toe aan de groep in uw bestand build.gradle:

Vernieuw vervolgens de projectafhankelijkheden om de binaire bestanden te downloaden.

**Aanmelding**

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
Volg de richtlijnen om Application Insights Java SDK handmatig te installeren, download de pot (Na aankomst op Maven Central Page klik op 'jar' link in download sectie) voor de juiste appender en voeg de gedownloade appender pot aan het project.

| Logger | Download | Bibliotheek |
| --- | --- | --- |
| Aanmelding |[Logback appender Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Log4J v2 appender Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4j v1.2 |[Log4J v1.2 appender Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


### <a name="add-the-appender-to-your-logging-framework"></a>De appender toevoegen aan uw logboekregistratiekader
Als u sporen wilt krijgen, voegt u het relevante codefragment samen met het configuratiebestand Log4J of Logboekterug: 

*Aanmelding*

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

De Application Insights appenders kan worden verwezen door een geconfigureerde logger, en niet noodzakelijkerwijs door de root logger (zoals weergegeven in de code monsters hierboven).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Ontdek uw sporen in de Application Insights portal
Nu u uw project hebt geconfigureerd om traces naar Application Insights te verzenden, u deze traceringen bekijken en doorzoeken in de Portal Application Insights in het [zoekblad.][diagnostic]

Uitzonderingen ingediend via loggers zal worden weergegeven op de portal als Uitzondering Telemetrie.

![Open Zoeken in de portal Application Insights](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>Volgende stappen
[Diagnostische zoekopdracht][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


