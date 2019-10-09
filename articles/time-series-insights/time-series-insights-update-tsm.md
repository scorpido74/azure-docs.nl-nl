---
title: Time Series-model in Azure Time Series Insights preview | Microsoft Docs
description: Meer informatie over Azure Time Series Insights time series-model.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 8f4ed6de5ff47efa441c371b80670c500f57c9bb
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034058"
---
# <a name="time-series-model"></a>Time Series-model

In dit artikel wordt het tijds reeks model onderdeel van Azure Time Series Insights preview beschreven. Het model zelf, de mogelijkheden en de manier waarop u uw eigen model maakt en bijwerkt, wordt besproken.

De gegevens die op IoT-apparaten worden verzameld, hebben doorgaans geen contextuele informatie, waardoor het lastig is om Sens oren snel te vinden en te analyseren. De belangrijkste motivatie voor time series-model is het vereenvoudigen van het vinden en analyseren van IoT-gegevens. Deze doel stelling wordt gerealiseerd door de uitschakeling, het onderhoud en de verrijking van tijdreeks gegevens in te scha kelen voor het voorbereiden van voor bereidingen op basis van gebruikers.

Time Series-modellen spelen een belang rijke rol in query's en navigatie omdat ze waarmee apparaat-en niet-apparaat-entiteiten hebben. Gegevens die persistent zijn in het tijdreeks model, worden in de loop van de tijd Series-query berekeningen door gebruik te maken van de formules die erin zijn opgeslagen.

