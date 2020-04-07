---
title: Werken met tekenreeksen in Azure Monitor-logboekquery's | Microsoft Documenten
description: In dit artikel vindt u een zelfstudie voor het gebruik van Azure Monitor Log Analytics in de Azure-portal voor het opvragen en analyseren van logboekgegevens in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: f792820b7b0dff20e647031410ba87ac26c2495a
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672961"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Werken met JSON- en gegevensstructuren in azure monitor-logboekquery's

> [!NOTE]
> U moet aan [de slag gaan met Azure Monitor Log Analytics](get-started-portal.md) en Aan de slag met Azure [Monitor-logboekquery's](get-started-queries.md) voordat u deze les voltooit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Geneste objecten zijn objecten die andere objecten in een array of een kaart van sleutelwaardeparen bevatten. Deze objecten worden weergegeven als JSON-tekenreeksen. In dit artikel wordt beschreven hoe JSON wordt gebruikt om gegevens op te halen en geneste objecten te analyseren.

## <a name="working-with-json-strings"></a>Werken met JSON-snaren
Gebruiken `extractjson` om toegang te krijgen tot een specifiek JSON-element in een bekend pad. Deze functie vereist een padexpressie die de volgende conventies gebruikt.

- _$_ om naar de hoofdmap te verwijzen
- Gebruik de beugel of puntnotatie om te verwijzen naar indexen en elementen zoals geïllustreerd in de volgende voorbeelden.


Gebruik haakjes voor indexen en stippen om elementen te scheiden:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Dit is hetzelfde resultaat met alleen de haken notatie:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Als er slechts één element is, u alleen de dot notatie gebruiken:

```Kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Met objecten werken

### <a name="parsejson"></a>parsejson
Als u toegang hebt tot meerdere elementen in uw json-structuur, is het eenvoudiger om toegang te krijgen tot het object als een dynamisch object. Tekstgegevens `parsejson` casten naar een dynamisch object. Eenmaal geconverteerd naar een dynamisch type, kunnen extra functies worden gebruikt om de gegevens te analyseren.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylengte
Gebruik `arraylength` om het aantal elementen in een array te tellen:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Hiermee `mvexpand` u de eigenschappen van een object in afzonderlijke rijen splitsen.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>bouwschema
Gebruik `buildschema` om het schema op te halen dat alle waarden van een object toelaat:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

De uitvoer is een schema in JSON-indeling:
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
Deze uitvoer beschrijft de namen van de objectvelden en de overeenkomende gegevenstypen. 

Geneste objecten kunnen verschillende schema's hebben, zoals in het volgende voorbeeld:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Schema samenstellen](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Volgende stappen
Bekijk andere lessen voor het gebruik van logboekquery's in Azure Monitor:

- [Tekenreeksbewerkingen](string-operations.md)
- [Datum- en tijdbewerkingen](datetime-operations.md)
- [Aggregatiefuncties](aggregations.md)
- [Geavanceerde aggregaties](advanced-aggregations.md)
- [Geavanceerde query's schrijven](advanced-query-writing.md)
- [Joins](joins.md)
- [Grafieken](charts.md)
