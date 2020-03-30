---
title: Aangepaste weergaven delen met geparameteriseerde URL's - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over het maken van geparameteriseerde URL's om eenvoudig aangepaste explorerweergaven te delen in Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.topic: conceptual
ms.workload: big-data
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 9dfe499a7d6084a23fd71ab98db472befe71fc04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024361"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Een aangepaste weergave delen via een geparameteriseerde URL

Als u een aangepaste weergave wilt delen in Time Series Insights Explorer, u programmatisch een geparameteriseerde URL van de aangepaste weergave maken.

De Time Series Insights Explorer ondersteunt URL-queryparameters om weergaven in de ervaring rechtstreeks vanuit de URL op te geven. U kunt bijvoorbeeld via de URL een doelomgeving, een zoekpredicaat en de gewenste tijdspanne opgeven. Wanneer een gebruiker de aangepaste URL selecteert, biedt de interface rechtstreeks een koppeling naar dat item in de Time Series Insights-portal. Beleid voor gegevenstoegang wordt toegepast.

> [!TIP]
> * Bekijk de gratis [Time Series Insights demo](https://insights.timeseries.azure.com/samples).
> * Lees de bijbehorende [Time Series Insights Explorer-documentatie.](./time-series-insights-explorer.md)

## <a name="environment-id"></a>Omgevings-id

De parameter `environmentId=<guid>` geeft de id van de doelomgeving op. Het is een onderdeel van de FQDN voor gegevenstoegang en u het vinden in de rechterbovenhoek van het omgevingsoverzicht in de Azure-portal. Dit is alles wat voorafgaat aan `env.timeseries.azure.com`.

Een voorbeeld van de parameter voor omgevings-id is `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Time

U kunt absolute of relatieve tijdwaarden opgeven met een geparameteriseerde URL.

### <a name="absolute-time-values"></a>Absolute tijdwaarden

Voor absolute tijdwaarden gebruikt u de parameters `from=<integer>` en `to=<integer>`.

* `from=<integer>` is een waarde in JavaScript-milliseconden voor de begintijd van de tijdspanne voor zoeken.
* `to=<integer>` is een waarde in JavaScript-milliseconden voor de eindtijd van de tijdspanne voor zoeken.

> [!TIP]
> Als u datums eenvoudig wilt vertalen naar JavaScript-milliseconden, probeert u de [Epoch-& Unix Timestamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relatieve tijdwaarden

Gebruik voor een relatieve `relativeMillis=<value>`tijdswaarde , waarbij *de waarde* zich in JavaScript-milliseconden bevindt ten opzichte van de meest recente tijdstempel die van de API is ontvangen.

`&relativeMillis=3600000` geeft bijvoorbeeld de afgelopen 60 minuten aan gegevens weer.

Geaccepteerde waarden komen overeen met het **menu Time** Series Insights explorer en omvatten:

* `1800000`(Laatste 30 minuten)
* `3600000`(Laatste 60 minuten)
* `10800000`(Laatste 3 uur)
* `21600000`(Laatste 6 uur)
* `43200000`(Laatste 12 uur)
* `86400000`(Laatste 24 uur)
* `604800000`(Laatste 7 dagen)
* `2592000000`(Laatste 30 uur)

### <a name="optional-parameters"></a>Optionele parameters

De `timeSeriesDefinitions=<collection of term objects>` parameter geeft predicaattermen op die worden weergegeven in een weergave Time Series Insights:

| Parameter | URL-item | Beschrijving |
| --- | --- | --- |
| **Naam** | `\<string>` | De naam van het *onderdeel*. |
| **splitBy** | `\<string>` | De naam van de kolom waarop moet worden *gesplitst*. |
| **measureName measureName measureName measureName** | `\<string>` | De kolomnaam van de *meting*. |
| **Predikaat** | `\<string>` | De *where*-component voor filteren aan de serverzijde. |
| **useSOm** | `true` | Een optionele parameter die aangeeft met behulp van som voor uw meting. |

> [!NOTE]
> Als `Events` is de geselecteerde **useSOm** maatregel, telling is standaard geselecteerd.  
> Als `Events` niet is geselecteerd, wordt het gemiddelde standaard geselecteerd. |

* Het `multiChartStack=<true/false>` sleutelwaardepaar maakt stapelen in de grafiek mogelijk.
* Het `multiChartSameScale=<true/false>` sleutelwaardepaar maakt dezelfde Y-asschaal mogelijk tussen termen binnen een optionele parameter.  
* Hiermee `timeBucketUnit=<Unit>&timeBucketSize=<integer>` u de intervalschuifregelaar aanpassen om een meer gedetailleerde of vloeiendere, meer geaggregeerde weergave van de grafiek te bieden.  
* Met `timezoneOffset=<integer>` de parameter u de tijdzone instellen waarop de grafiek moet worden weergegeven als een verschuiving naar UTC.

| Paar(en) | Beschrijving |
| --- | --- |
| `multiChartStack=false` | `true`is standaard ingeschakeld, `false` dus pas door om te stapelen. |
| `multiChartStack=false&multiChartSameScale=true` | Stapelen moet zijn ingeschakeld om dezelfde schaling van de Y-as te gebruiken voor verschillende onderdelen.  Het is `false` standaard, dus `true` het passeren maakt deze functionaliteit mogelijk. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Eenheden `days`= `hours` `minutes`, `seconds` `milliseconds`, , , .  Gebruik altijd een hoofdletter voor de eenheid. </br> Definieer het aantal eenheden door het gewenste gehele getal voor **timeBucketSize door te**geven .  |
| `timezoneOffset=-<integer>` | Het gehele getal is altijd in milliseconden. |

> [!NOTE]
> **timeBucketUnit-waarden** kunnen tot 7 dagen worden gladgestreken.
> **timezoneOffset-waarden** zijn geen UTC of lokale tijd.

### <a name="examples"></a>Voorbeelden

Als u tijdreeksdefinities wilt toevoegen aan een omgeving met tijdzones als URL-parameter, voegt u het als gevolg van:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Gebruik de definities van voorbeeldtijdreeksen voor:

* De omgevings-ID
* De laatste 60 minuten aan gegevens
* De termen (**F1PressureID**, **F2TempStation**en **F3VibrationPL**) die de optionele parameters omvatten

U de volgende geparameteriseerde URL voor een weergave maken:

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Url van Time Series Insights explorer](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Zie de Explorer live met het bovenstaande [URL-voorbeeld.](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}])

De bovenstaande URL beschrijft en geeft de geparameteriseerde time series Insights explorer-weergave weer. 

* De geparameteriseerde predicaten.

  [![Time Series Insights explorer parameterized predicaten.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* De gedeelde volledige grafiekweergave.

  [![De gedeelde volledige grafiekweergave.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [opvragen van gegevens met C#](time-series-insights-query-data-csharp.md).

* Meer informatie over de [Time Series Insights Explorer](./time-series-insights-explorer.md).
