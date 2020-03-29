---
title: Configureerbare regels op basis van drempelwaarden in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u referentiegegevens gebruikt om een waarschuwingsoplossing te bereiken met configureerbare regels op basis van drempelwaarden in Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 94fdddf11acb6763ed98a4b7e17304fbde0e25dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75369708"
---
# <a name="process-configurable-threshold-based-rules-in-azure-stream-analytics"></a>Configureerbare regels op basis van drempelwaarden verwerken in Azure Stream Analytics
In dit artikel wordt beschreven hoe u referentiegegevens gebruikt om een waarschuwingsoplossing te bereiken die configureerbare regels op basis van drempelwaarden gebruikt in Azure Stream Analytics.

## <a name="scenario-alerting-based-on-adjustable-rule-thresholds"></a>Scenario: Waarschuwingen op basis van instelbare regeldrempels
Mogelijk moet u een waarschuwing produceren als uitvoer wanneer inkomende gestreamde gebeurtenissen een bepaalde waarde hebben bereikt of wanneer een geaggregeerde waarde op basis van de inkomende gestreamde gebeurtenissen een bepaalde drempel overschrijdt. Het is eenvoudig om een Stream Analytics-query in te stellen die de waarde vergeleek met een statische drempelwaarde die is vastgesteld en vooraf is bepaald. Een vaste drempelwaarde kan hard worden gecodeerd in de syntaxis van streamingquery's met behulp van eenvoudige numerieke vergelijkingen (groter dan, minder dan en gelijkheid).

In sommige gevallen moeten de drempelwaarden gemakkelijker kunnen worden geconfigureerd zonder de querysyntaxis telkens opnieuw te bewerken wanneer een drempelwaarde verandert. In andere gevallen hebt u mogelijk talloze apparaten of gebruikers nodig die door dezelfde query zijn verwerkt, waarbij elk van hen een andere drempelwaarde waarden op elk type apparaat heeft. 

Dit patroon kan worden gebruikt om drempels dynamisch te configureren, selectief te kiezen welk type apparaat de drempel waarde is door de invoergegevens te filteren en selectief te kiezen welke velden in de uitvoer moeten worden opgenomen.

## <a name="recommended-design-pattern"></a>Aanbevolen ontwerppatroon
Gebruik een referentiegegevensinvoer voor een streamanalytics-taak als een opzoekfunctie van de waarschuwingsdrempels:
- Sla de drempelwaarden op in de referentiegegevens, één waarde per sleutel.
- Neem deel aan de invoergebeurtenissen voor streaminggegevens naar de referentiegegevens in de sleutelkolom.
- Gebruik de toetswaarde uit de referentiegegevens als drempelwaarde.

## <a name="example-data-and-query"></a>Voorbeeldgegevens en query
In het voorbeeld worden waarschuwingen gegenereerd wanneer het totaal van gegevens die vanaf apparaten in een minutenlang venster worden gestreamd, overeenkomt met de opgegeven waarden in de regel die als referentiegegevens wordt verstrekt.

In de query, voor elke deviceId en elke metricName onder de deviceId, u van 0 naar 5 dimensies configureren naar GROEPEREN OP. Alleen de gebeurtenissen met de bijbehorende filterwaarden worden gegroepeerd. Eenmaal gegroepeerd, worden gevensterde aggregaten van Min, Max, Avg berekend over een tuimelvenster van 60 seconden. Filters op de geaggregeerde waarden worden vervolgens berekend volgens de geconfigureerde drempelwaarde in de referentie, om de gebeurtenis voor de waarschuwingsuitvoer te genereren.

Stel bijvoorbeeld dat er een streamanalytics-taak is met een invoercode voor referentiegegevens met de naam **regels**en streaming gegevensinvoer met de naam **metrics**. 

## <a name="reference-data"></a>Referentiegegevens
In dit voorbeeld worden referentiegegevens weergegeven hoe een op drempels gebaseerde regel kan worden weergegeven. Een JSON-bestand bevat de referentiegegevens en wordt opgeslagen in Azure blob-opslag en die blob-opslagcontainer wordt gebruikt als invoer met de naam **regels**voor referentiegegevens . U dit JSON-bestand overschrijven en de regelconfiguratie vervangen naarmate de tijd vordert, zonder de streamingtaak te stoppen of te starten.

