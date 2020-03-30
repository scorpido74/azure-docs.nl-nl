---
title: Geavanceerde aggregaties in Azure Monitor-logboekquery's| Microsoft Documenten
description: Beschrijft enkele van de meer geavanceerde aggregatieopties die beschikbaar zijn voor Azure Monitor-logboekquery's.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: e5dc290a40342e0797001dde6cab90e12dd5cf39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77662175"
---
# <a name="advanced-aggregations-in-azure-monitor-log-queries"></a>Geavanceerde aggregaties in Azure Monitor-logboekquery's

> [!NOTE]
> U moet [aggregaties in Azure Monitor-query's](./aggregations.md) voltooien voordat u deze les voltooit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

In dit artikel worden enkele van de meer geavanceerde aggregatieopties beschreven die beschikbaar zijn voor Azure Monitor-query's.

## <a name="generating-lists-and-sets"></a>Lijsten en sets genereren
U kunt `makelist` gegevens gebruiken om gegevens te draaien op basis van de volgorde van waarden in een bepaalde kolom. U bijvoorbeeld de meest voorkomende ordergebeurtenissen op uw machines verkennen. U de gegevens in wezen draaien aan de orde van EventID's op elke machine. 

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makelist(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085,704,704,701] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

`makelist`genereert een lijst in de volgorde waarin gegevens zijn doorgegeven. Als u gebeurtenissen wilt sorteren `asc` van oudste naar `desc`nieuwste, gebruikt u in de volgordeinstructie in plaats van . 

Het is ook handig om een lijst te maken met alleen verschillende waarden. Dit wordt een _set_ genoemd `makeset`en kan worden gegenereerd met:

```Kusto
Event
| where TimeGenerated > ago(12h)
| order by TimeGenerated desc
| summarize makeset(EventID) by Computer
```

|Computer|list_EventID|
|---|---|
| computer1 | [704,701,1501,1500,1085] |
| computer2 | [326,105,302,301,300,102] |
| ... | ... |

Net `makelist` `makeset` als , werkt ook met bestelde gegevens en zal het genereren van de arrays op basis van de volgorde van de rijen die worden doorgegeven in het.

## <a name="expanding-lists"></a>Lijsten uitbreiden
De omgekeerde bewerking `makelist` `makeset` van `mvexpand`of is , die een lijst met waarden uitbreidt om rijen te scheiden. Het kan worden uitgebreid over een willekeurig aantal dynamische kolommen, zowel JSON als array. U bijvoorbeeld de *heartbeattabel* controleren op oplossingen die gegevens verzenden van computers die in het afgelopen uur een heartbeat hebben verzonden:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, Solutions
```

| Computer | Oplossingen | 
|--------------|----------------------|
| computer1 | "beveiliging", "updates", "changeTracking" |
| computer2 | "veiligheid", "updates" |
| computer3 | "antiMalware", "changeTracking" |
| ... | ... |

Hiermee `mvexpand` u elke waarde in een afzonderlijke rij weergeven in plaats van in een door komma's gescheiden lijst:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
```

| Computer | Oplossingen | 
|--------------|----------------------|
| computer1 | "veiligheid" |
| computer1 | "updates" |
| computer1 | "changeTracking" |
| computer2 | "veiligheid" |
| computer2 | "updates" |
| computer3 | "antiMalware" |
| computer3 | "changeTracking" |
| ... | ... |


U `makelist` vervolgens opnieuw items groeperen en deze keer de lijst met computers per oplossing bekijken:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| project Computer, split(Solutions, ",")
| mvexpand Solutions
| summarize makelist(Computer) by tostring(Solutions) 
```

|Oplossingen | list_Computer |
|--------------|----------------------|
| "veiligheid" | ["computer1", "computer2"] |
| "updates" | ["computer1", "computer2"] |
| "changeTracking" | ["computer1", "computer3"] |
| "antiMalware" | ["computer3"] |
| ... | ... |

## <a name="handling-missing-bins"></a>Ontbrekende opslaglocaties verwerken
Een handige `mvexpand` toepassing van is de noodzaak om standaardwaarden in te vullen voor ontbrekende opslaglocaties. Stel dat u op zoek bent naar de uptime van een bepaalde machine door de hartslag te verkennen. U wilt ook de bron zien van de hartslag die zich in de _categoriekolom_ bevindt. Normaal gesproken zouden we gebruik maken van een eenvoudige samenvatting verklaring als volgt:

```Kusto
Heartbeat
| where TimeGenerated > ago(12h)
| summarize count() by Category, bin(TimeGenerated, 1h)
```

| Categorie | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direct Agent | 2017-06-06T17:00:00Z | 15 |
| Direct Agent | 2017-06-06T18:00:00Z | 60 |
| Direct Agent | 2017-06-06T20:00:00Z | 55 |
| Direct Agent | 2017-06-06T21:00:00Z | 57 |
| Direct Agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |

In deze resultaten ontbreekt echter de bucket die is gekoppeld aan "2017-06-06T19:00:00Z" omdat er geen hartslaggegevens voor dat uur zijn. Gebruik `make-series` de functie om een standaardwaarde toe te wijzen aan lege buckets. Hiermee wordt een rij gegenereerd voor elke categorie met twee extra matrixkolommen, één voor waarden en één voor overeenkomende tijdbuckets:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
```

| Categorie | count_ | TimeGenerated |
|---|---|---|
| Direct Agent | [15,60,0,55,60,57,60,...] | ["2017-06-06T17:00:00.0000000Z","2017-06-06T18:00:00000000Z","2017-06-06T19:0 00:0000000Z","2017-06-06T20:00:00.0000000Z","2017-06-06T21:00:000000000Z",...] |
| ... | ... | ... |

Het derde element van de *count_* array is een 0 zoals verwacht, en er is een overeenkomende tijdstempel van "2017-06-06T19:00:00.0000000Z" in de _array TimeGenerated._ Deze array formaat is echter moeilijk te lezen. Gebruik `mvexpand` om de arrays uit te vouwen `summarize`en dezelfde indelingsuitvoer te produceren als gegenereerd door:

```Kusto
Heartbeat
| make-series count() default=0 on TimeGenerated in range(ago(1d), now(), 1h) by Category 
| mvexpand TimeGenerated, count_
| project Category, TimeGenerated, count_
```

| Categorie | TimeGenerated | count_ |
|--------------|----------------------|--------|
| Direct Agent | 2017-06-06T17:00:00Z | 15 |
| Direct Agent | 2017-06-06T18:00:00Z | 60 |
| Direct Agent | 2017-06-06T19:00:00Z | 0 |
| Direct Agent | 2017-06-06T20:00:00Z | 55 |
| Direct Agent | 2017-06-06T21:00:00Z | 57 |
| Direct Agent | 2017-06-06T22:00:00Z | 60 |
| ... | ... | ... |



## <a name="narrowing-results-to-a-set-of-elements-let-makeset-toscalar-in"></a>De resultaten beperken tot een `let` `makeset`reeks `toscalar`elementen: , ,`in`
Een veelvoorkomend scenario is om de namen van bepaalde specifieke entiteiten te selecteren op basis van een reeks criteria en vervolgens een andere gegevensset te filteren op die set entiteiten. U bijvoorbeeld computers vinden waarvan bekend is dat ze ontbrekende updates hebben en IP's identificeren waarvoor deze computers hebben geroepen:


```Kusto
let ComputersNeedingUpdate = toscalar(
    Update
    | summarize makeset(Computer)
    | project set_Computer
);
WindowsFirewall
| where Computer in (ComputersNeedingUpdate)
```

## <a name="next-steps"></a>Volgende stappen

Bekijk andere lessen voor het gebruik van de [Kusto-querytaal](/azure/kusto/query/) met Azure Monitor-logboekgegevens:

- [Tekenreeksbewerkingen](string-operations.md)
- [Datum- en tijdbewerkingen](datetime-operations.md)
- [Aggregatiefuncties](aggregations.md)
- [Geavanceerde aggregaties](advanced-aggregations.md)
- [JSON en gegevensstructuren](json-data-structures.md)
- [Joins](joins.md)
- [Grafieken](charts.md)
