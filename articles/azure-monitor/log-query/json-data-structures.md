---
title: Werken met teken reeksen in Azure Monitor-logboek query's | Microsoft Docs
description: In dit artikel vindt u een zelf studie voor het gebruik van Azure Monitor Log Analytics in de Azure Portal voor het opvragen en analyseren van logboek gegevens in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: f792820b7b0dff20e647031410ba87ac26c2495a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80672961"
---
# <a name="working-with-json-and-data-structures-in-azure-monitor-log-queries"></a>Werken met JSON en gegevens structuren in Azure Monitor-logboek query's

> [!NOTE]
> U moet aan de [slag met Azure Monitor Log Analytics om](get-started-portal.md) aan de slag te gaan [met Azure monitor logboek query's](get-started-queries.md) voordat u deze les voltooit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Geneste objecten zijn objecten die andere objecten bevatten in een matrix of een kaart van sleutel-waardeparen. Deze objecten worden weer gegeven als JSON-teken reeksen. In dit artikel wordt beschreven hoe JSON wordt gebruikt om gegevens op te halen en geneste objecten te analyseren.

## <a name="working-with-json-strings"></a>Werken met JSON-teken reeksen
Wordt `extractjson` gebruikt voor toegang tot een specifiek JSON-element in een bekend pad. Voor deze functie is een padexpressie vereist die gebruikmaakt van de volgende conventies.

- _$_ verwijzen naar de hoofdmap
- Gebruik de vier Kante haak of punt notatie om te verwijzen naar indexen en elementen, zoals aangegeven in de volgende voor beelden.


Gebruik haakjes voor indexen en punten om elementen van elkaar te scheiden:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

Dit is hetzelfde resultaat als u alleen de vier Kante haken gebruikt:

```Kusto
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

Als er slechts één element is, kunt u alleen de punt notatie gebruiken:

```Kusto
let hosts_report=dynamic({"location":"North_DC", "status":"running", "rate":5});
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>Met objecten werken

### <a name="parsejson"></a>parsejson
Om toegang te krijgen tot meerdere elementen in uw JSON-structuur, is het eenvoudiger om deze te openen als een dynamisch object. Wordt `parsejson` gebruikt om tekst gegevens naar een dynamisch object te casten. Wanneer de gegevens zijn geconverteerd naar een dynamisch type, kunnen er extra functies worden gebruikt voor het analyseren van de informatie.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
Gebruiken `arraylength` om het aantal elementen in een matrix te tellen:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
Gebruiken `mvexpand` om de eigenschappen van een object te verstoren in afzonderlijke rijen.

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
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
In deze uitvoer worden de namen van de object velden en de bijbehorende gegevens typen beschreven. 

Geneste objecten kunnen verschillende schema's hebben, zoals in het volgende voor beeld:

```Kusto
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![Schema maken](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>Volgende stappen
Zie andere lessen voor het gebruik van logboek query's in Azure Monitor:

- [Tekenreeksbewerkingen](string-operations.md)
- [Datum- en tijdbewerkingen](datetime-operations.md)
- [Aggregatiefuncties](aggregations.md)
- [Geavanceerde aggregaties](advanced-aggregations.md)
- [Geavanceerde query's schrijven](advanced-query-writing.md)
- [Samenvoegingen](joins.md)
- [Grafieken](charts.md)
