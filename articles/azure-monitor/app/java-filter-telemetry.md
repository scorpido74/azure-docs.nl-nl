---
title: Azure Application Insights-telemetrie filteren in uw Java-webapp
description: Verminder telemetrieverkeer door de gebeurtenissen die u niet hoeft te controleren, uit te filteren.
ms.topic: conceptual
ms.date: 3/14/2019
ms.openlocfilehash: 020e54132e0ca0a9f9ccf0236f94515877015637
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659914"
---
# <a name="filter-telemetry-in-your-java-web-app"></a>Telemetrie filteren in uw Java-web-app

Filters bieden een manier om de telemetrie te selecteren die uw [Java-web-app naar Application Insights verzendt.](java-get-started.md) Er zijn een aantal kant-en-klare filters die u gebruiken, en u ook uw eigen aangepaste filters schrijven.

De out-of-the-box filters omvatten:

* Ernstniveau traceren
* Specifieke URL's, trefwoorden of antwoordcodes
* Snelle antwoorden - dat wil zeggen, verzoeken waarop uw app snel heeft gereageerd
* Specifieke gebeurtenisnamen

> [!NOTE]
> Filters scheeftrekken de statistieken van uw app. U bijvoorbeeld besluiten dat u, om trage reacties te diagnosticeren, een filter instelt om snelle responstijden te verwijderen. Maar u moet zich ervan bewust zijn dat de gemiddelde responstijden die door Application Insights worden gerapporteerd dan langzamer zijn dan de werkelijke snelheid en dat het aantal aanvragen kleiner is dan de werkelijke telling.
> Als dit een probleem is, gebruikt u in plaats daarvan [Sampling.](../../azure-monitor/app/sampling.md)

## <a name="setting-filters"></a>Filters instellen

Voeg in ApplicationInsights.xml `TelemetryProcessors` een sectie als dit voorbeeld toe:


```XML

    <ApplicationInsights>
      <TelemetryProcessors>

        <BuiltInProcessors>
           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="100"/>
                  <Add name="NotNeededResponseCodes" value="200-400"/>
           </Processor>

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="100"/>
                  <Add name="NotNeededNames" value="home,index"/>
                  <Add name="NotNeededUrls" value=".jpg,.css"/>
           </Processor>

           <Processor type="TelemetryEventFilter">
                  <!-- Names of events we don't want to see -->
                  <Add name="NotNeededNames" value="Start,Stop,Pause"/>
           </Processor>

           <!-- Exclude telemetry from availability tests and bots -->
           <Processor type="SyntheticSourceFilter">
                <!-- Optional: specify which synthetic sources,
                     comma-separated
                     - default is all synthetics -->
                <Add name="NotNeededSources" value="Application Insights Availability Monitoring,BingPreview"
           </Processor>

        </BuiltInProcessors>

        <CustomProcessors>
          <Processor type="com.fabrikam.MyFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>

      </TelemetryProcessors>
    </ApplicationInsights>

```




