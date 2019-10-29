---
title: Time Series-model in Azure Time Series Insights preview | Microsoft Docs
description: Meer informatie over Azure Time Series Insights time series-model.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/21/2019
ms.custom: seodec18
ms.openlocfilehash: 802fd444c953c69dfa99fbd49fdf9541cda372ba
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989832"
---
# <a name="time-series-model"></a>Time Series-model

In dit document worden de tijdreeks modellen en de mogelijkheden ervan beschreven en wordt uitgelegd hoe u uw eigen kunt bouwen en bijwerken in Time Series Insights omgeving.

> [!TIP]
>  * Ga naar de [Contoso wikkeling-Farm demo](https://insights.timeseries.azure.com/preview/samples) omgeving voor een live time series model-voor beeld.
> * Meer informatie over de [Time Series Insights preview Explorer](time-series-insights-update-explorer.md) om te leren hoe u kunt navigeren in de gebruikers interface van uw tijd reeks model.

## <a name="summary"></a>Samenvatting

De gegevens die op IoT-apparaten worden verzameld, hebben doorgaans geen contextuele informatie, waardoor het lastig is om Sens oren snel te vinden en te analyseren. De belangrijkste motivatie voor time series-model is het vereenvoudigen van het vinden en analyseren van IoT/time series-gegevens. Deze doel stelling wordt gerealiseerd door het inschakelen, onderhouds-en verrijken van tijdreeks gegevens in te scha kelen om voor bereide gegevens sets voor analyse te maken.

## <a name="scenario-contosos-new-smart-oven"></a>Scenario: de nieuwe Smart oven van contoso

**Bekijk het fictieve scenario van een nieuwe contoso-Smart oven.** In dit scenario wordt ervan uitgesteld dat elke contoso-Smart oven vijf temperatuur Sens oren heeft, één voor elk van de vier meest meest gebranders en één voor de oven zelf. Tot onlangs is elke contoso-temperatuur sensor de gegevens afzonderlijk verzonden, opgeslagen en gevisualiseerd. Voor de bewaking van de keuken apparaten is contoso afhankelijk van eenvoudige grafieken, één voor elke afzonderlijke sensor.

Hoewel contoso tevreden was met hun oorspronkelijke gegevens-en visualisatie-oplossing, werd een aantal beperkingen duidelijk:

* Klanten wilden weten hoe heet de algehele oven zou halen wanneer het meren deel van de meeste branders was ingeschakeld, en contoso had moeite met het analyseren en presen teren van een uniform antwoord over de voor waarden van de totale oven.
* Contoso-technici wilden controleren of de meest voorkomende branders gelijktijdig worden uitgevoerd, zouden niet kunnen leiden tot een inefficiënte stroom teken en er problemen ontstaan met het kruisen van de Tempe ratuur en spannings Sens oren en hoe deze kunnen worden gevonden in de Store.
* Het contoso Quality Assurance-team wilde de geschiedenis tussen twee sensor versies controleren en vergelijken, en er was problemen met het bepalen van de gegevens die bij welke sensor versie horen.

Zonder de mogelijkheid om het time series-model van de overkoepelend-Smart oven te structureren, te organiseren en te definiëren, wordt elke temperatuur sensor die niet gevonden, geïsoleerd en minder informatieve gegevens punten bevindt, bewaard. Het kan lastiger zijn om deze gegevens punten in te stellen op inzichten omdat elke gegevensset onafhankelijk van de andere gegevens sets werd bewaard.

Deze beperkingen onthulden het belang van slimme gegevens aggregatie en visualisatie tools om de nieuwe oven van Contoso te begeleiden:

* Gegevens visualisatie is handig wanneer u gegevens in een handige weer gave kunt koppelen en combi neren. Bijvoorbeeld, met spannings sensoren samen met de temperatuur sensors.
* Het beheren van multi-dimensionale gegevens voor verschillende entiteiten, samen met de functies voor vergelijking, zoomen en tijds bereik kan moeilijk zijn.

**Time Series-model biedt een handige oplossing** voor veel van de scenario's die zijn aangetroffen in het fictieve voor beeld hierboven:

[grafieken van![time series-model](media/v2-update-tsm/tsi-charting.png)](media/v2-update-tsm/tsi-charting.png#lightbox)

* Time Series-model speelt een cruciale rol in query's en navigatie, omdat deze gegevens contextualizes, doordat er vergelijkingen kunnen worden gemaakt over Peri Oden en tussen sensor-en apparaattypen.
* De gegevens worden verder gecontextd omdat de gegevens die in een time series-model worden bewaard, de tijdreeks query berekeningen als variabelen behouden en deze tijdens de query tijd gebruiken.
* Time Series-model organiseert en verzamelt gegevens voor verbeterde visualisatie-en beheer mogelijkheden.

### <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Met het doel om eenvoudig en moeiteloos tijd Series contextualization te beheren, maakt time series model de volgende mogelijkheden in Time Series Insights preview. U kunt het volgende doen:

* Berekenings-of formules ontwerpen en beheren met scalaire functies, aggregatie bewerkingen, enzovoort.
* Definieer de relaties tussen bovenliggende en onderliggende items om navigatie, zoek acties en verwijzingen in te scha kelen.
* Definieer eigenschappen die zijn gekoppeld aan de instanties, gedefinieerd als *exemplaar velden* en gebruik deze om hiërarchieën te maken.

### <a name="components"></a>Onderdelen

Time Series model heeft drie kern onderdelen:

* [Exemplaren van de time series-model](#time-series-model-instances)
* [Time Series-model hiërarchieën](#time-series-model-hierarchies)
* [Typen Time Series-modellen](#time-series-model-types)

Deze onderdelen worden gecombineerd om een time series-model op te geven en om uw Azure Time Series Insights gegevens te organiseren.

[overzicht van![time series-model](media/v2-update-tsm/tsm.png)](media/v2-update-tsm/tsm.png#lightbox)

Time Series-model kan worden gemaakt en beheerd via de [Time Series Insights preview](time-series-insights-update-how-to-tsm.md) -interface. Instellingen voor tijdreeks modellen kunnen worden beheerd via de [API voor model instellingen](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api).

## <a name="time-series-model-instances"></a>Exemplaren van de time series-model

De time series-model *instanties* zijn virtuele voors tellingen van de tijd reeks zelf.

In de meeste gevallen worden instanties uniek geïdentificeerd door de **deviceId** of **assetId**. deze worden opgeslagen als tijd reeks-id's.

Instanties bevatten beschrijvende informatie die is gekoppeld aan *instantie-eigenschappen* , zoals een tijd reeks-id, type, naam, beschrijving, hiërarchieën en instantie velden. Instantie-eigenschappen bevatten ten minste hiërarchie-informatie.

Exemplaar velden zijn een verzameling beschrijvende gegevens die waarden voor hiërarchie niveaus kunnen bevatten, evenals de fabrikant, operator, enzovoort.

Zodra een gebeurtenis bron is geconfigureerd voor Time Series Insights omgeving, worden exemplaren automatisch gedetecteerd en gemaakt in het time series-model. De exemplaren kunnen via Time Series Insights Explorer worden gemaakt of bijgewerkt met behulp van Time Series-model Query's.

De [demo](https://insights.timeseries.azure.com/preview/samples) van de contoso-Farm bevat enkele voor beelden van Live instances.

[![time series-model instanties](media/v2-update-tsm/instance.png)](media/v2-update-tsm/instance.png#lightbox)

### <a name="instance-properties"></a>Instantie-eigenschappen

Instanties worden gedefinieerd door **timeSeriesId**, **typeId**, **name**, **Description**, **hierarchyIds**en **instanceFields**. Elk exemplaar wordt toegewezen aan slechts één *type*en een of meer *hiërarchieën*.

| Eigenschap | Beschrijving |
| --- | ---|
| timeSeriesId | De UUID van de tijd reeks waaraan het exemplaar is gekoppeld. |
| typeId | De UUID van het model van de tijd reeks waaraan het exemplaar is gekoppeld. Alle gedetecteerde nieuwe instanties worden standaard gekoppeld aan een standaard type|
| name | De eigenschap *name* is optioneel en hoofdletter gevoelig. Als de *naam* niet beschikbaar is, wordt de standaard waarde voor de *timeSeriesId*. Als er een *naam* wordt gegeven, is de *timeSeriesId* nog steeds [beschikbaar.](time-series-insights-update-explorer.md#preview-well) |
| description | Een tekst beschrijving van het exemplaar. |
| hierarchyIds | Hiermee definieert u de hiërarchieën waarvan het exemplaar deel uitmaakt. |
| instanceFields | *instanceFields* zijn eigenschappen van een exemplaar en alle statische gegevens die een exemplaar definiëren. Ze definiëren waarden van hiërarchie-of niet-hiërarchie-eigenschappen, terwijl indexeren ook ondersteuning biedt voor het uitvoeren van zoek bewerkingen. |

> [!NOTE]
> Hiërarchieën worden gebouwd met behulp van **exemplaar velden**, en aanvullende **instanceFields** kunnen worden toegevoegd voor de definitie van een andere instantie-eigenschap.

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
> Raadpleeg het artikel over het [uitvoeren van gegevens query's](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) en de versie van de reacties- [API-documentatie](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api)voor time series INSIGHTS-API en ruwe ondersteuning

## <a name="time-series-model-hierarchies"></a>Time Series-model hiërarchieën

Met *hiërarchieën* voor time series-modellen worden instanties geordend door eigenschapnamen en hun relaties op te geven.

U kunt meerdere hiërarchieën configureren in een bepaalde Time Series Insights omgeving. Een exemplaar van een time series-model kan worden toegewezen aan een enkele of meerdere hiërarchieën (veel-op-veel-relatie).

De [Contoso wikkeling-Farm demo](https://insights.timeseries.azure.com/preview/samples) client interface geeft een standaard exemplaar en een type hiërarchie weer.

[![time series-model hiërarchieën](media/v2-update-tsm/hierarchy.png)](media/v2-update-tsm/hierarchy.png#lightbox)

### <a name="hierarchy-definition"></a>Hiërarchie definitie

Hiërarchieën worden gedefinieerd door de hiërarchie **-id**, **naam**en **bron**.

| Eigenschap | Beschrijving |
| ---| ---|
| id | De unieke id voor de hiërarchie die wordt gebruikt, bijvoorbeeld bij het definiëren van een exemplaar. |
| name | Een teken reeks die wordt gebruikt om een naam op te geven voor de hiërarchie. |
| source | Hiermee geeft u de organisatie hiërarchie of het pad op. Dit is een bovenliggende en onderliggende volg orde van de hiërarchie die gebruikers willen maken. De bovenliggende/onderliggende eigenschappen van het toewijzings *exemplaar velden*. |

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

Waarboven

* `Location` definieert een hiërarchie met bovenliggende `states` en onderliggende `cities`. Elke `location` kan meerdere `states` hebben die op zijn beurt meerdere `cities`kunnen hebben.
* `ManufactureDate` definieert een hiërarchie met bovenliggende `year` en onderliggende `month`. Elke `ManufactureDate` kan meerdere `years` hebben die op zijn beurt meerdere `months`kunnen hebben.

> [!TIP]
> Raadpleeg het artikel voor [gegevens query's](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) en de documentatie van de [hiërarchie-API](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)voor time series INSIGHTS-instance-API en ruwe ondersteuning.

### <a name="hierarchy-example"></a>Hiërarchie-voor beeld

Bekijk een voor beeld waarin hiërarchie H1 *gebouw*, *vloer*en *ruimte* heeft gemaakt als onderdeel van de **instanceFieldNames** -definitie:

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

Op basis van de **instantie velden** die worden gebruikt in de bovenstaande definitie en een aantal Peri Series, worden de hiërarchie kenmerken en-waarden weer gegeven, zoals wordt weer gegeven in de volgende tabel:

| Time Series-ID | Exemplaar velden |
| --- | --- |
| ID1 | ' buil ding ' = ' 1000 ', ' Floor ' = ' 10 ', ' room ' = ' 55 '  |
| ID2 | "buil ding" = "1000", "room" = "55" |
| ID3 | ' Floor ' = ' 10 ' |
| ID4 | ' buil ding ' = ' 1000 ', ' Floor ' = ' 10 '  |
| ID5 | Geen: ' buil ding ', ' Floor ' of ' room ' is ingesteld |

Time Series **id1** en **ID4** worden weer gegeven als onderdeel van de hiërarchie **H1** in [de Azure time series Insights Explorer](time-series-insights-update-explorer.md) , omdat ze volledig zijn gedefinieerd en goed *zijn geordend,* *vloer*en *kamer* instellen.

De andere worden onder niet- *bovenliggende instanties* geclassificeerd, omdat ze niet voldoen aan de opgegeven gegevens hiërarchie.

## <a name="time-series-model-types"></a>Typen Time Series-modellen

Met de *typen* tijd reeks modellen kunt u variabelen of formules voor het uitvoeren van berekeningen definiëren. Typen zijn gekoppeld aan een specifiek Time Series Insights exemplaar.

Een type kan een of meer variabelen hebben. Een exemplaar van een time series-model kan bijvoorbeeld van het type *temperatuur sensor*zijn, dat bestaat uit de *gemiddelde Tempe ratuur*, *minimale Tempe ratuur*en de *maximale Tempe ratuur*.

In de demo van de [Contoso wikkeling-Farm](https://insights.timeseries.azure.com/preview/samples) worden verschillende typen Time Series-modellen gevisualiseerd die aan hun respectieve instanties zijn gekoppeld.

[![time series-model typen](media/v2-update-tsm/types.png)](media/v2-update-tsm/types.png#lightbox)

> [!TIP]
> Raadpleeg het artikel voor [gegevens query's](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) en de [informatie over het type API-rest](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api)voor time series INSIGHTS-instance-API en ruwe ondersteuning.

### <a name="type-properties"></a>Type-eigenschappen

De typen van de tijd reeks model worden gedefinieerd door een **id**, **naam**, **Beschrijving**en **variabelen**.

| Eigenschap | Beschrijving |
| ---| ---|
| id | De UUID voor het type. |
| name | Een teken reeks die wordt gebruikt om een naam op te geven voor het type. |
| description | Een beschrijving van de teken reeks voor het type. |
| Variabelen | Geef de variabelen op die zijn gekoppeld aan het type. |

Typen komen overeen met het volgende JSON-voor beeld:

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

Time Series Insights typen kunnen veel variabelen bevatten die formule-en verwerkings regels op gebeurtenissen opgeven.

Elke variabele kan een van de volgende drie *typen*zijn: *numeric*, *categorische*en *aggregatie*.

* *Numerieke* soorten werken met doorlopende waarden. 
* *Categorische* -soorten werken met een gedefinieerde set discrete waarden.
* *Aggregatie* waarden combi neren meerdere variabelen van één soort (ofwel alle *numerieke* of alle *categorische*).

In de volgende tabel ziet u welke eigenschappen relevant zijn voor elk type variabele.

[![time series-model typen](media/v2-update-tsm/variable-table.png)](media/v2-update-tsm/variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Numerieke variabelen

| Eigenschap variable | Beschrijving |
| --- | ---|
| Variabele filter | Filters zijn optionele voorwaardelijke componenten voor het beperken van het aantal rijen dat wordt overwogen voor berekening. |
| Waarde van variabele | Telemetrie-waarden die worden gebruikt voor de berekening van het apparaat of Sens oren of die zijn getransformeerd met behulp van Time Series-expressies. De variabelen van een numeriek type moeten van het type *Double*zijn.|
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
| Waarde van variabele | Telemetrie-waarden die worden gebruikt voor de berekening van het apparaat/de Sens oren. Categorische-variabelen moeten een *Long-waarde* of een *teken reeks*zijn. |
| Variabele interpolatie | Interpolatie Hiermee geeft u op hoe een signaal opnieuw moet worden samengesteld met behulp van bestaande gegevens. Optie voor interpolatie van *stap* is beschikbaar voor Categorische-variabelen. |
| Variabelen Categorieën | Categorieën maken een toewijzing tussen de waarden die afkomstig zijn van het apparaat/de Sens oren aan een label. |
| Variabele standaard categorie | De standaard categorie is voor alle waarden die niet worden toegewezen in de eigenschap ' categories '. |

Variabelen voldoen aan het volgende JSON-voor beeld:

```JSON
"Status": {
  "kind": "categorical",
  "value": "toLong($event.[Status].Double)",
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
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

- Zie [Azure time series Insights preview-opslag en](./time-series-insights-update-storage-ingress.md)inkomend verkeer.

- Meer informatie over algemene bewerkingen voor time series-modellen in [gegevens modellering in azure time series Insights preview](./time-series-insights-update-how-to-tsm.md)

- Lees de naslag documentatie over het nieuwe [Time Series-model](https://docs.microsoft.com/rest/api/time-series-insights/preview-model) .
