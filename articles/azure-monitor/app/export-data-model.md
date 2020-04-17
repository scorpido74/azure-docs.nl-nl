---
title: Gegevensmodel Azure Application Insights | Microsoft Documenten
description: Beschrijft eigenschappen die zijn geëxporteerd vanuit continue export in JSON en worden gebruikt als filters.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: 9891bea1d52c61197fa32fa5c0764df5450b563c
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536842"
---
# <a name="application-insights-export-data-model"></a>Gegevensmodel voor exportgegevens van toepassingsinzichten
In deze tabel worden de eigenschappen van telemetrie weergegeven die vanuit de [SDK's](../../azure-monitor/app/app-insights-overview.md) van Application Insights naar de portal worden verzonden.
U ziet deze eigenschappen in gegevensuitvoer van [Continue export.](export-telemetry.md)
Ze worden ook weergegeven in eigenschapsfilters in [Metric Explorer](../../azure-monitor/platform/metrics-charts.md) en [Diagnostisch zoeken.](../../azure-monitor/app/diagnostic-search.md)

Aandachtspunten:

* `[0]`in deze tabellen geeft een punt aan in het pad waar u een index moet invoegen; maar het is niet altijd 0.
* Tijdsduur is in tienden van een microseconde, dus 10000000 == 1 seconde.
* Datums en tijden zijn UTC en worden gegeven in de ISO-indeling`yyyy-MM-DDThh:mm:ss.sssZ`


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
Alle soorten telemetrie gaan vergezeld van een contextsectie. Niet al deze velden worden met elk gegevenspunt verzonden.

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| context.custom.dimensions [0] |object [ ] |Tekenreeksparen met sleutelwaarde die zijn ingesteld op de parameter Aangepaste eigenschappen. Sleutel max lengte 100, waarden max lengte 1024. Meer dan 100 unieke waarden, de eigenschap kan worden doorzocht, maar kan niet worden gebruikt voor segmentatie. Max 200 sleutels per ikey. |
| context.custom.metrics [0] |object [ ] |Sleutelparen ingesteld op basis van aangepaste metingen parameter en door TrackMetrics. Toets maximale lengte 100, waarden kunnen numeriek zijn. |
| context.data.eventTime |tekenreeks |UTC |
| context.data.isSynthetisch |booleaans |Verzoek lijkt te komen van een bot of web test. |
| context.data.samplingRate |getal |Percentage telemetrie gegenereerd door de SDK dat naar portal wordt verzonden. Bereik 0,0-100,0. |
| context.device |object |Clientapparaat |
| context.device.browser |tekenreeks |IE, Chrome, ... |
| context.device.browserVersie |tekenreeks |Chroom 48.0, ... |
| context.device.deviceModel |tekenreeks | |
| context.device.deviceNaam |tekenreeks | |
| context.device.id |tekenreeks | |
| context.device.locale |tekenreeks |nl-GB, de-DE, ... |
| context.device.network |tekenreeks | |
| context.device.oemNaam |tekenreeks | |
| context.device.os |tekenreeks | |
| context.device.osVersie |tekenreeks |Hostbesturingssyteem |
| context.device.roleInstance |tekenreeks |ID van serverhost |
| context.device.roleName |tekenreeks | |
| context.device.screenResolutie |tekenreeks | |
| context.device.type |tekenreeks |PC, Browser, ... |
| context.locatie |object |Afgeleid `clientip`van . |
| context.location.city |tekenreeks |Afgeleid `clientip`van , indien bekend |
| context.location.clientip |tekenreeks |Laatste achthoek is geanonimiseerd tot 0. |
| context.location.continent |tekenreeks | |
| context.location.country |tekenreeks | |
| context.location.provincie |tekenreeks |Staat of provincie |
| context.operation.id |tekenreeks |Items met dezelfde `operation id` items worden weergegeven als gerelateerde items in de portal. Meestal `request id`de . |
| context.operation.name |tekenreeks |url of aanvraagnaam |
| context.operation.parentId |tekenreeks |Hiermee kunnen geneste gerelateerde items worden toegestaan. |
| context.session.id |tekenreeks |`Id`van een groep bewerkingen uit dezelfde bron. Een periode van 30 minuten zonder bewerking betekent het einde van een sessie. |
| context.session.isEerste |booleaans | |
| context.user.accountAcquisitiedatum |tekenreeks | |
| context.user.accountId |tekenreeks | |
| context.user.anonAcquisitionDate |tekenreeks | |
| context.user.anonId |tekenreeks | |
| context.user.authAcquisitionDate |tekenreeks |[Geverifieerde gebruiker](../../azure-monitor/app/api-custom-events-metrics.md#authenticated-users) |
| context.user.authId |tekenreeks | |
| context.user.isGeverifieerd |booleaans | |
| context.user.storeRegio |tekenreeks | |
| internal.data.documentVersie |tekenreeks | |
| internal.data.id |tekenreeks | `Unique id`die wordt toegewezen wanneer een item wordt ingenomen aan Application Insights |

## <a name="events"></a>Gebeurtenissen
Aangepaste gebeurtenissen gegenereerd door [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent).

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| gebeurtenis [0] tellen |geheel getal |100/([bemonsteringspercentage).](../../azure-monitor/app/sampling.md) Bijvoorbeeld 4&gt; = 25%. |
| gebeurtenis [0] naam |tekenreeks |Naam van het evenement.  Maximale lengte 250. |
| gebeurtenis [0] url |tekenreeks | |
| gebeurtenis [0] urlData.base |tekenreeks | |
| gebeurtenis [0] urlData.host |tekenreeks | |

## <a name="exceptions"></a>Uitzonderingen
Rapporteert [uitzonderingen](../../azure-monitor/app/asp-net-exceptions.md) in de server en in de browser.

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| basisuitzondering [0] assemblage |tekenreeks | |
| basisuitzondering [0] tellen |geheel getal |100/([bemonsteringspercentage).](../../azure-monitor/app/sampling.md) Bijvoorbeeld 4&gt; = 25%. |
| basisuitzondering [0] uitzonderingGroep |tekenreeks | |
| basisuitzondering [0] uitzonderingType |tekenreeks | |
| basicException [0] misluktUserCodeMethod |tekenreeks | |
| basicException [0] misluktUserCodeAssembly |tekenreeks | |
| basicException [0] behandeldAt |tekenreeks | |
| basicException [0] hasFullStack |booleaans | |
| basisuitzondering [0]`id` |tekenreeks | |
| basisuitzondering [0] methode |tekenreeks | |
| basisuitzondering [0] bericht |tekenreeks |Uitzonderingsbericht. Maximale lengte 10k. |
| basisuitzondering [0] outerExceptionMessage |tekenreeks | |
| basicException [0] outerExceptionThrownAtAssembly basicException [0] outerExceptionThrownAtAssembly basicException [0] outerExceptionThrownAtAssembly basicException |tekenreeks | |
| basisuitzondering [0] outerExceptionThrownAtMethod |tekenreeks | |
| basisuitzondering [0] outerExceptionType |tekenreeks | |
| basisuitzondering [0] outerId |tekenreeks | |
| basicException [0] parsedStack [0] assemblage |tekenreeks | |
| basicException [0] parsedStack [0] fileName |tekenreeks | |
| basicException [0] parsedStack [0] niveau |geheel getal | |
| basicException [0] parsedStack [0] regel |geheel getal | |
| basicException [0] parsedStack [0] methode |tekenreeks | |
| basisuitzondering [0] stack |tekenreeks |Maximale lengte 10k |
| basicException [0] typeNaam |tekenreeks | |

## <a name="trace-messages"></a>Berichten traceren
Verzonden door [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)en door de [logboekadapters](../../azure-monitor/app/asp-net-trace-logs.md).

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| bericht [0] loggerName |tekenreeks | |
| bericht [0] parameters |tekenreeks | |
| bericht [0] rauw |tekenreeks |Het logboekbericht, maximale lengte 10k. |
| bericht [0] ernstNiveau |tekenreeks | |

## <a name="remote-dependency"></a>Afhankelijkheid op afstand
Verzonden door TrackDependency. Wordt gebruikt om prestaties en het gebruik van [oproepen naar afhankelijkheden](../../azure-monitor/app/asp-net-dependencies.md) in de server te rapporteren en AJAX belt in de browser.

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| remoteDependency [0] async |booleaans | |
| remoteDependency [0] baseName |tekenreeks | |
| remoteDependency [0] commandName |tekenreeks |Bijvoorbeeld "home/index" |
| remoteDependency [0] tellen |geheel getal |100/([bemonsteringspercentage).](../../azure-monitor/app/sampling.md) Bijvoorbeeld 4&gt; = 25%. |
| afhankelijkheid van externe afhankelijkheid [0] |tekenreeks |HTTP, SQL, ... |
| duur van externeafhankelijkheid [0]Metric.value |getal |Tijd van oproep tot voltooiing van de respons door afhankelijkheid |
| afhankelijk van de afstandsbediening [0]`id` |tekenreeks | |
| naam remoteDependency [0] |tekenreeks |Url. Maximale lengte 250. |
| remoteDependency [0] resultCode |tekenreeks |van HTTP-afhankelijkheid |
| remoteDependency [0] succes |booleaans | |
| externafhankelijkheid [0] type |tekenreeks |Http, Sql,... |
| remoteDependency [0] url |tekenreeks |Maximale lengte 2000 |
| remoteDependency [0] urlData.base |tekenreeks |Maximale lengte 2000 |
| remoteDependency [0] urlData.hashTag |tekenreeks | |
| remoteDependency [0] urlData.host |tekenreeks |Maximale lengte 200 |

## <a name="requests"></a>Aanvragen
Verzonden door [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). De standaardmodules gebruiken dit om de responstijd van de server, gemeten op de server, te rapporten.

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| aanvraag [0] tellen |geheel getal |100/([bemonsteringspercentage).](../../azure-monitor/app/sampling.md) Bijvoorbeeld: 4&gt; = 25%. |
| aanvraag [0] duurMetric.value |getal |Tijd van het verzoek aankomen om te reageren. 1e7 == 1s |
| verzoek [0]`id` |tekenreeks |`Operation id` |
| aanvraag [0] naam |tekenreeks |GET/POST + url base.  Maximale lengte 250 |
| verzoek [0] responseCode |geheel getal |HTTP-antwoord verzonden naar client |
| verzoek [0] succes |booleaans |Standaard == (responseCode &lt; 400) |
| aanvraag [0] url |tekenreeks |Inclusief gastheer niet |
| aanvraag [0] urlData.base |tekenreeks | |
| aanvraag [0] urlData.hashTag |tekenreeks | |
| aanvraag [0] urlData.host |tekenreeks | |

## <a name="page-view-performance"></a>Prestaties paginaweergave
Verzonden door de browser. Hiermee meet u de tijd om een pagina te verwerken, van de gebruiker die het verzoek initieert om voltooid weer te geven (met uitzondering van async AJAX-oproepen).

Contextwaarden tonen client-besturingssysteem en browserversie.

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| clientPerformance [0] clientProces.value |geheel getal |Tijd vanaf het einde van het ontvangen van de HTML tot het weergeven van de pagina. |
| clientPrestatie [0] naam |tekenreeks | |
| clientPerformance [0] networkConnection.value |geheel getal |Tijd die nodig is om een netwerkverbinding tot stand te brengen. |
| clientPerformance [0] receiveRequest.value |geheel getal |Tijd vanaf het einde van het verzenden van het verzoek tot het ontvangen van de HTML in antwoord. |
| clientPerformance [0] sendRequest.value |geheel getal |De tijd die is genomen om het HTTP-verzoek te verzenden. |
| clientPerformance [0] totaal.waarde |geheel getal |Tijd vanaf het begin om het verzoek te verzenden naar het weergeven van de pagina. |
| clientPerformance [0] url |tekenreeks |URL van dit verzoek |
| clientPerformance [0] urlData.base |tekenreeks | |
| clientPerformance [0] urlData.hashTag |tekenreeks | |
| clientPerformance [0] urlData.host |tekenreeks | |
| clientPerformance [0] urlData.protocol |tekenreeks | |

## <a name="page-views"></a>Paginaweergaven
Verzonden via trackPageView() of [stopTrackPage](../../azure-monitor/app/api-custom-events-metrics.md#page-views)

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| bekijken [0] tellen |geheel getal |100/([bemonsteringspercentage).](../../azure-monitor/app/sampling.md) Bijvoorbeeld 4&gt; = 25%. |
| weergave [0] duurMetric.value |geheel getal |Waarde optioneel ingesteld in trackPageView() of door startTrackPage() - stopTrackPage(). Niet hetzelfde als clientPerformance-waarden. |
| weergave [0] naam |tekenreeks |Paginatitel.  Maximale lengte 250 |
| [0] url weergeven |tekenreeks | |
| bekijken [0] urlData.base |tekenreeks | |
| bekijken [0] urlData.hashTag |tekenreeks | |
| bekijken [0] urlData.host |tekenreeks | |

## <a name="availability"></a>Beschikbaarheid
Rapporten [beschikbaarheid webtests](../../azure-monitor/app/monitor-web-app-availability.md).

| Pad | Type | Opmerkingen |
| --- | --- | --- |
| beschikbaarheid [0] availabilityMetric.name |tekenreeks |availability |
| beschikbaarheid [0] beschikbaarheidMetric.value |getal |1.0 of 0.0 |
| beschikbaarheid [0] tellen |geheel getal |100/([bemonsteringspercentage).](../../azure-monitor/app/sampling.md) Bijvoorbeeld 4&gt; = 25%. |
| beschikbaarheid [0] dataSizeMetric.name |tekenreeks | |
| beschikbaarheid [0] dataSizeMetric.value |geheel getal | |
| beschikbaarheid [0] durationMetric.name |tekenreeks | |
| beschikbaarheid [0] duurMetric.value |getal |Duur van de test. 1e7==1s |
| beschikbaarheid [0] bericht |tekenreeks |Foutdiagnose |
| beschikbaarheid [0] resultaat |tekenreeks |Geslaagd/mislukt |
| beschikbaarheid [0] runLocatie |tekenreeks |Geo bron van http req |
| beschikbaarheid [0] testNaam |tekenreeks | |
| beschikbaarheid [0] testRunId |tekenreeks | |
| beschikbaarheid [0] testTimestamp |tekenreeks | |

## <a name="metrics"></a>Metrische gegevens
Gegenereerd door TrackMetric().

De metrische waarde wordt gevonden in context.custom.metrics[0]

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

## <a name="about-metric-values"></a>Informatie over metrische waarden
Metrische waarden, zowel in metrische rapporten als elders, worden gerapporteerd met een standaardobjectstructuur. Bijvoorbeeld:

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

Momenteel - hoewel dit in de toekomst kan veranderen - `count==1` in alle `name` `value` waarden gerapporteerd uit de standaard SDK-modules, en alleen de en velden zijn nuttig. Het enige geval waar ze anders zou zijn zou zijn als u uw eigen TrackMetric oproepen waarin u de andere parameters.

Het doel van de andere velden is om metrische gegevens te kunnen samenvoegen in de SDK, om het verkeer naar de portal te verminderen. U bijvoorbeeld meerdere opeenvolgende metingen gemiddelde geven voordat u elk metriekrapport verzendt. Vervolgens berekent u de min, max, standaarddeviatie en totale waarde (som of gemiddelde) en stelt u het aantal in op het aantal metingen dat door het rapport wordt weergegeven.

In de bovenstaande tabellen hebben we de zelden gebruikte velden telling, min, max, stdDev en sampledValue weggelaten.

In plaats van statistieken vooraf te aggregeren, u [steekproeven](../../azure-monitor/app/sampling.md) gebruiken als u het volume van telemetrie wilt verminderen.

### <a name="durations"></a>Duur
Tenzij anders vermeld, worden de duuren vertegenwoordigd in tienden van een microseconde, zodat 1000000.0 1 seconde betekent.

## <a name="see-also"></a>Zie ook
* [Application Insights](../../azure-monitor/app/app-insights-overview.md)
* [Continue export](export-telemetry.md)
* [Codevoorbeelden](export-telemetry.md#code-samples)