- De voorbeeldregel wordt gebruikt om een instelbare waarschuwing weer te geven `90` wanneer de CPU hoger is dan (gemiddeld groter dan of gelijk is aan) het waardepercentage. Het `value` veld is configureerbaar als dat nodig is.
- Let op de regel heeft een **operatorveld,** dat later `AVGGREATEROREQUAL`dynamisch wordt geïnterpreteerd in de querysyntaxis . 
- De regel filtert de gegevens `2` op `C1`een bepaalde dimensiesleutel met waarde . Andere velden zijn lege tekenreeksen, die aangeven dat de invoerstroom niet door die gebeurtenisvelden moet worden gefilterd. U extra CPU-regels instellen om andere overeenkomende velden te filteren als dat nodig is.
- Niet alle kolommen worden opgenomen in de gebeurtenis uitvoerwaarschuwing. In dit `includedDim` geval `2` wordt het `TRUE` sleutelnummer ingeschakeld om te vertegenwoordigen dat veldnummer 2 van gebeurtenisgegevens in de stream wordt opgenomen in de kwalificerende uitvoergebeurtenissen. De andere velden zijn niet opgenomen in de waarschuwingsuitvoer, maar de veldlijst kan worden aangepast.


```json
{
    "ruleId": 1234, 
    "deviceId" : "978648", 
    "metricName": "CPU", 
    "alertName": "hot node AVG CPU over 90",
    "operator" : "AVGGREATEROREQUAL",
    "value": 90, 
    "includeDim": {
        "0": "FALSE", 
        "1": "FALSE", 
        "2": "TRUE", 
        "3": "FALSE", 
        "4": "FALSE"
    },
    "filter": {
        "0": "", 
        "1": "",
        "2": "C1", 
        "3": "", 
        "4": ""
    }    
}
```

## <a name="example-streaming-query"></a>Voorbeeldstreamingquery
In dit voorbeeld voegt Stream Analytics-query de **verwijzingsgegevens** van de regels uit het bovenstaande voorbeeld samen met een invoerstroom met de namen **met de**naam metrics .

```sql
WITH transformedInput AS
(
    SELECT
        dim0 = CASE rules.includeDim.[0] WHEN 'TRUE' THEN metrics.custom.dimensions.[0].value ELSE NULL END,
        dim1 = CASE rules.includeDim.[1] WHEN 'TRUE' THEN metrics.custom.dimensions.[1].value ELSE NULL END,
        dim2 = CASE rules.includeDim.[2] WHEN 'TRUE' THEN metrics.custom.dimensions.[2].value ELSE NULL END,
        dim3 = CASE rules.includeDim.[3] WHEN 'TRUE' THEN metrics.custom.dimensions.[3].value ELSE NULL END,
        dim4 = CASE rules.includeDim.[4] WHEN 'TRUE' THEN metrics.custom.dimensions.[4].value ELSE NULL END,
        metric = metrics.metric.value,
        metricName = metrics.metric.name,
        deviceId = rules.deviceId, 
        ruleId = rules.ruleId, 
        alertName = rules.alertName,
        ruleOperator = rules.operator, 
        ruleValue = rules.value
    FROM 
        metrics
        timestamp by eventTime
    JOIN 
        rules
        ON metrics.deviceId = rules.deviceId AND metrics.metric.name = rules.metricName
    WHERE
        (rules.filter.[0] = '' OR metrics.custom.filters.[0].value = rules.filter.[0]) AND 
        (rules.filter.[1] = '' OR metrics.custom.filters.[1].value = rules.filter.[1]) AND
        (rules.filter.[2] = '' OR metrics.custom.filters.[2].value = rules.filter.[2]) AND
        (rules.filter.[3] = '' OR metrics.custom.filters.[3].value = rules.filter.[3]) AND
        (rules.filter.[4] = '' OR metrics.custom.filters.[4].value = rules.filter.[4])
)

SELECT
    System.Timestamp as time, 
    transformedInput.deviceId as deviceId,
    transformedInput.ruleId as ruleId,
    transformedInput.metricName as metric,
    transformedInput.alertName as alert,
    AVG(metric) as avg,
    MIN(metric) as min, 
    MAX(metric) as max, 
    dim0, dim1, dim2, dim3, dim4
FROM
    transformedInput
GROUP BY
    transformedInput.deviceId,
    transformedInput.ruleId,
    transformedInput.metricName,
    transformedInput.alertName,
    dim0, dim1, dim2, dim3, dim4,
    ruleOperator, 
    ruleValue, 
    TumblingWindow(second, 60)
HAVING
    (
        (ruleOperator = 'AVGGREATEROREQUAL' AND avg(metric) >= ruleValue) OR
        (ruleOperator = 'AVGEQUALORLESS' AND avg(metric) <= ruleValue) 
    )
```