[Inspecteer de volledige set ingebouwde processors.](https://github.com/Microsoft/ApplicationInsights-Java/tree/master/core/src/main/java/com/microsoft/applicationinsights/internal/processor)

## <a name="built-in-filters"></a>Ingebouwde filters

### <a name="metric-telemetry-filter"></a>Metriek telemetrie, filter

```XML

           <Processor type="MetricTelemetryFilter">
                  <Add name="NotNeeded" value="metric1,metric2"/>
           </Processor>
```

* `NotNeeded`- Door komma's gescheiden lijst met aangepaste metrische namen.


### <a name="page-view-telemetry-filter"></a>Telemetriefilter Paginaweergave

```XML

           <Processor type="PageViewTelemetryFilter">
                  <Add name="DurationThresholdInMS" value="500"/>
                  <Add name="NotNeededNames" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```

* `DurationThresholdInMS`- Duur verwijst naar de tijd die nodig is om de pagina te laden. Als dit is ingesteld, worden pagina's die sneller geladen zijn dan deze tijd niet gerapporteerd.
* `NotNeededNames`- Door komma's gescheiden lijst met paginanamen.
* `NotNeededUrls`- Door komma's gescheiden lijst met URL-fragmenten. `"home"` Filtert bijvoorbeeld alle pagina's met 'thuis' in de URL.


### <a name="request-telemetry-filter"></a>Telemetriefilter aanvragen


```XML

           <Processor type="RequestTelemetryFilter">
                  <Add name="MinimumDurationInMS" value="500"/>
                  <Add name="NotNeededResponseCodes" value="page1,page2"/>
                  <Add name="NotNeededUrls" value="url1,url2"/>
           </Processor>
```



### <a name="synthetic-source-filter"></a>Synthetisch bronfilter

Hiermee filtert u alle telemetrie met waarden in de eigenschap SyntheticSource. Deze omvatten verzoeken van bots, spinnen en beschikbaarheid tests.

Filter telemetrie voor alle synthetische aanvragen:


```XML

           <Processor type="SyntheticSourceFilter" />
```

Telemetrie filteren voor specifieke synthetische bronnen:


```XML

           <Processor type="SyntheticSourceFilter" >
                  <Add name="NotNeeded" value="source1,source2"/>
           </Processor>
```

* `NotNeeded`- Door komma's gescheiden lijst van synthetische bronnamen.

### <a name="telemetry-event-filter"></a>Telemetriegebeurtenis, filter

Hiermee worden aangepaste gebeurtenissen (aangemeld met [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent)gelogd.


```XML

           <Processor type="TelemetryEventFilter" >
                  <Add name="NotNeededNames" value="event1, event2"/>
           </Processor>
```


* `NotNeededNames`- Door komma's gescheiden lijst met gebeurtenisnamen.


### <a name="trace-telemetry-filter"></a>Telemetrie-filter traceren

Filters logtraces (ingelogd met [TrackTrace()](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) of een [logging framework collector).](java-trace-logs.md)

```XML

           <Processor type="TraceTelemetryFilter">
                  <Add name="FromSeverityLevel" value="ERROR"/>
           </Processor>
```

* `FromSeverityLevel`geldige waarden zijn:
  *  UIT - Filter alle sporen
  *  TRACE - Geen filtering. gelijk aan Trace-niveau
  *  INFO - Trace-niveau filteren
  *  WAARSCHUWING - Trace en INFO filteren
  *  FOUT - Filter WAARSCHUWEN, INFO, TRACE
  *  KRITISCH - filter alles behalve KRITISCH


## <a name="custom-filters"></a>Aangepaste filters

### <a name="1-code-your-filter"></a>1. Codeer uw filter

Maak in uw code een `TelemetryProcessor`klasse die implementeert:

```Java

    package com.fabrikam.MyFilter;
    import com.microsoft.applicationinsights.extensibility.TelemetryProcessor;
    import com.microsoft.applicationinsights.telemetry.Telemetry;

    public class SuccessFilter implements TelemetryProcessor {

       /* Any parameters that are required to support the filter.*/
       private final String successful;

       /* Initializers for the parameters, named "setParameterName" */
       public void setNotNeeded(String successful)
       {
          this.successful = successful;
       }

       /* This method is called for each item of telemetry to be sent.
          Return false to discard it.
          Return true to allow other processors to inspect it. */
       @Override
       public boolean process(Telemetry telemetry) {
        if (telemetry == null) { return true; }
        if (telemetry instanceof RequestTelemetry)
        {
            RequestTelemetry requestTelemetry = (RequestTelemetry)telemetry;
            return request.getSuccess() == successful;
        }
        return true;
       }
    }

```


### <a name="2-invoke-your-filter-in-the-configuration-file"></a>2. Uw filter aanroepen in het configuratiebestand

In ApplicationInsights.xml:

```XML


    <ApplicationInsights>
      <TelemetryProcessors>
        <CustomProcessors>
          <Processor type="com.fabrikam.SuccessFilter">
            <Add name="Successful" value="false"/>
          </Processor>
        </CustomProcessors>
      </TelemetryProcessors>
    </ApplicationInsights>

```

### <a name="3-invoke-your-filter-java-spring"></a>3. Beroep doen op uw filter (Java Spring)

Voor toepassingen op basis van het Spring framework moeten aangepaste telemetrieprocessors worden geregistreerd in uw hoofdtoepassingsklasse als boon. Ze zullen dan worden autowired wanneer de toepassing begint.

```Java
@Bean
public TelemetryProcessor successFilter() {
      return new SuccessFilter();
}
```

U moet uw eigen filterparameters `application.properties` maken in en gebruik maken van het externe configuratiekader van Spring Boot om deze parameters door te geven aan uw aangepaste filter. 


## <a name="troubleshooting"></a>Problemen oplossen

*Mijn filter werkt niet.*

* Controleer of u geldige parameterwaarden hebt opgegeven. Duur moet bijvoorbeeld gehele getallen zijn. Ongeldige waarden zorgen ervoor dat het filter wordt genegeerd. Als uw aangepaste filter een uitzondering van een constructor of ingestelde methode gooit, wordt deze genegeerd.

## <a name="next-steps"></a>Volgende stappen

* [Sampling](../../azure-monitor/app/sampling.md) - Beschouw sampling als een alternatief dat uw statistieken niet scheeftrekt.
