---
title: Efficiënte logboek query's schrijven in Azure Monitor | Microsoft Docs
description: Verwijzingen naar bronnen voor het leren van query's in Log Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2019
ms.openlocfilehash: a5ee03f6c42f076549856161a6ebe0b1888fe4aa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894137"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Efficiënte logboek query's schrijven in Azure Monitor
In dit artikel vindt u aanbevelingen voor het schrijven van efficiënte logboek query's in Azure Monitor. Met behulp van deze strategieën kunt u ervoor zorgen dat uw query's snel en met minimale overgehoord worden uitgevoerd.

## <a name="scope-your-query"></a>Uw query bereiken
Als uw query meer gegevens verwerkt dan u werkelijk nodig hebt, kan dit leiden tot een langlopende query. het resultaat is vaak te veel gegevens in uw resultaten om effectief te kunnen analyseren. In uitzonderlijke gevallen kan de query zelfs een time-out veroorzaken en mislukken.

### <a name="specify-your-data-source"></a>Uw gegevens bron opgeven
De eerste stap bij het schrijven van een efficiënte query beperkt het bereik tot de vereiste gegevens bronnen. Het opgeven van een tabel is altijd de voor keur boven het uitvoeren van een brede zoek opdracht, zoals `search *`. Als u een query wilt uitvoeren op een specifieke tabel, start u de query met de naam van de tabel, zoals in het volgende:

``` Kusto
requests | ...
```

U kunt [zoeken](/azure/kusto/query/searchoperator) gebruiken om te zoeken naar een waarde in meerdere kolommen in specifieke tabellen, met behulp van een query als de volgende:

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

Gebruik [Union](/azure/kusto/query/unionoperator) om verschillende tabellen op te vragen, zoals de volgende:

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>Een tijds bereik opgeven
U moet de query ook beperken tot het tijds bereik van de gegevens die u nodig hebt. De query bevat standaard gegevens die in de afgelopen 24 uur zijn verzameld. U kunt deze optie in de [tijds bereik kiezer](get-started-portal.md#select-a-time-range) wijzigen of expliciet aan uw query toevoegen. Het is het beste om het tijd filter direct achter de tabel naam toe te voegen, zodat de rest van uw query alleen gegevens in dat bereik verwerkt:

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>Alleen de meest recente records ophalen

Als u alleen de meest recente records wilt retour neren, gebruikt u de operator *Top* zoals in de volgende query die de meest recente 10 records retourneert die in de *tracerings* tabel zijn geregistreerd:

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>Records filteren
Als u alleen logboeken wilt bekijken die overeenkomen met een bepaalde voor waarde, gebruikt u de operator *where* zoals in de volgende query die alleen records retourneert waarin de _severityLevel_ -waarde hoger is dan 0:

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>Teken reeks vergelijkingen
Bij het [evalueren van teken reeksen](/azure/kusto/query/datatypes-string-operators)moet u doorgaans `has` in plaats van `contains` gebruiken bij het zoeken naar volledige tokens. `has` is efficiënter omdat het niet nodig is om te zoeken naar subtekenreeksen.

## <a name="returned-columns"></a>Geretourneerde kolommen

Gebruik [project](/azure/kusto/query/projectoperator) om de set kolommen die alleen worden verwerkt, beperkt te beperken:

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

Hoewel u [uitbreiden](/azure/kusto/query/extendoperator) kunt gebruiken om waarden te berekenen en uw eigen kolommen te maken, is het meestal efficiënter om te filteren op een tabel kolom.

Zo is de eerste query hieronder die filtert op de naam van de _bewerkings\__ een efficiënter doel dan de tweede waarmee een nieuwe _abonnements_ kolom wordt gemaakt en er filters op worden toegepast:

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>Samen voegen gebruiken
Wanneer u de operator voor [samen voegen](/azure/kusto/query/joinoperator) gebruikt, kiest u de tabel met minder rijen aan de linkerkant van de query.


## <a name="next-steps"></a>Volgende stappen
Zie [Aanbevolen procedures voor query's](/azure/kusto/query/best-practices)voor meer informatie over aanbevolen procedures voor query's.