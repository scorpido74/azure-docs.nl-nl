---
title: Joins in Azure Monitor-logboekquery's | Microsoft Documenten
description: Dit artikel bevat een les over het gebruik van joins in Azure Monitor-logboekquery's.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 2dace6968fbbe69f806c27fb7a46e60c63f78b4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670199"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Voegt zich aan bij Azure Monitor-logboekquery's

> [!NOTE]
> U moet aan [de slag gaan met Azure Monitor Log Analytics](get-started-portal.md) en Azure [Monitor-logboekquery's](get-started-queries.md) voordat u deze les voltooit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Met joins u gegevens uit meerdere tabellen analyseren in dezelfde query. Ze voegen de rijen van twee gegevenssets samen door waarden van opgegeven kolommen te afstemmen.


```Kusto
SecurityEvent 
| where EventID == 4624     // sign-in events
| project Computer, Account, TargetLogonId, LogonTime=TimeGenerated
| join kind= inner (
    SecurityEvent 
    | where EventID == 4634     // sign-out events
    | project TargetLogonId, LogoffTime=TimeGenerated
) on TargetLogonId
| extend Duration = LogoffTime-LogonTime
| project-away TargetLogonId1 
| top 10 by Duration desc
```

In dit voorbeeld wordt de eerste gegevenssetfilters voor alle aanmeldingsgebeurtenissen gefilterd. Dit wordt gekoppeld aan een tweede gegevensset die filtert voor alle afmeldingsgebeurtenissen. De geprojecteerde kolommen zijn _Computer,_ _Account,_ _TargetLogonId_en _TimeGenerated_. De gegevenssets worden gecorreleerd door een gedeelde kolom, _TargetLogonId_. De uitvoer is één record per correlatie, die zowel de aanmeldings- als de aanmeldingstijd heeft.

Als beide gegevenssets kolommen met dezelfde namen hebben, krijgen de kolommen van de gegevensset aan de rechterkant een indexnummer, dus in dit voorbeeld worden _targetlogonid_ weergegeven met waarden uit de linkertabel en _TargetLogonId1_ met waarden uit de rechterzijdetabel. In dit geval is de tweede _TargetLogonId1-kolom_ verwijderd met behulp van de `project-away` operator.

> [!NOTE]
> Om de prestaties te verbeteren, houdt u alleen de `project` relevante kolommen van de samengevoegde gegevenssets met behulp van de operator.


Gebruik de volgende syntaxis om twee gegevenssets samen te voegen en de samengevoegde sleutel heeft een andere naam tussen de twee tabellen:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Opzoektabellen
Een gemeenschappelijk gebruik van joins is `datatable` het gebruik van statische mapping van waarden met behulp van die kunnen helpen bij het omzetten van de resultaten in meer toonbare manier. Bijvoorbeeld om de beveiligingsgebeurtenisgegevens te verrijken met de gebeurtenisnaam voor elke gebeurtenis-id.

```Kusto
let DimTable = datatable(EventID:int, eventName:string)
  [
    4625, "Account activity",
    4688, "Process action",
    4634, "Account activity",
    4658, "The handle to an object was closed",
    4656, "A handle to an object was requested",
    4690, "An attempt was made to duplicate a handle to an object",
    4663, "An attempt was made to access an object",
    5061, "Cryptographic operation",
    5058, "Key file operation"
  ];
SecurityEvent
| join kind = inner
 DimTable on EventID
| summarize count() by eventName
```

![Deelnemen aan een gegevenstabel](media/joins/dim-table.png)

## <a name="join-kinds"></a>Deelnemen aan soorten
Geef het type join met het _vriendelijke_ argument op. Elk type voert een andere overeenkomst uit tussen de records van de opgegeven tabellen zoals beschreven in de volgende tabel.

| Relatietype | Beschrijving |
|:---|:---|
| innerunique | Dit is de standaard join-modus. Eerst worden de waarden van de overeenkomende kolom in de linkertabel gevonden en worden dubbele waarden verwijderd.  Vervolgens wordt de set unieke waarden afgestemd op de juiste tabel. |
| Innerlijke | Alleen overeenkomende records in beide tabellen zijn opgenomen in de resultaten. |
| linksbuiten | Alle records in de linkertabel en overeenkomende records in de rechtertabel zijn opgenomen in de resultaten. Niet-overeenkomende uitvoereigenschappen bevatten nullen.  |
| linksanti | Records van de linkerkant die geen overeenkomsten van rechts hebben, worden in de resultaten opgenomen. De resultatentabel bevat alleen kolommen uit de linkertabel. |
| linkssemi | Records van de linkerkant met overeenkomsten van rechts zijn opgenomen in de resultaten. De resultatentabel bevat alleen kolommen uit de linkertabel. |


## <a name="best-practices"></a>Aanbevolen procedures

Houd rekening met de volgende punten voor optimale prestaties:

- Gebruik een tijdfilter op elke tabel om de records te verminderen die moeten worden geëvalueerd voor de join.
- Gebruik `where` `project` en om het aantal rijen en kolommen in de invoertabellen vóór de join te verminderen.
- Als de ene tabel altijd kleiner is dan de andere, gebruikt u deze als de linkerkant van de join.


## <a name="next-steps"></a>Volgende stappen
Bekijk andere lessen voor het gebruik van Azure Monitor-logboekquery's:

- [Tekenreeksbewerkingen](string-operations.md)
- [Aggregatiefuncties](aggregations.md)
- [Geavanceerde aggregaties](advanced-aggregations.md)
- [JSON en gegevensstructuren](json-data-structures.md)
- [Geavanceerde query's schrijven](advanced-query-writing.md)
- [Grafieken](charts.md)
