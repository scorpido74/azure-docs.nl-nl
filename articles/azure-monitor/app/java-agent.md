---
title: Prestatie bewaking voor Java-Web-apps in Azure-toepassing Insights | Microsoft Docs
description: Uitgebreide prestaties en gebruiks bewaking van uw Java-website met Application Insights.
services: application-insights
documentationcenter: java
author: mrbullwinkle
manager: carmonm
ms.assetid: 84017a48-1cb3-40c8-aab1-ff68d65e2128
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: mbullwin
ms.openlocfilehash: ff9d4bb98a79c379fda2c1a0a0ab9d5e0ec212ce
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338096"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Afhankelijkheden, onderschepte uitzonde ringen en methode-uitvoerings tijden in Java-Web-apps bewaken


Als u [uw Java-Web-app hebt voorzien van Application Insights][java], kunt u de Java-Agent gebruiken om diepere inzichten te verkrijgen, zonder dat u code hoeft te wijzigen:

* **Elkaar** Gegevens over de aanroepen die uw toepassing maakt met andere onderdelen, waaronder:
  * **Uitgaande HTTP-aanroepen** via Apache httpclient maakt, OkHttp en `java.net.HttpURLConnection` worden vastgelegd.
  * **Redis** -aanroepen via de jedis-client worden vastgelegd.
  * **JDBC-query's** : voor MySQL en PostgreSQL, als de aanroep langer dan 10 seconden duurt, rapporteert de agent het query plan.

* **Toepassings logboeken:** Uw toepassings logboeken vastleggen en correleren met HTTP-aanvragen en andere telemetrie
  * **Log4j 1,2**
  * **Log4j2**
  * **Logback**

* **Betere bewerkings naam:** (wordt gebruikt voor het samen voegen van aanvragen in de portal)
  * **Lente** op basis van `@RequestMapping`.
  * **Jax-RS** -gebaseerd op `@Path`. 

Als u de Java-Agent wilt gebruiken, installeert u deze op uw server. Uw web-apps moeten worden beinstrumented met de [Application Insights Java SDK][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>De Application Insights-agent voor Java installeren
1. [Download de agent](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest) op de machine die uw Java-server uitvoert. Zorg ervoor dat u dezelfde versie van de Java-agent downloadt als de Java-SDK-kern en -webpakketten van Application Insights.
2. Bewerk het opstart script van de toepassings server en voeg het volgende JVM-argument toe:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Bijvoorbeeld in Tomcat op een Linux-computer:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Start de toepassings server opnieuw op.

## <a name="configure-the-agent"></a>De agent configureren
Maak een bestand met `AI-Agent.xml` de naam en plaats het in dezelfde map als het jar-bestand van de agent.

Stel de inhoud van het XML-bestand in. Bewerk het volgende voor beeld om de gewenste functies toe te voegen of te weglaten.

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

## <a name="additional-config-spring-boot"></a>Aanvullende configuratie (Spring boot)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Ga als volgt te werk voor Azure-app Services:

* Selecteer Instellingen > Toepassingsinstellingen
* Voeg een nieuw sleutelwaardepaar toe bij App-instellingen:

Prestatie `JAVA_OPTS`Value`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

Voor de meest recente versie van de Java-agent controleert u [hier](https://github.com/Microsoft/ApplicationInsights-Java/releases
)de releases. 

De agent moet worden verpakt als een resource in uw project, zodat deze wordt beëindigd op de D:/Home/site/wwwroot/map. U kunt controleren of uw agent zich in de juiste app service Directory bevindt door te gaan naar **ontwikkel hulpprogramma's** > **Geavanceerde hulpprogram ma's** > **console voor fout opsporing** en de inhoud van de sitemap te controleren.    

* Sla de instellingen op en start de app opnieuw. (Deze stappen zijn alleen van toepassing op App Services die worden uitgevoerd op Windows.)

> [!NOTE]
> AI-Agent. XML en het jar-bestand van de agent moeten zich in dezelfde map bevindt. Ze worden vaak samen in de `/resources` map van het project geplaatst.  

#### <a name="enable-w3c-distributed-tracing"></a>In W3C gedistribueerde tracering inschakelen

Voeg het volgende toe aan AI-Agent. XML:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> Achterwaartse compatibiliteits modus is standaard ingeschakeld en de para meter enableW3CBackCompat is optioneel en moet alleen worden gebruikt als u deze wilt uitschakelen. 

In het ideale geval is dit de situatie waarin al uw services zijn bijgewerkt naar een nieuwere versie van Sdk's die W3C-protocol ondersteunen. Het wordt sterk aanbevolen om zo snel mogelijk over te stappen op een nieuwere versie van Sdk's met W3C-ondersteuning.

Zorg ervoor dat de configuraties voor inkomend en uitgaand verkeer **(agent) [](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps)**  precies hetzelfde zijn.

## <a name="view-the-data"></a>De gegevens weer geven
In de Application Insights resource worden de geaggregeerde externe afhankelijkheden en de uitvoerings tijden van de methode weer gegeven [onder de tegel prestaties][metrics].

Als u wilt zoeken naar afzonderlijke exemplaren van de rapporten afhankelijkheid, uitzonde ring en methode, opent u [zoeken][diagnostic].

[Afhankelijkheids problemen vaststellen-meer informatie](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Vragen? Problemen?
* Zijn er geen gegevens? [Firewall-uitzonde ringen instellen](../../azure-monitor/app/ip-addresses.md)
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
