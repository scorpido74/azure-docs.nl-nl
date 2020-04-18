---
title: Java-toepassingen in elke omgeving bewaken - Azure Monitor Application Insights
description: Toepassingsprestatiebewaking voor Java-toepassingen die in elke omgeving worden uitgevoerd zonder de app in te dienen. Gedistribueerde traceer- en toepassingskaart.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: 5706d5a74bd6850a237f7418b1a86a8e9c7762e1
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604583"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Java-codeloze toepassingsbewaking Azure Monitor Application Insights - openbare preview

Java codeless applicatie monitoring is alles over eenvoud - er zijn geen code wijzigingen, de Java-agent kan worden ingeschakeld door middel van slechts een paar wijzigingen in de configuratie.

 De Java-agent werkt in elke omgeving en stelt u in staat om al uw Java-toepassingen te controleren. Met andere woorden, of u nu uw Java-apps op VM's, on-premises, in AKS, op Windows, Linux - noem maar op, de Java 3.0-agent zal uw app controleren.

Het toevoegen van de Application Insights Java SDK aan uw toepassing is niet langer nodig, omdat de 3.0-agent automatisch verzoeken, afhankelijkheden en logboeken op zichzelf verzamelt.

U nog steeds aangepaste telemetrie verzenden vanuit uw toepassing. De 3.0 agent zal volgen en correleren samen met alle van de automatisch verzamelde telemetrie.

## <a name="quickstart"></a>Snelstartgids

**1. Download de agent**

Download [applicationinsights-agent-3.0.0-PREVIEW.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.3/applicationinsights-agent-3.0.0-PREVIEW.3.jar)

**2. Richt de JVM aan de agent**

Toevoegen `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.3.jar` aan de JVM-args van uw toepassing

Typische JVM args zijn `-Xmx512m` en `-XX:+UseG1GC`. Dus als je weet waar deze toe te voegen, dan weet je al waar dit toe te voegen.

Voor extra hulp bij het configureren van de JVM-args van uw toepassing raadpleegt u [3.0 Preview: Tips voor het bijwerken van uw JVM-args.](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Tips-for-updating-your-JVM-args)

**3. Wijs de agent naar uw Application Insights-bron**

Als u nog geen Application Insights-bron hebt, u een nieuwe resource maken door de stappen in de [handleiding voor het maken van resources te](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)volgen.

Wijs de agent aan uw Application Insights-bron door een omgevingsvariabele in te stellen:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

Of door een configuratiebestand met de naam `ApplicationInsights.json`, `applicationinsights-agent-3.0.0-PREVIEW.3.jar`en plaats het in dezelfde directory als , met de volgende inhoud:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

U uw verbindingstekenreeks vinden in uw bron Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Verbindingstekenreeks voor toepassingsinzichten":::

**4. Dat is het!**

Start nu uw toepassing op en ga naar uw Application Insights-bron in de Azure-portal om uw bewakingsgegevens te bekijken.

> [!NOTE]
> Het kan een paar minuten duren voordat uw bewakingsgegevens in het portaal worden weergegeven.


## <a name="configuration-options"></a>Configuratie-opties

In `ApplicationInsights.json` het bestand u bovendien configureren:

* Naam van cloudrol
* Cloudrolinstantie
* Het vastleggen van toepassingslogboeken
* JMX-statistieken
* Micrometer
* Hartslag
* Steekproeven
* HTTP-proxy
* Zelfdiagnostiek

Zie details op [3.0 Public Preview: Configuratieopties](https://github.com/microsoft/ApplicationInsights-Java/wiki/3.0-Preview:-Configuration-Options).

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Automatisch verzamelde aanvragen, afhankelijkheden, logboeken en statistieken

### <a name="requests"></a>Aanvragen

* JMS Consumenten
* Kafka Consumenten
* Netty/WebFlux
* Servlets
* Voorjaarsplanning

### <a name="dependencies-with-distributed-trace-propagation"></a>Afhankelijkheden met gedistribueerde spoorpropagatie

* Apache HttpClient en HttpAsyncClient
* gRPC
* java.net.HttpURLConnection
* Jms
* Kafka
* Netty-client
* Okhttp

### <a name="other-dependencies"></a>Andere afhankelijkheden

* Cassandra
* JDBC
* MongoDB (async en synchronisatie)
* Redis (Sla en Jedis)

### <a name="logs"></a>Logboeken

* java.util.logging
* Log4j
* SLF4J/Aanmelding

### <a name="metrics"></a>Metrische gegevens

* Micrometer
* JMX-statistieken

## <a name="sending-custom-telemetry-from-your-application"></a>Aangepaste telemetrie verzenden vanuit uw toepassing

Ons doel in 3.0+ is om u in staat te stellen uw aangepaste telemetrie te verzenden met behulp van standaard API's.

Wij ondersteunen Micrometer, OpenTelemetry API en de populaire logging frameworks. Application Insights Java 3.0 zal automatisch vastleggen van de telemetrie, en correleren samen met alle van de automatisch verzamelde telemetrie.

Daarom zijn we op dit moment niet van plan om een SDK met Application Insights 3.0 uit te brengen.

Application Insights Java 3.0 luistert al naar telemetrie die naar de Application Insights Java SDK 2.x wordt verzonden. Deze functionaliteit is een belangrijk onderdeel van het upgradeverhaal voor bestaande 2.x-gebruikers en vult een belangrijk gat in onze aangepaste telemetrieondersteuning totdat de OpenTelemetry API GA is.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Aangepaste telemetrie verzenden met Application Insights Java SDK 2.x

Toevoegen `applicationinsights-core-2.6.0.jar` aan uw toepassing (alle 2.x-versies worden ondersteund door Application Insights Java 3.0, maar het is de moeite waard om de nieuwste als je een keuze hebt):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Een telemetrieclient maken:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

en gebruik dat voor het verzenden van aangepaste telemetrie.

### <a name="events"></a>Gebeurtenissen

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Metrische gegevens

U metrische telemetrie verzenden via [Micrometer:](https://micrometer.io)

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

Of u ook Application Insights Java SDK 2.x gebruiken:

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>Afhankelijkheden

```java
  boolean success = false;
  long startTime = System.currentTimeMillis();
  try {
      success = dependency.call();
  } finally {
      long endTime = System.currentTimeMillis();
      RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
      telemetry.setTimestamp(new Date(startTime));
      telemetry.setDuration(new Duration(endTime - startTime));
      telemetryClient.trackDependency(telemetry);
  }
```

### <a name="logs"></a>Logboeken
U aangepaste log telemetrie via uw favoriete logging framework.

Of u ook Application Insights Java SDK 2.x gebruiken:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Uitzonderingen
U aangepaste uitzondering telemetrie via uw favoriete logging framework.

Of u ook Application Insights Java SDK 2.x gebruiken:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Upgraden van Application Insights Java SDK 2.x

Als u Application Insights Java SDK 2.x al in uw toepassing gebruikt, hoeft u deze niet te verwijderen. De Java 3.0-agent detecteert het en legt elke aangepaste telemetrie vast die u via de Java SDK 2.x verzendt, terwijl u elke autocollectie onderdrukt die door de Java SDK 2.x wordt uitgevoerd om dubbele opname te voorkomen.

> [!NOTE]
> Opmerking: Java SDK 2.x TelemetrieInitializers en Telemetrieprocessors worden niet uitgevoerd wanneer u de 3.0-agent gebruikt.
