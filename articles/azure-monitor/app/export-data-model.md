---
title: Azure-toepassing Insights-gegevens model | Microsoft Docs
description: Beschrijft de eigenschappen die zijn geëxporteerd uit continue export in JSON en gebruikt als filters.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 01/08/2019
ms.openlocfilehash: 8f84e3179a6f949e4a322a2218736fc9ebe60442
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677917"
---
# <a name="application-insights-export-data-model"></a>Gegevens model Application Insights exporteren
Deze tabel bevat de eigenschappen van de telemetrie die van de [Application Insights](../../azure-monitor/app/app-insights-overview.md) sdk's naar de portal worden verzonden.
Deze eigenschappen worden weer gegeven in gegevens uitvoer van [doorlopend exporteren](export-telemetry.md).
Ze worden ook weer gegeven in eigenschaps filters in [metrische Explorer](../../azure-monitor/app/metrics-explorer.md) en [Diagnostische Zoek opdrachten](../../azure-monitor/app/diagnostic-search.md).

Punten om te noteren:

* `[0]` in deze tabellen duidt een punt aan in het pad waar u een index moet invoegen. maar dit is niet altijd 0.
* De tijds duur ligt in tienden van een micro seconde, dus 10000000 = = 1 seconde.
* Datums en tijden zijn UTC en worden uitgedrukt in de ISO-notatie `yyyy-MM-DDThh:mm:ss.sssZ`


## <a name="example"></a>Voorbeeld
    // A server report about an HTTP request
    {
    "request": [
      {
        "urlData": { // derived from 'url'
          "host": "contoso.org",
          "base": "/",
          "hashTag": ""
        },
        "responseCode": 200, // Sent to client
        "success": true, // Default == responseCode<400
        // Request id becomes the operation id of child events
        "id": "fCOhCdCnZ9I=",  
        "name": "GET Home/Index",
        "count": 1, // 100% / sampling rate
        "durationMetric": {
          "value": 1046804.0, // 10000000 == 1 second
          // Currently the following fields are redundant:
          "count": 1.0,
          "min": 1046804.0,
          "max": 1046804.0,
          "stdDev": 0.0,
          "sampledValue": 1046804.0
        },
        "url": "/"
      }
    ],
    "internal": {
      "data": {
        "id": "7f156650-ef4c-11e5-8453-3f984b167d05",
        "documentVersion": "1.61"
      }
    },
    "context": {
      "device": { // client browser
        "type": "PC",
        "screenResolution": { },
        "roleInstance": "WFWEB14B.fabrikam.net"
      },
      "application": { },
      "location": { // derived from client ip
        "continent": "North America",
        "country": "United States",
        // last octagon is anonymized to 0 at portal:
        "clientip": "168.62.177.0",
        "province": "",
        "city": ""
      },
      "data": {
        "isSynthetic": true, // we identified source as a bot
        // percentage of generated data sent to portal:
        "samplingRate": 100.0,
        "eventTime": "2016-03-21T10:05:45.7334717Z" // UTC
      },
      "user": {
        "isAuthenticated": false,
        "anonId": "us-tx-sn1-azr", // bot agent id
        "anonAcquisitionDate": "0001-01-01T00:00:00Z",
        "authAcquisitionDate": "0001-01-01T00:00:00Z",
        "accountAcquisitionDate": "0001-01-01T00:00:00Z"
      },
      "operation": {
        "id": "fCOhCdCnZ9I=",
        "parentId": "fCOhCdCnZ9I=",
        "name": "GET Home/Index"
      },
      "cloud": { },
      "serverDevice": { },
      "custom": { // set by custom fields of track calls
        "dimensions": [ ],
        "metrics": [ ]
      },
      "session": {
        "id": "65504c10-44a6-489e-b9dc-94184eb00d86",
        "isFirst": true
      }
    }
  }