[overzicht van ![Time-serie model](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

## <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Met het doel om eenvoudig en moeiteloos tijd Series contextualization te beheren, maakt time series model de volgende mogelijkheden in Time Series Insights preview. U kunt het volgende doen:

* U kunt berekeningen of formules ontwerpen en beheren, gegevens transformeren met scalaire functies, aggregatie bewerkingen, enzovoort.
* Definieer de relaties tussen bovenliggende en onderliggende items om navigatie en referentie in te scha kelen en context te bieden aan telemetrie van de tijd reeks.
* Definieer eigenschappen die gekoppeld zijn aan het onderdeel exemplaren van *exemplaar velden* en gebruik deze om hiërarchieën te maken.

## <a name="entity-components"></a>Entiteit onderdelen

Time Series-modellen hebben drie kern onderdelen:

* <a href="#time-series-model-types">Typen Time Series-modellen</a>
* <a href="#time-series-model-hierarchies">Time Series-model hiërarchieën</a>
* <a href="#time-series-model-instances">Exemplaren van de time series-model</a>

Deze onderdelen worden gecombineerd om een time series-model op te geven en om uw Azure Time Series Insights gegevens te organiseren.

## <a name="time-series-model-types"></a>Typen Time Series-modellen

Met de *typen* tijd reeks modellen kunt u variabelen of formules voor het uitvoeren van berekeningen definiëren. De typen zijn gekoppeld aan een specifiek Time Series Insights exemplaar. Een type kan een of meer variabelen hebben. Een Time Series Insights-exemplaar kan bijvoorbeeld van het type *temperatuur sensor*zijn, dat bestaat uit de *gemiddelde Tempe ratuur*, de *minimale Tempe ratuur*en de *maximale Tempe ratuur*. Er wordt een standaard type gemaakt wanneer de gegevens naar Time Series Insights worden gestroomd. Het standaard type kan worden opgehaald en bijgewerkt vanuit model instellingen. Standaard typen hebben een variabele die het aantal gebeurtenissen telt.

### <a name="time-series-model-type-json-example"></a>JSON-voor beeld van type time series-model

Voorbeeld:

```JSON
{
    "name": "SampleType",
    "description": "This is sample type",
    "variables": {
        "Avg Temperature": {
            "kind": "numeric",
            "filter": null,
            "value": { "tsx": "$event.temperature.Double" },
            "aggregation": {"tsx": "avg($value)"}
        },
        "Count Temperature": {
            "kind": "aggregate",
            "filter": null,
            "value": null,
            "aggregation": {"tsx": "count()"}
        }
    }
}
```

Zie de [referentie documentatie](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api)voor meer informatie over de typen tijd reeks modellen.

### <a name="variables"></a>Variabelen

Time Series Insights typen bevatten variabelen, die de naam berekeningen hebben over waarden van de gebeurtenissen. Definities van Time Series Insights variabelen bevatten formule-en reken regels. Variabele definities bevatten *soort*, *waarde*, *filter*, *reductie*en *grenzen*. Variabelen worden opgeslagen in de type definitie in de time series-model en kunnen worden aangelegd via query-Api's om de opgeslagen definitie te overschrijven.

De volgende matrix werkt als een legenda voor variabele definities:

[definitie tabel ![Type](media/v2-update-tsm/table.png)](media/v2-update-tsm/table.png#lightbox)

| Definitie | Description |
| --- | ---|
| Variabele soort |  *Numerieke* en *statistische* typen worden ondersteund |
| Variabele filter | Variabele filters geven een optionele filter component op voor het beperken van het aantal rijen dat wordt overwogen voor de berekening op basis van voor waarden. |
| Waarde van variabele | Variabele waarden zijn en moeten worden gebruikt in de berekening. Het relevante veld waarnaar moet worden verwezen voor het betreffende gegevens punt. |
| Variabele aggregatie | De statistische functie van de variabele maakt deel uit van de berekening. Time Series Insights ondersteunt reguliere aggregaties (te weten, *min*, *Max*, *Gem*, *Sum*en *Count*). |

## <a name="time-series-model-hierarchies"></a>Time Series-model hiërarchieën

Hiërarchieën organiseren instanties door eigenschaps namen en de bijbehorende relaties op te geven. Mogelijk hebt u één of meerdere hiërarchieën. Ze hoeven geen actueel deel uit te maken van uw gegevens, maar elk exemplaar moet worden toegewezen aan een hiërarchie. Een exemplaar van een time series-model kan worden toegewezen aan een enkele of meerdere hiërarchieën.

Hiërarchieën worden gedefinieerd door de *hiërarchie-id*, *naam*en *bron*. Hiërarchieën hebben een pad, een bovenliggende en onderliggende volg orde van de hiërarchie die gebruikers willen maken. De bovenliggende/onderliggende eigenschappen van het toewijzings *exemplaar velden*.

### <a name="time-series-model-hierarchy-json-example"></a>JSON-voor beeld van Time Series model-hiërarchie

Voorbeeld:

```JSON
{
    "id": "4c6f1231-f632-4d6f-9b63-e366d04175e3",
    "name": "Location",
    "source": {
        "instanceFieldNames": [
                "state",
                "city"
            ]
    }
}
```

Zie de [referentie documentatie](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)voor meer informatie over time series model-hiërarchieën.

### <a name="hierarchy-definition-behavior"></a>Gedrag van de hiërarchie definitie

Houd rekening met het volgende voor beeld waarbij hiërarchie H1 *gebouw*, *vloer*en *ruimte* heeft gemaakt als onderdeel van de definitie:

```plaintext
 H1 = [“building”, “floor”, “room”]
```

Afhankelijk van de *instantie velden*, worden de hiërarchie kenmerken en-waarden weer gegeven, zoals wordt weer gegeven in de volgende tabel:

| Time Series-ID | Exemplaar velden |
| --- | --- |
| ID1 | ' buil ding ' = ' 1000 ', ' Floor ' = ' 10 ', ' room ' = ' 55 '  |
| ID2 | "buil ding" = "1000", "room" = "55" |
| ID3 | ' Floor ' = ' 10 ' |
| ID4 | ' buil ding ' = ' 1000 ', ' Floor ' = ' 10 '  |
| ID5 | Geen: ' buil ding ', ' Floor ' of ' room ' is ingesteld |

In het vorige voor beeld worden **id1** en **ID4** weer gegeven als onderdeel van Hierarchy H1 in de Azure time series Insights Explorer, en de rest worden geclassificeerd onder niet- *bovenliggende instanties* , omdat ze niet voldoen aan de opgegeven gegevens hiërarchie.

## <a name="time-series-model-instances"></a>Exemplaren van de time series-model

Exemplaren zijn de tijd reeks zelf. In de meeste gevallen is *deviceId* of *assetId* de unieke id van de asset in de omgeving. Instanties bevatten beschrijvende informatie die betrekking heeft op instantie-eigenschappen. Instantie-eigenschappen bevatten ten minste hiërarchie-informatie. Ze kunnen ook nuttige, beschrijvende gegevens bevatten, zoals de fabrikant, de operator of de laatste service datum.

Instanties worden gedefinieerd door *typeId*, *timeSeriesId*, *name*, *Description*, *hierarchyIds*en *instanceFields*. Elk exemplaar wordt toegewezen aan slechts één *type*en een of meer hiërarchieën. Instanties nemen alle eigenschappen van hiërarchieën over, en aanvullende *instanceFields* kunnen worden toegevoegd voor de definitie van een andere exemplaar-eigenschap.

*instanceFields* zijn eigenschappen van een exemplaar en alle statische gegevens die een exemplaar definiëren. Ze definiëren waarden van hiërarchie-of niet-hiërarchie-eigenschappen, terwijl indexeren ook ondersteuning biedt voor het uitvoeren van zoek bewerkingen.

De eigenschap *name* is optioneel en hoofdletter gevoelig. Als de *naam* niet beschikbaar is, wordt deze standaard ingesteld op de tijd reeks-id. Als er een *naam* wordt gegeven, is de time series-id nog steeds beschikbaar (het raster onder de grafieken in de Explorer).

### <a name="time-series-model-instance-json-example"></a>JSON-voor beeld van Time Series model-instantie

Voorbeeld:

```JSON
{
    "typeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
    "timeSeriesId": ["sampleTimeSeriesId"],
    "name": "sampleName",
    "description": "Sample Instance",
    "hierarchyIds": [
        "1643004c-0a84-48a5-80e5-7688c5ae9295"
    ],
    "instanceFields": {
        "state": "California",
        "city": "Los Angeles"
    }
}
```

Zie de [referentie documentatie](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api)voor meer informatie over time series-model instanties.

### <a name="time-series-model-settings-example"></a>Voor beeld van Time Series-model instellingen

Voorbeeld:

```JSON
{
    "modelSettings": {
        "name": "DefaultModel",
        "timeSeriesIdProperties": [
            {
                "name": "id",
                "type": "String"
            }
        ],
        "defaultTypeId": "1be09af9-f089-4d6b-9f0b-48018b5f7393"
    }
}
```

Zie de [referentie documentatie](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)voor meer informatie over de instellingen van het tijdreeks model.

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure time series Insights preview-opslag en](./time-series-insights-update-storage-ingress.md)inkomend verkeer.

- Bekijk het nieuwe [Time Series-model](https://docs.microsoft.com/rest/api/time-series-insights/preview-model).