---
title: Time Series-model-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over time series-model in Azure Time Series Insights preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 03/16/2020
ms.custom: seodec18
ms.openlocfilehash: 648578563a0e53d3ed5bda6ab47f85c3c6a2a24e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79476651"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Time Series-model in Azure Time Series Insights preview

In dit artikel wordt beschreven hoe u een tijdreeks model, de mogelijkheden en het maken en bijwerken van uw eigen modellen in de Azure Time Series Insights voorbeeld omgeving kunt beginnen.

> [!TIP]
>  * Ga naar de [Contoso wikkeling-Farm demo](https://insights.timeseries.azure.com/preview/samples) omgeving voor een live time series model-voor beeld.
> * Meer informatie over de [Azure time series Insights preview Explorer](time-series-insights-update-explorer.md) om te leren hoe u kunt navigeren in de gebruikers interface van uw tijd reeks model.
> * Meer informatie [over het werken met een time series-model](time-series-insights-update-how-to-tsm.md) met behulp van de time series Insights Web Explorer.

## <a name="summary"></a>Samenvatting

De gegevens die op IoT-apparaten worden verzameld, hebben doorgaans geen contextuele informatie, waardoor het lastig is om Sens oren snel te vinden en te analyseren. De belangrijkste motivatie voor time series-model is het vereenvoudigen van het vinden en analyseren van IoT-of time series-gegevens. Deze doel stelling wordt gerealiseerd door het inschakelen, onderhouds-en verrijken van tijdreeks gegevens in te scha kelen om voor bereide gegevens sets voor analyse te maken.

## <a name="scenario-contosos-new-smart-oven"></a>Scenario: de nieuwe Smart oven van contoso

**Bekijk het fictieve scenario van een slimme oven van contoso.** In dit scenario wordt ervan uitgesteld dat elke contoso-Smart oven vijf temperatuur Sens oren heeft, één voor elk van de vier meest meest gebranders en één voor de oven zelf. Tot onlangs is elke contoso-temperatuur sensor de gegevens afzonderlijk verzonden, opgeslagen en gevisualiseerd. Voor de bewaking van de keuken apparaten is contoso afhankelijk van eenvoudige grafieken, één voor elke afzonderlijke sensor.

Hoewel contoso tevreden was met de oorspronkelijke gegevens-en visualisatie oplossing, zijn er enkele beperkingen zichtbaar:

* Klanten wilden weten hoe heet de algehele oven wanneer het meren deel van de meeste branders was ingeschakeld. Contoso had problemen met het analyseren en presen teren van een uniform antwoord over de voor waarden van de algemene oven.
* Contoso-technici wilden controleren of de meest voorkomende branders die gelijktijdig worden uitgevoerd, niet kunnen leiden tot een inefficiënte stroom teken. Er is een probleem opgetreden bij het kruisen van de Tempe ratuur en spannings Sens oren en het vinden daarvan in de Store.
* Het contoso Quality Assurance-team wilde de geschiedenis tussen twee sensor versies controleren en vergelijken. Er is een probleem opgetreden bij het bepalen van de gegevens van de sensor versie.

Zonder de mogelijkheid om het time series-model van de overkoepelend-Smart oven te structureren, te organiseren en te definiëren, wordt elke temperatuur sensor die niet gevonden, geïsoleerd en minder informatieve gegevens punten bevindt, bewaard. Het is lastiger om deze gegevens punten in te scha kelen op inzichten omdat elke gegevensset onafhankelijk van de andere wordt bewaard.

Deze beperkingen onthulden het belang van slimme gegevens aggregatie en visualisatie tools om de nieuwe oven van Contoso te begeleiden:

* Gegevens visualisatie is handig wanneer u gegevens in een handige weer gave kunt koppelen en combi neren. Er wordt een voor beeld weer gegeven met spannings sensoren en temperatuur sensors.
* Het beheren van multidimensionale gegevens voor verschillende entiteiten, samen met de functies voor vergelijking, zoomen en tijds bereik kan moeilijk zijn.

**Time Series-model biedt een handige oplossing** voor veel van de scenario's die in dit fictieve voor beeld zijn aangetroffen:

[![Voor beeld van een Smart oven voor time series model](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* Time Series-model speelt een cruciale rol in query's en navigatie, omdat deze gegevens contextualizes, doordat er vergelijkingen kunnen worden gemaakt over Peri Oden en tussen sensor-en apparaattypen. (**A**) 
* Gegevens worden nader gecontextd, omdat de gegevens die in het tijdreeks model worden bewaard, de tijdreeks query berekeningen als variabelen behouden en ze opnieuw gebruiken op het moment van de query.
* Met de time series-model worden gegevens ingedeeld en geaggregeerd voor verbeterde visualisatie-en beheer mogelijkheden. (**B**) 

### <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Met het doel om eenvoudig en moeiteloos tijd Series contextualization te beheren, maakt time series model de volgende mogelijkheden in Time Series Insights preview. U kunt het volgende doen:

* Berekenings-of formules ontwerpen en beheren met scalaire functies, aggregatie bewerkingen, enzovoort.
* Definieer de relaties tussen bovenliggende en onderliggende items om navigatie, zoek acties en verwijzingen in te scha kelen.
* Definieer eigenschappen die gekoppeld zijn aan de instanties, gedefinieerd als *exemplaar velden*, en gebruik deze om hiërarchieën te maken.

### <a name="components"></a>Onderdelen

Time Series model heeft drie kern onderdelen:

* [Exemplaren van de time series-model](#time-series-model-instances)
* [Time Series-model hiërarchieën](#time-series-model-hierarchies)
* [Typen Time Series-modellen](#time-series-model-types)

Deze onderdelen worden gecombineerd om een time series-model op te geven en om uw Azure Time Series Insights gegevens te organiseren.

[![Overzichts grafiek voor tijdreeks model](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Een time series-model kan worden gemaakt en beheerd via de [Time Series Insights preview](time-series-insights-update-how-to-tsm.md) -interface. Instellingen voor tijdreeks modellen kunnen worden beheerd via de [API voor model instellingen](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="time-series-model-instances"></a>Exemplaren van de time series-model

De time series-model *instanties* zijn virtuele voors tellingen van de tijd reeks zelf.

In de meeste gevallen worden instanties uniek geïdentificeerd door **deviceId** of **assetId**, die worden opgeslagen als tijd reeks-id's.

Instanties bevatten beschrijvende informatie die betrekking heeft op *instantie-eigenschappen*, zoals een tijd reeks-id, type, naam, beschrijving, hiërarchieën en instantie velden. Instantie-eigenschappen bevatten ten minste hiërarchie-informatie.

*Exemplaar velden* zijn een verzameling beschrijvende gegevens die waarden voor hiërarchie niveaus kunnen bevatten, evenals de fabrikant, operator, enzovoort.

Nadat een gebeurtenis bron is geconfigureerd voor de Time Series Insights omgeving, worden exemplaren automatisch gedetecteerd en gemaakt in een time series-model. De exemplaren kunnen via de Time Series Insights Explorer worden gemaakt of bijgewerkt door gebruik te maken van Time Series-model query's.

De [demo](https://insights.timeseries.azure.com/preview/samples) van de contoso-Farm bevat enkele voor beelden van Live instances.

[![Voor beeld van een exemplaar van het time series-model](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Instantie-eigenschappen

Instanties worden gedefinieerd door **timeSeriesId**, **typeId**, **name**, **Description**, **hierarchyIds**en **instanceFields**. Elk exemplaar wordt toegewezen aan slechts één *type*en een of meer *hiërarchieën*.

| Eigenschap | Beschrijving |
| --- | ---|
| timeSeriesId | De UUID van de tijd reeks waaraan het exemplaar is gekoppeld. |
| typeId | De UUID van het model van de tijd reeks waaraan het exemplaar is gekoppeld. Alle gedetecteerde nieuwe instanties worden standaard gekoppeld aan een standaard type.
| name | De eigenschap **name** is optioneel en hoofdletter gevoelig. Als de **naam** niet beschikbaar is, wordt standaard **timeSeriesId**. Als er een naam wordt gegeven, is **timeSeriesId** nog steeds beschikbaar [.](time-series-insights-update-explorer.md#4-time-series-well) |
| description | Een tekst beschrijving van het exemplaar. |
| hierarchyIds | Hiermee definieert u de hiërarchieën waarvan het exemplaar deel uitmaakt. |
| instanceFields | De eigenschappen van een exemplaar en alle statische gegevens waarmee een exemplaar wordt gedefinieerd. Ze definiëren waarden van hiërarchie-of niet-hiërarchie-eigenschappen, terwijl indexeren ook ondersteuning biedt voor het uitvoeren van zoek bewerkingen. |

> [!NOTE]
> Hiërarchieën worden gebouwd met behulp van exemplaar velden. Aanvullende **instanceFields** kunnen worden toegevoegd voor de verdere definities van instantie-eigenschappen.

Exemplaren hebben de volgende JSON-weer gave:

```JSON
{
  "timeSeriesId": ["PU2"],
  "typeId": "545314a5-7166-4b90-abb9-fd93966fa39b",
  "hierarchyIds": ["95f0a8d1-a3ef-4549-b4b3-f138856b3a12"],
  "description": "Pump #2",
  "instanceFields": {
    "Location": "Redmond",
    "Fleet": "Fleet 5",
    "Unit": "Pump Unit 3",
    "Manufacturer": "Contoso",
    "ScalePres": "0.54",
    "scaleTemp": "0.54"
  }
}
```

> [!TIP]
> Lees voor de API voor Time Series Insights-api's en het maken, lezen, bijwerken en verwijderen (ruwe) ondersteuning, het [gegevens query](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) -artikel en de [API-documentatie](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api)van het exemplaar.

## <a name="time-series-model-hierarchies"></a>Time Series-model hiërarchieën

Met *hiërarchieën* voor time series-modellen worden instanties geordend door eigenschapnamen en hun relaties op te geven.

U kunt meerdere hiërarchieën configureren in een bepaalde Time Series Insights omgeving. Een exemplaar van een time series-model kan worden toegewezen aan een enkele of meerdere hiërarchieën (veel-op-veel-relatie).

De [Contoso wikkeling-Farm demo](https://insights.timeseries.azure.com/preview/samples) client interface geeft een standaard exemplaar en een type hiërarchie weer.

[![Voor beeld van hiërarchie van Time Series-model](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Hiërarchie definitie

Hiërarchieën worden gedefinieerd door de hiërarchie **-id**, **naam**en **bron**.

| Eigenschap | Beschrijving |
| ---| ---|
| id | De unieke id voor de hiërarchie die wordt gebruikt, bijvoorbeeld wanneer u een exemplaar definieert. |
| name | Een teken reeks die wordt gebruikt om een naam op te geven voor de hiërarchie. |
| source | Hiermee geeft u de organisatie hiërarchie of het pad op. Dit is een bovenliggende en onderliggende volg orde van de hiërarchie die gebruikers willen maken. De bovenliggende/onderliggende eigenschappen van het toewijzings exemplaar velden. |

Hiërarchieën worden weer gegeven in JSON als:

```JSON
{
  "hierarchies": [
    {
      "id": "6e292e54-9a26-4be1-9034-607d71492707",
      "name": "Location",
      "source": {
        "instanceFieldNames": [
          "state",
          "city"
        ]
      }
    },
    {
      "id": "a28fd14c-6b98-4ab5-9301-3840f142d30e",
      "name": "ManufactureDate",
      "source": {
        "instanceFieldNames": [
          "year",
          "month"
        ]
      }
    }
  ]
}
```

In het vorige voor beeld van JSON:

* `Location`Hiermee definieert u een hiërarchie `states` met bovenliggend `cities`en onderliggend item. Elk `location` kan meerdere `states`hebben, die op zijn beurt meerdere `cities`kunnen bevatten.
* `ManufactureDate`Hiermee definieert u een hiërarchie `year` met bovenliggend `month`en onderliggend item. Elk `ManufactureDate` kan meerdere `years`hebben, die op zijn beurt meerdere `months`kunnen bevatten.

> [!TIP]
> Lees voor Time Series Insights instance API en ruwe ondersteuning het artikel [gegevens query's](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) en de documentatie van de [hiërarchie-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api).

### <a name="hierarchy-example"></a>Hiërarchie-voor beeld

Bekijk een voor beeld waarin **H1** hiërarchie H1 `building`, `floor`en `room` als onderdeel van de **instanceFieldNames** -definitie:

```JSON
{
  "id": "aaaaaa-bbbbb-ccccc-ddddd-111111",
  "name": "H1",
  "source": {
    "instanceFieldNames": [
      "building",
      "floor",
      "room"
    ]
  }
}
```

Op basis van de instantie velden die worden gebruikt in de vorige definitie en een aantal Peri Series, worden de hiërarchie kenmerken en-waarden weer gegeven, zoals wordt weer gegeven in de volgende tabel:

| Time Series-ID | Exemplaar velden |
| --- | --- |
| ID1 | ' buil ding ' = ' 1000 ', ' Floor ' = ' 10 ', ' room ' = ' 55 '  |
| ID2 | "buil ding" = "1000", "room" = "55" |
| ID3 | ' Floor ' = ' 10 ' |
| ID4 | ' buil ding ' = ' 1000 ', ' Floor ' = ' 10 '  |
| ID5 | Geen: ' buil ding ', ' Floor ' of ' room ' is ingesteld. |

Time Series **id1** en **ID4** worden weer gegeven als onderdeel van de hiërarchie **H1** in de [Azure time series Insights Explorer](time-series-insights-update-explorer.md) , omdat ze volledig gedefinieerde en correct bestelde *bouw*-, *vloer*-en *room* -para meters hebben gedefinieerd.

De andere zijn ingedeeld onder niet- *bovenliggende instanties* , omdat ze niet voldoen aan de opgegeven gegevens hiërarchie.

## <a name="time-series-model-types"></a>Typen Time Series-modellen

Met de *typen* tijd reeks modellen kunt u variabelen of formules voor het uitvoeren van berekeningen definiëren. Typen zijn gekoppeld aan een specifiek Time Series Insights exemplaar.

Een type kan een of meer variabelen hebben. Een exemplaar van een time series-model kan bijvoorbeeld van het type *temperatuur sensor*zijn, dat bestaat uit de *gemiddelde Tempe ratuur*, *minimale Tempe ratuur*en de *maximale Tempe ratuur*.

In de demo van de [Contoso wikkeling-Farm](https://insights.timeseries.azure.com/preview/samples) worden verschillende typen Time Series-modellen gevisualiseerd die aan hun respectieve instanties zijn gekoppeld.

[![Voor beeld van type time series-model](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> Lees voor Time Series Insights instance API en ruwe ondersteuning het artikel [gegevens query's](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) en de rest- [documentatie van het type API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Type-eigenschappen

De typen van de tijd reeks model worden gedefinieerd op basis van **id**, **naam**, **Beschrijving**en **variabelen**.

| Eigenschap | Beschrijving |
| ---| ---|
| id | De UUID voor het type. |
| name | Een teken reeks die wordt gebruikt om een naam op te geven voor het type. |
| description | Een beschrijving van de teken reeks voor het type. |
| variabelen | Geef de variabelen op die zijn gekoppeld aan het type. |

Typen voldoen aan het volgende JSON-voor beeld:

```JSON
{
  "types": [
    {
      "id": "1be09af9-f089-4d6b-9f0b-48018b5f7393",
      "name": "DefaultType",
      "description": "Default type",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "value": null,
          "filter": null,
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Interpolated Speed": {
          "kind": "numeric",
          "value": {
              "tsx": "$event.[speed].Double"
          },
          "filter": null,
          "interpolation": {
              "kind": "step",
              "boundary": {
                  "span": "P1D"
              }
          },
          "aggregation": {
              "tsx": "left($value)"
          }
        }
      }
    }
  ]
}
```

### <a name="variables"></a>Variabelen

Time Series Insights typen kunnen veel variabelen hebben waarmee formule-en reken regels voor gebeurtenissen worden opgegeven.

Elke variabele kan een van de volgende drie *typen*zijn: *numeric*, *categorische*en *aggregatie*.

* **Numerieke** soorten werken met doorlopende waarden. 
* **Categorische** -soorten werken met een gedefinieerde set discrete waarden.
* **Aggregatie** waarden combi neren meerdere variabelen van één soort (ofwel alle numerieke of alle categorische).

In de volgende tabel ziet u welke eigenschappen relevant zijn voor elk type variabele.

[![Tabel met variabele Time Series-modellen](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Numerieke variabelen

| Eigenschap variable | Beschrijving |
| --- | ---|
| Variabele filter | Filters zijn optionele voorwaardelijke componenten voor het beperken van het aantal rijen dat wordt overwogen voor berekening. |
| Waarde van variabele | Telemetrie-waarden die worden gebruikt voor berekeningen die afkomstig zijn van het apparaat of Sens oren of worden getransformeerd met behulp van Time Series-expressies. De variabelen van een numeriek type moeten van het type *Double*zijn.|
| Variabele interpolatie | Interpolatie Hiermee geeft u op hoe een signaal opnieuw moet worden samengesteld met behulp van bestaande gegevens. Opties voor *stap* -en *lineaire* interpolatie zijn beschikbaar voor numerieke variabelen. |
| Variabele aggregatie | Ondersteunings berekeningen met de Opera tors *Gem*, *min*, *Max*, *som*, *aantal*, *eerste*, *laatste* en tijd gewogen (*Gem*, *min*, *Max*, *Sum*, *Left*). |

Variabelen voldoen aan het volgende JSON-voor beeld:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event.[speed].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "left($value)"
  }
}
```

#### <a name="categorical-variables"></a>Categorische variabelen

| Eigenschap variable | Beschrijving |
| --- | ---|
| Variabele filter | Filters zijn optionele voorwaardelijke componenten voor het beperken van het aantal rijen dat wordt overwogen voor berekening. |
| Waarde van variabele | Telemetrie-waarden die worden gebruikt voor de berekening die afkomstig zijn van het apparaat of de Sens oren. Categorische-variabelen moeten een *Long-waarde* of een *teken reeks*zijn. |
| Variabele interpolatie | Interpolatie Hiermee geeft u op hoe een signaal opnieuw moet worden samengesteld met behulp van bestaande gegevens. De optie interpolatie van de *stap* is beschikbaar voor Categorische-variabelen. |
| Variabelen Categorieën | Categorieën maken een toewijzing tussen de waarden die afkomstig zijn van het apparaat of Sens oren aan een label. |
| Variabele standaard categorie | De standaard categorie is voor alle waarden die niet worden toegewezen in de eigenschap ' categories '. |

Variabelen voldoen aan het volgende JSON-voor beeld:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "toLong($event.[Status].Double)"
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2],
      "label": "Good"
    },
    {
      "values": [3],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### <a name="aggregate-variables"></a>Aggregatie variabelen

| Eigenschap variable | Beschrijving |
| --- | ---|
| Variabele filter | Filters zijn optionele voorwaardelijke componenten voor het beperken van het aantal rijen dat wordt overwogen voor berekening. |
| Variabele aggregatie | Ondersteunings berekening door *Gem*, *min*, *Max*, *Sum*, *aantal*, *First*, *last*. |

Variabelen voldoen aan het volgende JSON-voor beeld:

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

Variabelen worden opgeslagen in de type definitie van een time series-model en kunnen worden aangelegd via [query-api's](time-series-insights-update-tsq.md) om de opgeslagen definitie te overschrijven.

## <a name="next-steps"></a>Volgende stappen

- Lees [Azure time series Insights voor beeld-opslag en](./time-series-insights-update-storage-ingress.md)-inkomend verkeer.

- Meer informatie over algemene bewerkingen voor time series-modellen in [gegevens modellering in azure time series Insights preview](./time-series-insights-update-how-to-tsm.md)

- Lees de naslag documentatie over het nieuwe [Time Series-model](https://docs.microsoft.com/rest/api/time-series-insights/preview-model) .
