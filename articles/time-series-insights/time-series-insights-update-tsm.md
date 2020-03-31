---
title: Time Series-model - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over timeseries model in Azure Time Series Insights Preview.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476651"
---
# <a name="time-series-model-in-azure-time-series-insights-preview"></a>Time Series-model in de preview-proefversie van Azure Time Series Insights

In dit artikel worden timeseries model, de mogelijkheden en hoe u beginnen met het bouwen en bijwerken van uw eigen modellen in de Azure Time Series Insights Preview-omgeving.

> [!TIP]
>  * Ga naar de [Contoso Wind Farm demo](https://insights.timeseries.azure.com/preview/samples) omgeving voor een live Time Series Model voorbeeld.
> * Lees meer over de [Azure Time Series Insights Preview-verkenner](time-series-insights-update-explorer.md) voor meer informatie over het navigeren door de gebruikersinterface van het tijdreeksmodel.
> * Meer informatie over [het werken met Time Series Model](time-series-insights-update-how-to-tsm.md) met behulp van de Time Series Insights web explorer.

## <a name="summary"></a>Samenvatting

Traditioneel missen de gegevens die worden verzameld van IoT-apparaten contextuele informatie, waardoor het moeilijk is om sensoren snel te vinden en te analyseren. De belangrijkste motivatie voor Time Series Model is het vereenvoudigen van het vinden en analyseren van IoT- of Time Series-gegevens. Het bereikt dit doel door de curatie, het onderhoud en de verrijking van tijdreeksgegevens mogelijk te maken om gegevens die klaar zijn voor de consument voor analyses voor te bereiden.

## <a name="scenario-contosos-new-smart-oven"></a>Scenario: Contoso's nieuwe slimme oven

**Denk aan het fictieve scenario van een Contoso slimme oven.** In dit scenario, stel dat elke Contoso slimme oven heeft vijf temperatuursensoren, een voor elk van de vier bovenste branders en een voor de oven zelf. Tot voor kort stuurde, bewaarde en visualiseerde elke Contoso-temperatuursensor zijn gegevens afzonderlijk. Voor de bewaking van het keukenapparaat baseerde Contoso zich op basiskaarten, één voor elke individuele sensor.

Terwijl Contoso tevreden was met de oorspronkelijke gegevens en visualisatie oplossing, werden verschillende beperkingen duidelijk:

* Klanten wilden weten hoe warm de totale oven zou krijgen wanneer de meeste van de top branders waren op. Contoso had meer moeite met het analyseren en presenteren van een uniform antwoord over de voorwaarden van de totale oven.
* Contoso ingenieurs wilden controleren of de bovenste branders gelijktijdig worden uitgevoerd niet zou resulteren in inefficiënte power draw. Er was moeite met kruisverwijzingen welke temperatuur- en spanningssensoren met elkaar werden geassocieerd en hoe ze in de winkel te lokaliseren.
* Het Contoso quality assurance team wilde de geschiedenis tussen twee sensorversies controleren en vergelijken. Er was moeite om te bepalen welke gegevens tot welke sensorversie behoorden.

Zonder de mogelijkheid om het overkoepelende model van de slimme oventijdreeks te structureren, te organiseren en te definiëren, blijft elke temperatuursensor ontwricht, geïsoleerd en minder informatieve gegevenspunten behouden. Het omzetten van deze gegevenspunten in bruikbare inzichten was moeilijker omdat elke dataset onafhankelijk van de anderen leefde.

Deze beperkingen toonden het belang aan van slimme dataaggregatie- en visualisatietools om de nieuwe oven van Contoso te begeleiden:

* Gegevensvisualisatie is handig wanneer u gegevens koppelen en combineren in een handige weergave. Een voorbeeld is het tonen van spanningssensoren samen met temperatuursensoren.
* Het beheren van multidimensionale gegevens voor verschillende entiteiten, samen met vergelijkings-, zoom- en tijdbereikfunctionaliteiten, kan moeilijk te bereiken zijn.

**Time Series Model biedt een handige oplossing** voor veel van de scenario's die in dit fictieve voorbeeld:

[![Time Series Model slimme oven grafieken voorbeeld](media/v2-update-tsm/time-series-model-smart-oven.png)](media/v2-update-tsm/time-series-model-smart-oven.png#lightbox)

* Time Series Model speelt een cruciale rol in query's en navigatie, omdat het contextualiseert gegevens door dat vergelijkingen worden getrokken over tijdsafstanden en tussen sensor en apparaat soorten. (**A**) 
* Gegevens worden verder gecontextualiseerd omdat gegevens die in Time Series Model blijven bestaan, queryberekeningen van tijdreeksen als variabelen behouden en deze opnieuw gebruiken tijdens querytijd.
* Time Series Model organiseert en verzamelt gegevens voor verbeterde visualisatie- en beheermogelijkheden. (**B**) 

### <a name="key-capabilities"></a>Belangrijkste mogelijkheden

Met als doel het eenvoudig en moeiteloos te maken om contextuele tijdreeksen te beheren, maakt Time Series Model de volgende mogelijkheden mogelijk in Time Series Insights Preview. Het helpt je:

* Schrijf en beheer berekeningen of formules die gebruikmaken van scalaire functies, geaggregeerde bewerkingen, enzovoort.
* Definieer bovenliggende-onderliggende relaties om navigatie, zoeken en verwijzing in te schakelen.
* Definieer eigenschappen die zijn gekoppeld aan de instanties, gedefinieerd als *instantievelden*en gebruik ze om hiërarchieën te maken.

### <a name="components"></a>Onderdelen

Time Series Model heeft drie kerncomponenten:

* [Time Series Model-exemplaren](#time-series-model-instances)
* [Hiërarchieën van tijdreeksmodellen](#time-series-model-hierarchies)
* [Modeltypen tijdreeksen](#time-series-model-types)

Deze componenten worden gecombineerd om een tijdreeksmodel op te geven en uw Azure Time Series Insights-gegevens te ordenen.

[![Overzichtsdiagram tijdreeksmodel](media/v2-update-tsm/time-series-model-overview.png)](media/v2-update-tsm/time-series-model-overview.png#lightbox)

Een tijdreeksmodel kan worden gemaakt en beheerd via de [Interface van Time Series Insights Preview.](time-series-insights-update-how-to-tsm.md) Instellingen voor tijdreeksmodellen kunnen worden beheerd via de [API voor modelinstellingen.](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#model-settings-api)

## <a name="time-series-model-instances"></a>Time Series Model-exemplaren

Time Series Model *exemplaren* zijn virtuele voorstellingen van de tijdreekszelf.

In de meeste gevallen worden exemplaren uniek geïdentificeerd door **deviceId** of **assetId**, die worden opgeslagen als tijdreeks-id's.

Instanties bevatten beschrijvende informatie die aan deze *instantie-eigenschappen*is gekoppeld, zoals een tijdreeks-id, type, naam, beschrijving, hiërarchieën en instantievelden. Instantieeigenschappen bevatten ten minste hiërarchiegegevens.

*Instantievelden* zijn een verzameling beschrijvende informatie die waarden voor hiërarchieniveaus kan bevatten, evenals fabrikant, operator, enzovoort.

Nadat een gebeurtenisbron is geconfigureerd voor de Time Series Insights-omgeving, worden instanties automatisch gedetecteerd en gemaakt in een tijdreeksmodel. De instanties kunnen worden gemaakt of bijgewerkt via de Time Series Insights explorer met behulp van Time Series Model query's.

De [Contoso Wind Farm demo](https://insights.timeseries.azure.com/preview/samples) geeft verschillende voorbeelden van live voorbeelden.

[![Voorbeeld van voorbeeld time-seriemodel](media/v2-update-tsm/time-series-model-instance.png)](media/v2-update-tsm/time-series-model-instance.png#lightbox)

### <a name="instance-properties"></a>Instantie-eigenschappen

Instanties worden gedefinieerd door **timeSeriesId**, **typeId**, **naam**, **beschrijving**, **hierarchyIds**en **instanceFields**. Elke instantie wordt toegewezen aan slechts één *type*en één of meer *hiërarchieën*.

| Eigenschap | Beschrijving |
| --- | ---|
| timeSeriesId | De UUID van de tijdreeks waarmee de instantie is gekoppeld. |
| typeId | De UUID van het type Time Series Model waar de instantie aan is gekoppeld. Standaard worden alle nieuwe exemplaren gekoppeld aan een standaardtype.
| name | De **eigenschap naam** is optioneel en hoofdlettergevoelig. Als de **naam** niet beschikbaar is, wordt de **timeSeriesId**standaard weergegeven. Als er een naam wordt opgegeven, is **timeSeriesId** nog steeds beschikbaar in de [put.](time-series-insights-update-explorer.md#4-time-series-well) |
| description | Een tekstbeschrijving van de instantie. |
| hiërarchieËn | Hiermee bepaalt u tot welke hiërarchieën de instantie behoort. |
| instantieVelden | De eigenschappen van een instantie en statische gegevens die een instantie definieert. Ze definiëren waarden van hiërarchie- of niet-hiërarchie-eigenschappen en ondersteunen ook indexering om zoekbewerkingen uit te voeren. |

> [!NOTE]
> Hiërarchieën worden gebouwd met instantievelden. Extra **instantieVelden** kunnen worden toegevoegd voor verdere instantieeigenschapsdefinities.

Instanties hebben de volgende JSON-vertegenwoordiging:

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
> Lees voor Time Series Insights Instance API en maak, lees, update en verwijder (CRUD) ondersteuning, lees het artikel [Gegevensquery's](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) en de [INSTANCE API REST-documentatie](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#instances-api).

## <a name="time-series-model-hierarchies"></a>Hiërarchieën van tijdreeksmodellen

Time Series Model *hiërarchieën* organiseren instanties door eigenschapsnamen en hun relaties op te geven.

U meerdere hiërarchieën configureren in een bepaalde Time Series Insights-omgeving. Een instantie van het tijdreeksmodel kan worden toegewezen aan één hiërarchie of meerdere hiërarchieën (veel-op-veel relatie).

De [democlientinterface van Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples) geeft een standaardinstantie- en typehiërarchie weer.

[![Voorbeeld van de hiërarchie van tijdreeksmodel](media/v2-update-tsm/time-series-model-hierarchies.png)](media/v2-update-tsm/time-series-model-hierarchies.png#lightbox)

### <a name="hierarchy-definition"></a>Hiërarchiedefinitie

Hiërarchieën worden gedefinieerd door **hiërarchie-id**, **naam**en **bron**.

| Eigenschap | Beschrijving |
| ---| ---|
| id | De unieke id voor de hiërarchie, die bijvoorbeeld wordt gebruikt wanneer u een instantie definieert. |
| name | Een tekenreeks die wordt gebruikt om een naam voor de hiërarchie op te geven. |
| source | Hiermee geeft u de organisatiehiërarchie of het pad op, een top-down bovenliggende-onderliggende volgorde van de hiërarchie die gebruikers willen maken. De eigenschappen van bovenliggende en onderliggende eigenschappen brengen instantievelden in kaart. |

Hiërarchieën worden in JSON weergegeven als:

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

In het vorige JSON-voorbeeld:

* `Location`definieert een hiërarchie `states` met `cities`ouder en kind . Elk `location` kan `states`meerdere hebben, die `cities`op zijn beurt meerdere kunnen hebben.
* `ManufactureDate`definieert een hiërarchie `year` met `month`ouder en kind . Elk `ManufactureDate` kan `years`meerdere hebben, die `months`op zijn beurt meerdere kunnen hebben.

> [!TIP]
> Lees voor Time Series Insights Instance API en CRUD-ondersteuning het artikel [Gegevensquery's](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) en de documentatie Over de Api REST van de [Hiërarchie.](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#hierarchies-api)

### <a name="hierarchy-example"></a>Voorbeeld van hiërarchie

Overweeg een voorbeeld waarin hiërarchie `building` `floor` **H1** , en `room` als onderdeel van de **instantieFieldNames** definitie:

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

Gezien de instantievelden die in de vorige definitie en verschillende tijdreeksen zijn gebruikt, worden de hiërarchiekenmerken en -waarden weergegeven zoals weergegeven in de volgende tabel:

| Tijdserie-ID | Instantievelden |
| --- | --- |
| ID1 | "gebouw" = "1000", "vloer" = "10", "kamer" = "55"  |
| ID2 | "gebouw" = "1000", "kamer" = "55" |
| ID3 | "verdieping" = "10" |
| ID4 | "gebouw" = "1000", "vloer" = "10"  |
| ID5 | Geen van "gebouw", "verdieping", of "kamer" is ingesteld. |

Time Series **ID1** en **ID4** worden weergegeven als onderdeel van hiërarchie **H1** in de [Azure Time Series Insights-verkenner,](time-series-insights-update-explorer.md) omdat ze de *parameters voor het bouwen,* *de vloer*en *de ruimte* volledig hebben gedefinieerd en correct geordend.

De andere worden geclassificeerd onder *niet-parented instances* omdat ze niet voldoen aan de opgegeven gegevenshiërarchie.

## <a name="time-series-model-types"></a>Modeltypen tijdreeksen

Met *tijdreeksmodeltypen* u variabelen of formules definiëren voor het uitvoeren van berekeningen. Typen zijn gekoppeld aan een specifieke timeseries Insights-instantie.

Een type kan een of meer variabelen hebben. Een instantie van het Tijdreeksmodel kan bijvoorbeeld van het type *Temperatuursensor*zijn , die bestaat uit de variabelen *avg temperatuur,* *min temperatuur*en *maximale temperatuur*.

De [Demo van het Windpark van Contoso](https://insights.timeseries.azure.com/preview/samples) visualiseert verscheidene types van het Model van de Tijd die met hun respectieve instanties worden verbonden.

[![Voorbeeld van het type tijdreeksmodel](media/v2-update-tsm/time-series-model-types.png)](media/v2-update-tsm/time-series-model-types.png#lightbox)

> [!TIP]
> Lees voor Time Series Insights Instance API en CRUD-ondersteuning het artikel [Gegevensquery's](time-series-insights-update-tsq.md#time-series-model-query-tsm-q-apis) en de [Documenteerbewijs voor type-API REST](https://docs.microsoft.com/rest/api/time-series-insights/preview-model#types-api).

### <a name="type-properties"></a>Eigenschappen typen

Tijdreeksmodeltypen worden gedefinieerd op **id**, **naam**, **beschrijving**en **variabelen**.

| Eigenschap | Beschrijving |
| ---| ---|
| id | De UUID voor het type. |
| name | Een tekenreeks die wordt gebruikt om een naam voor het type op te geven. |
| description | Een tekenreeksbeschrijving voor het type. |
| Variabelen | Geef variabelen op die aan het type zijn gekoppeld. |

De typen voldoen aan het volgende JSON-voorbeeld:

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

Time Series Insights-typen kunnen veel variabelen hebben die formule- en berekeningsregels voor gebeurtenissen opgeven.

Elke variabele kan een van de drie *soorten*zijn: *numeriek*, *categorisch*en *aggregaat*.

* **Numerieke** soorten werken met continue waarden. 
* **Categorische** soorten werken met een gedefinieerde set afzonderlijke waarden.
* **Geaggregeerde** waarden combineren meerdere variabelen van één soort (alle numerieke of alle categorische).

In de volgende tabel ziet u welke eigenschappen relevant zijn voor elke variabele soort.

[![Variabele tabel tijdreeksmodel](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### <a name="numeric-variables"></a>Numerieke variabelen

| Variabele eigenschap | Beschrijving |
| --- | ---|
| Variabel filter | Filters zijn optionele voorwaardelijke clausules om het aantal rijen te beperken dat in aanmerking komt voor berekening. |
| Variabele waarde | Telemetriewaarden die worden gebruikt voor berekeningen die afkomstig zijn van het apparaat of de sensoren of worden getransformeerd met behulp van tijdreeksexpressies. Numerieke soortvariabelen moeten van het type *Dubbel*zijn .|
| Variabele interpolatie | Interpolatie geeft aan hoe u een signaal reconstrueren met behulp van bestaande gegevens. *Stap-* *en lineaire* interpolatieopties zijn beschikbaar voor numerieke variabelen. |
| Variabele aggregatie | Ondersteuningsberekening via *Avg*, *Min*, *Max*, *Sum*, *Count*, *First*, *Last* and time-weighted (*Avg*, *Min*, *Max*, *Sum*, *Left*) operators. |

Variabelen voldoen aan het volgende JSON-voorbeeld:

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

| Variabele eigenschap | Beschrijving |
| --- | ---|
| Variabel filter | Filters zijn optionele voorwaardelijke clausules om het aantal rijen te beperken dat in aanmerking komt voor berekening. |
| Variabele waarde | Telemetriewaarden die worden gebruikt voor berekening afkomstig van het apparaat of sensoren. Categorische soortvariabelen moeten *lang* of *tekenreeks zijn.* |
| Variabele interpolatie | Interpolatie geeft aan hoe u een signaal reconstrueren met behulp van bestaande gegevens. De optie *Interpolatie* van stap is beschikbaar voor categorische variabelen. |
| Variabele categorieën | Categorieën maken een toewijzing tussen de waarden die afkomstig zijn van het apparaat of sensoren naar een label. |
| Variabele standaardcategorie | De standaardcategorie is voor alle waarden die niet worden toegewezen in de eigenschap 'categorieën'. |

Variabelen voldoen aan het volgende JSON-voorbeeld:

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

#### <a name="aggregate-variables"></a>Geaggregeerde variabelen

| Variabele eigenschap | Beschrijving |
| --- | ---|
| Variabel filter | Filters zijn optionele voorwaardelijke clausules om het aantal rijen te beperken dat in aanmerking komt voor berekening. |
| Variabele aggregatie | Ondersteuningsberekening via *Avg*, *Min*, *Max*, *Som*, *Telling*, *Eerste*, *Laatste*. |

Variabelen voldoen aan het volgende JSON-voorbeeld:

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

Variabelen worden opgeslagen in de typedefinitie van een tijdreeksmodel en kunnen inline worden verstrekt via [Query-API's](time-series-insights-update-tsq.md) om de opgeslagen definitie te overschrijven.

## <a name="next-steps"></a>Volgende stappen

- Lees [Azure Time Series Insights Preview-opslag en -inval](./time-series-insights-update-storage-ingress.md).

- Meer informatie over algemene bewerkingen van tijdreeksmodellen in [gegevensmodellering in Azure Time Series Insights Preview](./time-series-insights-update-how-to-tsm.md)

- Lees de nieuwe [time series model](https://docs.microsoft.com/rest/api/time-series-insights/preview-model) referentiedocumentatie.
