---
title: Aangepaste weer gaven met geparametriseerde Url's delen-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het maken van geparametriseerde Url's voor het eenvoudig delen van aangepaste Explorer-weer gaven in Azure Time Series Insights.
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
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024361"
---
# <a name="share-a-custom-view-using-a-parameterized-url"></a>Een aangepaste weergave delen via een geparameteriseerde URL

Als u een aangepaste weer gave in Time Series Insights Explorer wilt delen, kunt u programmatisch een geparametriseerde URL maken van de aangepaste weer gave.

De Time Series Insights Explorer ondersteunt URL-query parameters om weer gaven in de ervaring rechtstreeks vanuit de URL op te geven. U kunt bijvoorbeeld via de URL een doelomgeving, een zoekpredicaat en de gewenste tijdspanne opgeven. Wanneer een gebruiker de aangepaste URL selecteert, biedt de interface een koppeling rechtstreeks naar die asset in de Time Series Insights Portal. Beleid voor gegevenstoegang wordt toegepast.

> [!TIP]
> * Bekijk de gratis [Time Series Insights demo](https://insights.timeseries.azure.com/samples).
> * Lees de documentatie bij de bijbehorende [Time Series Insights Explorer](./time-series-insights-explorer.md) .

## <a name="environment-id"></a>Omgevings-id

De parameter `environmentId=<guid>` geeft de id van de doelomgeving op. Het is een onderdeel van de FQDN van gegevens toegang en u vindt het in de rechter bovenhoek van het overzicht van de omgeving in het Azure Portal. Dit is alles wat voorafgaat aan `env.timeseries.azure.com`.

Een voorbeeld van de parameter voor omgevings-id is `?environmentId=10000000-0000-0000-0000-100000000108`.

## <a name="time"></a>Tijd

U kunt absolute of relatieve tijdwaarden opgeven met een geparameteriseerde URL.

### <a name="absolute-time-values"></a>Absolute tijdwaarden

Voor absolute tijdwaarden gebruikt u de parameters `from=<integer>` en `to=<integer>`.

* `from=<integer>` is een waarde in JavaScript-milliseconden voor de begintijd van de tijdspanne voor zoeken.
* `to=<integer>` is een waarde in JavaScript-milliseconden voor de eindtijd van de tijdspanne voor zoeken.

> [!TIP]
> Als u eenvoudig datums wilt omzetten in Java script-milliseconden, probeert u de [epoche & Unix Time Stamp Converter](https://www.freeformatter.com/epoch-timestamp-to-date-converter.html).

### <a name="relative-time-values"></a>Relatieve tijdwaarden

Gebruik `relativeMillis=<value>`voor een relatieve tijd waarde, waarbij *waarde* zich in Java script-milliseconden bevindt van de meest recente tijds tempel die is ontvangen van de API.

`&relativeMillis=3600000` geeft bijvoorbeeld de afgelopen 60 minuten aan gegevens weer.

Geaccepteerde waarden komen overeen met het menu **snelle tijd** van Time Series Insights Explorer en bevatten het volgende:

* `1800000` (afgelopen 30 minuten)
* `3600000` (afgelopen 60 minuten)
* `10800000` (afgelopen 3 uur)
* `21600000` (afgelopen 6 uur)
* `43200000` (afgelopen 12 uur)
* `86400000` (afgelopen 24 uur)
* `604800000` (afgelopen 7 dagen)
* `2592000000` (afgelopen 30 uur)

### <a name="optional-parameters"></a>Optionele parameters

Met de para meter `timeSeriesDefinitions=<collection of term objects>` worden de predicaat termen opgegeven die worden weer gegeven in een Time Series Insights weer gave:

| Parameter | URL-item | Beschrijving |
| --- | --- | --- |
| **De naam** | `\<string>` | De naam van het *onderdeel*. |
| **splitBy** | `\<string>` | De naam van de kolom waarop moet worden *gesplitst*. |
| **measureName** | `\<string>` | De kolomnaam van de *meting*. |
| **predicaat** | `\<string>` | De *where*-component voor filteren aan de serverzijde. |
| **useSum** | `true` | Een optionele para meter waarmee het gebruik van een som voor uw meting wordt opgegeven. |

> [!NOTE]
> Als `Events` de geselecteerde **useSum** -meting is, is aantal standaard geselecteerd.  
> Als `Events` niet is geselecteerd, wordt standaard het gemiddelde geselecteerd. |

* Het `multiChartStack=<true/false>` sleutel-waardepaar maakt stapeling in de grafiek mogelijk.
* Met de `multiChartSameScale=<true/false>` sleutel-waardepaar kunt u dezelfde schaal van de Y-as voor alle termen binnen een optionele para meter.  
* Met de `timeBucketUnit=<Unit>&timeBucketSize=<integer>` kunt u de schuif regelaar voor het interval aanpassen om een meer gedetailleerde of vloeiendere weer gave van de grafiek te bieden.  
* Met de para meter `timezoneOffset=<integer>` kunt u de tijd zone voor de grafiek instellen, zodat deze wordt weer gegeven als een offset naar UTC.

| Paar (s) | Beschrijving |
| --- | --- |
| `multiChartStack=false` | `true` is standaard ingeschakeld, dus geef `false` door. |
| `multiChartStack=false&multiChartSameScale=true` | Stapelen moet zijn ingeschakeld om dezelfde schaling van de Y-as te gebruiken voor verschillende onderdelen.  Het is standaard `false`, dus door door te geven `true` deze functionaliteit mogelijk maakt. |
| `timeBucketUnit=<Unit>&timeBucketSize=<integer>` | Units = `days`, `hours`, `minutes`, `seconds``milliseconds`.  Gebruik altijd een hoofdletter voor de eenheid. </br> Definieer het aantal eenheden door het gewenste gehele getal voor **timeBucketSize**door te geven.  |
| `timezoneOffset=-<integer>` | Het gehele getal is altijd in milliseconden. |

> [!NOTE]
> **time Bucket unit** waarden kunnen Maxi maal zeven dagen worden gesoepeld.
> **time zone offset** waarden zijn geen UTC of lokale tijd.

### <a name="examples"></a>Voorbeelden

Als u de definities van de tijd reeks wilt toevoegen aan een Time Series Insights omgeving als een URL-para meter, voegt u het volgende toe:

```URL parameter
&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},
{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

Gebruik de voor beelden van Time Series-definities voor:

* De omgevings-ID
* De laatste 60 minuten aan gegevens
* De voor waarden (**F1PressureID**, **F2TempStation**en **F3VibrationPL**) waaruit de optionele para meters bestaan

U kunt de volgende geparametriseerde URL voor een weer gave maken:

```URL
https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]
```

[![Time Series Insights Explorer-URL met para meters](media/parameterized-url/share-parameterized-url.png)](media/parameterized-url/share-parameterized-url.png#lightbox)

> [!TIP]
> Bekijk de Explorer Live [met behulp van het bovenstaande URL](https://insights.timeseries.azure.com/samples?environmentId=10000000-0000-0000-0000-100000000108&relativeMillis=3600000&timeSeriesDefinitions=[{"name":"F1PressureId","splitBy":"Id","measureName":"Pressure","predicate":"'Factory1'"},{"name":"F2TempStation","splitBy":"Station","measureName":"Temperature","predicate":"'Factory2'"},{"name":"F3VibrationPL","splitBy":"ProductionLine","measureName":"Vibration","predicate":"'Factory3'"}]) -voor beeld.

De URL hierboven wordt beschreven en geeft de weer gave Time Series Insights Explorer met para meters. 

* De para meters-predikaten.

  [![Time Series Insights Explorer-predikaten met para meters.](media/parameterized-url/share-parameterized-url-predicates.png)](media/parameterized-url/share-parameterized-url-predicates.png#lightbox)

* De gedeelde volledige grafiek weergave.

  [![de gedeelde volledige grafiek weergave.](media/parameterized-url/share-parameterized-url-full-chart.png)](media/parameterized-url/share-parameterized-url-full-chart.png#lightbox)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [opvragen van C#gegevens met behulp van ](time-series-insights-query-data-csharp.md).

* Meer informatie over de [Time Series Insights Explorer](./time-series-insights-explorer.md).