## <a name="example-streaming-input-event-data"></a>Voorbeeld van streaming-invoergebeurtenisgegevens
Dit voorbeeld JSON-gegevens vertegenwoordigt de **metrische** invoergegevens die worden gebruikt in de bovenstaande streamingquery. 

- Drie voorbeeldgebeurtenissen worden weergegeven binnen de tijdspanne van 1 minuut, waarde `T14:50`. 
- Alle drie hebben `deviceId` `978648`dezelfde waarde.
- De cpu-metrische waarden variëren `98` `95`per `80` gebeurtenis, respectievelijk , , Alleen de eerste twee voorbeeldgebeurtenissen overschrijden de CPU-waarschuwingsregel die in de regel is ingesteld.
- Het veld includeDim in de waarschuwingsregel was sleutelnummer 2. Het bijbehorende veld 2 in `NodeName`de voorbeeldgebeurtenissen wordt de naam genoemd . De drie voorbeeldgebeurtenissen `N024` `N024`hebben `N014` waarden , respectievelijk. In de uitvoer ziet u `N024` alleen het knooppunt, omdat dit de enige gegevens zijn die overeenkomt met de waarschuwingscriteria voor hoge CPU.In the output, you see only the node as that is the only data that matches the alert criteria for high CPU. `N014`voldoet niet aan de hoge CPU-drempel.
- De waarschuwingsregel is geconfigureerd `filter` met alleen op sleutelnummer 2, wat overeenkomt met het `cluster` veld in de voorbeeldgebeurtenissen. De drie voorbeeldgebeurtenissen `C1` hebben allemaal waarde en voldoen aan de filtercriteria.

```json
{
    "eventTime": "2018-04-30T14:50:23.1324132Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N1"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N1"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 98,
        "count": 1.0,
        "min": 98,
        "max": 98,
        "stdDev": 0.0
    }
}
{
    "eventTime": "2018-04-30T14:50:24.1324138Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N2"
            },
            "1": {
                "name": "Cluster",
                "value": "C1"
            },
            "2": {
                "name": "NodeName",
                "value": "N024"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N2"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 95,
        "count": 1,
        "min": 95,
        "max": 95,
        "stdDev": 0
    }
}
{
    "eventTime": "2018-04-30T14:50:37.1324130Z",
    "deviceId": "978648",
    "custom": {
        "dimensions": {
            "0": {
                "name": "NodeType",
                "value": "N3"
            },
            "1": {
                "name": "Cluster",
                "value": "C1 "
            },
            "2": {
                "name": "NodeName",
                "value": "N014"
            }
        },
        "filters": {
            "0": {
                "name": "application",
                "value": "A1"
            },
            "1": {
                "name": "deviceType",
                "value": "T1"
            },
            "2": {
                "name": "cluster",
                "value": "C1"
            },
            "3": {
                "name": "nodeType",
                "value": "N3"
            }
        }
    },
    "metric": {
        "name": "CPU",
        "value": 80,
        "count": 1,
        "min": 80,
        "max": 80,
        "stdDev": 0
    }
}
```

## <a name="example-output"></a>Voorbeelduitvoer
In dit voorbeeld wordt json-gegevens weergegeven, waarbij één waarschuwingsgebeurtenis is geproduceerd op basis van de CPU-drempelregel die in de referentiegegevens is gedefinieerd. De uitvoergebeurtenis bevat de naam van de waarschuwing en de geaggregeerde (gemiddelde, min, max) van de onderzochte velden. De uitvoergebeurtenisgegevens bevatten veldsleutelnummer 2-waarde `NodeName` `N024` vanwege de regelconfiguratie. (De JSON werd gewijzigd om regeleinden voor leesbaarheid weer te geven.)

```JSON
{"time":"2018-05-01T02:03:00.0000000Z","deviceid":"978648","ruleid":1234,"metric":"CPU",
"alert":"hot node AVG CPU over 90","avg":96.5,"min":95.0,"max":98.0,
"dim0":null,"dim1":null,"dim2":"N024","dim3":null,"dim4":null}
```
