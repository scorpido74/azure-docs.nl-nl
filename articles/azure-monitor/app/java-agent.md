---
title: Prestatiebewaking van Java-webapps - Azure Application Insights
description: Uitgebreide prestaties en gebruikscontrole van uw Java-website met Application Insights.
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: b29618179d22eac97a07bf41906465aba1fd7929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657024"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Afhankelijkheden bewaken, uitzonderingen en uitvoeringstijden voor methoden in Java-webapps controleren


Als u [uw Java-webapp hebt uitgerust met Application Insights,][java]u de Java-agent gebruiken om diepere inzichten te verkrijgen, zonder wijzigingen in de code:

* **Afhankelijkheden:** Gegevens over oproepen die uw toepassing naar andere onderdelen voert, waaronder:
  * **Uitgaande HTTP-oproepen** via Apache HttpClient, `java.net.HttpURLConnection` OkHttp en worden vastgelegd.
  * **Redis telefoontjes** via de Jedis cliënt worden vastgelegd.
  * **JDBC-query's** - Als het gesprek langer duurt dan 10 seconden voor MySQL en PostgreSQL, rapporteert de agent het queryplan.

* **Toepassing logboekregistratie:** Uw toepassingslogboeken vastleggen en correleren met HTTP-aanvragen en andere telemetrie
  * **Log4j 1.2**
  * **Log4j2**
  * **Aanmelding**

* **Betere bedrijfsnaamgeving:** (gebruikt voor aggregatie van aanvragen in de portal)
  * **Lente** - `@RequestMapping`gebaseerd op .
  * **JAX-RS** - `@Path`gebaseerd op . 

Als u de Java-agent wilt gebruiken, installeert u deze op uw server. Uw web-apps moeten worden uitgerust met de [Application Insights Java SDK.][java] 

## <a name="install-the-application-insights-agent-for-java"></a>De Application Insights-agent voor Java installeren
1. [Download de agent](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) op de machine die uw Java-server uitvoert. Zorg ervoor dat u dezelfde versie van de Java-agent downloadt als de Java-SDK-kern en -webpakketten van Application Insights.
2. Bewerk het opstartscript van de toepassingsserver en voeg het volgende argument VAN JVM toe:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Bijvoorbeeld in Tomcat op een Linux-machine:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Start de toepassingsserver opnieuw.

## <a name="configure-the-agent"></a>De agent configureren
Maak een `AI-Agent.xml` bestand met de naam en plaats het in dezelfde map als het jar-bestand van de agent.

Stel de inhoud van het xml-bestand in. Bewerk het volgende voorbeeld om de gewenste functies op te nemen of weg te laten.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>Extra config (Spring Boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Ga als volgt te werk voor Azure App Services:

* Selecteer Instellingen > Toepassingsinstellingen
* Voeg een nieuw sleutelwaardepaar toe bij App-instellingen:

Sleutel: `JAVA_OPTS` Waarde:`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

Voor de nieuwste versie van de Java-agent, check de releases [hier](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

De agent moet worden verpakt als een bron in uw project, zodat het eindigt in de D:/home/site/wwwroot / directory. U bevestigen dat uw agent zich in de juiste appservicemap bevindt door naar **Advanced Tools** > **Advanced Tools** > **Debug Console** te gaan en de inhoud van de sitemap te onderzoeken.    

* Sla de instellingen op met Opslaan en start de app opnieuw met Opnieuw opstarten. (Deze stappen zijn alleen van toepassing op App Services die op Windows worden uitgevoerd.)

> [!NOTE]
> AI-Agent.xml en het agentjarbestand moeten zich in dezelfde map bevinden. Ze worden vaak samen `/resources` in de map van het project geplaatst.  

#### <a name="enable-w3c-distributed-tracing"></a>W3C distributed tracing inschakelen

Voeg het volgende toe aan AI-Agent.xml:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> De achterwaartse compatibiliteitsmodus is standaard ingeschakeld en de parameter enableW3CBackCompat is optioneel en mag alleen worden gebruikt wanneer u deze wilt uitschakelen. 

Idealiter zou dit het geval zijn wanneer al uw diensten zijn bijgewerkt naar nieuwere versie van SDKs ondersteunen W3C protocol. Het wordt ten zeerste aanbevolen om zo snel mogelijk over te stappen op een nieuwere versie van SDKs met W3C-ondersteuning.

Zorg ervoor dat **zowel [inkomende](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) als uitgaande (agent)configuraties** precies hetzelfde zijn.

## <a name="view-the-data"></a>De gegevens weergeven
In de resource Application Insights worden geaggregeerde afhankelijkheids- en methodeuitvoeringstijden op afstand weergegeven [onder de tegel Prestaties][metrics].

Als u wilt zoeken naar afzonderlijke instanties van afhankelijkheids-, uitzonderings- en methoderapporten, opent u [Zoeken][diagnostic].

[Het diagnosticeren van afhankelijkheidsproblemen - meer informatie](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Vragen? Problemen?
* Zijn er geen gegevens? [Uitzonderingen op firewall instellen](../../azure-monitor/app/ip-addresses.md)
* [Problemen met Java oplossen](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