## <a name="context"></a>Context
Alle soorten telemetrie zijn vergezeld van een context sectie. Niet al deze velden worden met elk gegevens punt verzonden.

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| context. custom. Dimensions [0] |object [] |Sleutel-waarde teken reeks paren die zijn ingesteld met de para meter aangepaste eigenschappen. Maximum lengte van de sleutel 100, waarden met een maximale lengte van 1024. Meer dan 100 unieke waarden, de eigenschap kan worden doorzocht, maar kan niet worden gebruikt voor segmentatie. Maxi maal 200 sleutels per iKey. |
| context. custom. Metrics [0] |object [] |Sleutel-waardeparen ingesteld op basis van de para meter voor aangepaste metingen en door TrackMetrics. Maximum lengte van de sleutel 100, waarden kunnen numeriek zijn. |
| context. data. eventTime |string |UTC |
| context. data. isSynthetic |booleaans |De aanvraag lijkt afkomstig te zijn van een bot of Webtest. |
| context. data. samplingRate |getal |Het percentage van de telemetrie dat is gegenereerd door de SDK en dat naar de portal wordt verzonden. Bereik 0.0-100,0. |
| context. apparaat |object |Client apparaat |
| context. device. browser |string |IE, Chrome,... |
| context. device. browserVersion |string |Chrome 48,0,... |
| context. device. deviceModel |string | |
| context. device. DeviceName |string | |
| context.device.id |string | |
| context. apparaat. land instelling |string |en-GB, de-DE,... |
| context. device. Network |string | |
| context. device. oemName |string | |
| context. device. OS |string | |
| context. device. onmedium |string |Host-besturings systeem |
| context. device. roleInstance |string |ID van de server host |
| context. device. rolnaam |string | |
| context. device. screenResolution |string | |
| context. device. type |string |PC, browser,... |
| context. locatie |object |Afgeleid van client. |
| context. locatie. plaats |string |Afgeleid van client, indien bekend |
| context. location. client |string |De laatste Octagon is geanonimiseerd op 0. |
| context. locatie. continent |string | |
| context. locatie. Country |string | |
| context. location. provincie |string |Staat of provincie |
| context.operation.id |string |Items met dezelfde bewerkings-id worden weer gegeven als gerelateerde items in de portal. Doorgaans de aanvraag-id. |
| context.operation.name |string |URL of aanvraag naam |
| context. Operation. parentId |string |Hiermee staat u geneste gerelateerde items toe. |
| context.session.id |string |Id van een groep bewerkingen uit dezelfde bron. Een periode van 30 minuten zonder een bewerking signaleert het einde van een sessie. |
| context. Session. isFirst |booleaans | |
| context. User. accountAcquisitionDate |string | |
| context. User. accountId |string | |
| context. User. anonAcquisitionDate |string | |
| context. User. anonId |string | |
| context. User. authAcquisitionDate |string |[Geverifieerde gebruiker](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context. User. authId |string | |
| context. User. isAuthenticated |booleaans | |
| context. User. storeRegion |string | |
| intern. data. documentVersion |string | |
| internal.data.id |string | De unieke id die wordt toegewezen wanneer een item wordt opgenomen in Application Insights |

## <a name="events"></a>Evenements
Aangepaste gebeurtenissen gegenereerd door [track Event ()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| aantal gebeurtenissen [0] |geheel getal |100/([sampling](../../azure-monitor/app/sampling.md) frequentie). Bijvoorbeeld 4 = &gt; 25%. |
| gebeurtenis [0] naam |string |Gebeurtenis naam.  Maximale lengte van 250. |
| gebeurtenis [0] URL |string | |
| gebeurtenis [0] urlData. base |string | |
| gebeurtenis [0] urlData. host |string | |

## <a name="exceptions"></a>Uitzonderingen
Rapporteert [uitzonde ringen](../../azure-monitor/app/asp-net-exceptions.md) op de server en in de browser.

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| basicException [0]-Assembly |string | |
| aantal basicException [0] |geheel getal |100/([sampling](../../azure-monitor/app/sampling.md) frequentie). Bijvoorbeeld 4 = &gt; 25%. |
| basicException [0] exceptionGroup |string | |
| basicException [0] exceptionType |string | |
| basicException [0] failedUserCodeMethod |string | |
| basicException [0] failedUserCodeAssembly |string | |
| basicException [0] handledAt |string | |
| basicException [0] hasFullStack |booleaans | |
| basicException [0] id |string | |
| basicException [0]-methode |string | |
| basicException [0] bericht |string |Uitzonderings bericht. Maximale lengte van 10k. |
| basicException [0] outerExceptionMessage |string | |
| basicException [0] outerExceptionThrownAtAssembly |string | |
| basicException [0] outerExceptionThrownAtMethod |string | |
| basicException [0] outerExceptionType |string | |
| basicException [0] outerId |string | |
| basicException [0] parsedStack [0] Assembly |string | |
| basicException [0] parsedStack [0] bestands naam |string | |
| basicException [0] parsedStack [0] niveau |geheel getal | |
| basicException [0] parsedStack [0] regel |geheel getal | |
| basicException [0] parsedStack [0]-methode |string | |
| basicException [0] stack |string |Maximale lengte 10k |
| basicException [0] typeName |string | |

## <a name="trace-messages"></a>Berichten traceren
Verzonden door [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)en door de [logboek registratie-adapters](../../azure-monitor/app/asp-net-trace-logs.md).

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| bericht [0] logger logboek |string | |
| bericht [0] para meters |string | |
| bericht [0] RAW |string |Het logboek bericht heeft een maximale lengte van 10k. |
| bericht [0] severityLevel |string | |

## <a name="remote-dependency"></a>Externe afhankelijkheid
Verzonden door TrackDependency. Wordt gebruikt voor het rapporteren van de prestaties en het gebruik van [aanroepen naar afhankelijkheden](../../azure-monitor/app/asp-net-dependencies.md) in de server en Ajax-aanroepen in de browser.

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| remoteDependency [0] async |booleaans | |
| remoteDependency [0] basenaam |string | |
| remoteDependency [0] opdrachtnaam |string |Bijvoorbeeld ' home/index ' |
| aantal remoteDependency [0] |geheel getal |100/([sampling](../../azure-monitor/app/sampling.md) frequentie). Bijvoorbeeld 4 = &gt; 25%. |
| remoteDependency [0] dependencyTypeName |string |HTTP, SQL,... |
| remoteDependency [0] durationMetric. waarde |getal |Tijd van oproep tot voltooiing van antwoord door afhankelijkheid |
| remoteDependency [0] id |string | |
| remoteDependency [0] naam |string |URL. Maximale lengte van 250. |
| remoteDependency [0] resultCode |string |van HTTP-afhankelijkheid |
| remoteDependency [0] geslaagd |booleaans | |
| remoteDependency [0] type |string |Http, SQL,... |
| remoteDependency [0] URL |string |Maximale lengte van 2000 |
| remoteDependency [0] urlData. base |string |Maximale lengte van 2000 |
| remoteDependency [0] urlData. hashTag |string | |
| remoteDependency [0] urlData. host |string |Maximale lengte van 200 |

## <a name="requests"></a>Aanvragen
Verzonden door [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). De standaard modules gebruiken dit om de reactie tijd van de server te rapporteren, gemeten op de server.

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| aantal aanvragen [0] |geheel getal |100/([sampling](../../azure-monitor/app/sampling.md) frequentie). Bijvoorbeeld: 4 = &gt; 25%. |
| aanvraag [0] durationMetric. waarde |getal |Tijd van aanvraag die inkomt bij antwoord. 1e7 = = 1S |
| id van aanvraag [0] |string |Bewerkings-id |
| naam van aanvraag [0] |string |GET/POST + URL-basis.  Maximale lengte van 250 |
| aanvraag [0] responseCode |geheel getal |HTTP-antwoord verzonden naar client |
| de aanvraag [0] is voltooid |booleaans |Standaard = = (responseCode &lt; 400) |
| URL van aanvraag [0] |string |Geen host |
| aanvraag [0] urlData. base |string | |
| aanvraag [0] urlData. hashTag |string | |
| aanvraag [0] urlData. host |string | |

## <a name="page-view-performance"></a>Prestaties van pagina weergave
Verzonden door de browser. Hiermee wordt de tijd gemeten voor het verwerken van een pagina, van de gebruiker die de aanvraag initieert om volledig weer te geven (exclusief asynchrone AJAX-aanroepen).

Met context waarden wordt het client besturingssysteem en de browser versie weer gegeven.

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| clientPerformance [0] clientProcess. waarde |geheel getal |De tijd vanaf het moment dat de HTML wordt ontvangen om de pagina weer te geven. |
| clientPerformance [0] naam |string | |
| clientPerformance [0] networkConnection. waarde |geheel getal |De tijd die nodig is om een netwerk verbinding tot stand te brengen. |
| clientPerformance [0] receiveRequest. waarde |geheel getal |De tijd vanaf het einde van het verzenden van de aanvraag om de HTML in antwoord te ontvangen. |
| clientPerformance [0] sendRequest. waarde |geheel getal |De tijd die nodig is om de HTTP-aanvraag te verzenden. |
| clientPerformance [0] totale. waarde |geheel getal |De start tijd voor het verzenden van de aanvraag om de pagina weer te geven. |
| clientPerformance [0] URL |string |URL van deze aanvraag |
| clientPerformance [0] urlData. base |string | |
| clientPerformance [0] urlData. hashTag |string | |
| clientPerformance [0] urlData. host |string | |
| clientPerformance [0] urlData. Protocol |string | |

## <a name="page-views"></a>Pagina weergaven
Verzonden door trackPageView () of [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| aantal weer geven [0] |geheel getal |100/([sampling](../../azure-monitor/app/sampling.md) frequentie). Bijvoorbeeld 4 = &gt; 25%. |
| weer gave [0] durationMetric. waarde |geheel getal |De waarde is optioneel ingesteld in trackPageView () of door startTrackPage ()-stopTrackPage (). Niet hetzelfde als clientPerformance-waarden. |
| naam van weer gave [0] |string |Pagina titel.  Maximale lengte van 250 |
| URL van weer gave [0] |string | |
| weer gave [0] urlData. base |string | |
| weer gave [0] urlData. hashTag |string | |
| weer gave [0] urlData. host |string | |

## <a name="availability"></a>Beschikbaarheid
Rapporten over [Beschik baarheid van webtests](../../azure-monitor/app/monitor-web-app-availability.md).

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| Beschik baarheid [0] availabilityMetric.name |string |availability |
| Beschik baarheid [0] availabilityMetric. waarde |getal |1,0 of 0,0 |
| aantal Beschik baarheid [0] |geheel getal |100/([sampling](../../azure-monitor/app/sampling.md) frequentie). Bijvoorbeeld 4 = &gt; 25%. |
| Beschik baarheid [0] dataSizeMetric.name |string | |
| Beschik baarheid [0] dataSizeMetric. waarde |geheel getal | |
| Beschik baarheid [0] durationMetric.name |string | |
| Beschik baarheid [0] durationMetric. waarde |getal |Duur van de test. 1e7 = = 1S |
| bericht Beschik baarheid [0] |string |Diagnose van fouten |
| resultaat van Beschik baarheid [0] |string |Geslaagd/mislukt |
| Beschik baarheid [0] runLocation |string |Geo-bron van HTTP-aanvraag |
| Beschik baarheid [0] testnaam |string | |
| Beschik baarheid [0] testRunId |string | |
| Beschik baarheid [0] testTimestamp |string | |

## <a name="metrics"></a>Metrische gegevens
Gegenereerd door TrackMetric ().

De metrische waarde is gevonden in context. custom. Metrics [0]

Bijvoorbeeld:

    {
     "metric": [ ],
     "context": {
     ...
     "custom": {
        "dimensions": [
          { "ProcessId": "4068" }
        ],
        "metrics": [
          {
            "dispatchRate": {
              "value": 0.001295,
              "count": 1.0,
              "min": 0.001295,
              "max": 0.001295,
              "stdDev": 0.0,
              "sampledValue": 0.001295,
              "sum": 0.001295
            }
          }
         } ] }
    }

## <a name="about-metric-values"></a>Metrische waarden
Metrische waarden, zowel in metrische rapporten als elders, worden gerapporteerd met een standaard-object structuur. Bijvoorbeeld:

      "durationMetric": {
        "name": "contoso.org",
        "type": "Aggregation",
        "value": 468.71603053650279,
        "count": 1.0,
        "min": 468.71603053650279,
        "max": 468.71603053650279,
        "stdDev": 0.0,
        "sampledValue": 468.71603053650279
      }

Dit kan al dan niet worden gewijzigd in de toekomst: alle waarden die zijn gerapporteerd uit de standaard SDK-modules, `count==1` en alleen de velden `name` en `value` zijn nuttig. Wanneer u uw eigen TrackMetric-aanroepen schrijft waarin u de andere para meters hebt ingesteld, is de enige andere situatie waar ze verschillend zouden zijn.

Het doel van de andere velden is om toe te staan dat metrische gegevens in de SDK worden geaggregeerd, om het verkeer naar de portal te verminderen. U kunt bijvoorbeeld een gemiddelde berekenen van een aantal opeenvolgende Lees bewerking voordat u elk rapport met metrische gegevens verzendt. Vervolgens berekent u de minimum-, maximum-, standaard afwijking-en aggregatie waarde (Sum of average) en stelt u het aantal lees bewerkingen in dat door het rapport wordt vertegenwoordigd.

In de bovenstaande tabellen zijn het aantal zelden gebruikte velden, min, Max, stddev verwerken en sampledValue wegge laten.

In plaats van vooraf aggregatie van metrische gegevens, kunt u [steek proeven](../../azure-monitor/app/sampling.md) gebruiken als u het volume van de telemetrie wilt reduceren.

### <a name="durations"></a>Duur
Tenzij anders aangegeven, worden de duursen weer gegeven in tienden van een micro seconde, zodat 10000000,0 betekent 1 seconde.

## <a name="see-also"></a>Zie ook
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Continue export](export-telemetry.md)
* [Codevoorbeelden](export-telemetry.md#code-samples)
