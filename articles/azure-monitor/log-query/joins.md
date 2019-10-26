---
title: Samen voegen in Azure Monitor-logboek query's | Microsoft Docs
description: Dit artikel bevat een les over het gebruik van samen voegingen in Azure Monitor-logboek query's.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 526c359367271c69ccd461e4421c3223b00fbc36
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900289"
---
# <a name="joins-in-azure-monitor-log-queries"></a>Samen voegen in Azure Monitor-logboek query's

> [!NOTE]
> U moet aan de [slag met Azure Monitor Log Analytics](get-started-portal.md) en [Azure monitor logboek query's](get-started-queries.md) uitvoeren voordat u deze les voltooit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Met samen voegingen kunt u gegevens uit meerdere tabellen analyseren, in dezelfde query. De rijen van twee gegevens sets worden samengevoegd door de waarden van de opgegeven kolommen te vergelijken.


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

In dit voor beeld worden de eerste gegevensset filters voor alle aanmeldings gebeurtenissen. Deze is gekoppeld aan een tweede gegevensset die voor alle afmeldings gebeurtenissen filtert. De geprojecteerde kolommen zijn _computer_, _account_, _TargetLogonId_en _TimeGenerated_. De gegevens sets worden gecorreleerd door een gedeelde kolom _TargetLogonId_. De uitvoer is één record per correlatie, die zowel de aanmeld-als de afmeldings tijd heeft.

Als beide gegevens sets kolommen met dezelfde namen bevatten, krijgen de kolommen van de gegevensset aan de rechter kant een index nummer, dus in dit voor beeld worden de resultaten _TargetLogonId_ weer gegeven met waarden uit de tabel aan de linkerkant en _TargetLogonId1_ met waarden uit de tabel aan de rechter kant. In dit geval is de tweede kolom _TargetLogonId1_ verwijderd met behulp van de operator `project-away`.

> [!NOTE]
> U kunt de prestaties verbeteren door alleen de relevante kolommen van de gekoppelde gegevens sets te gebruiken met behulp van de operator `project`.


Gebruik de volgende syntaxis om twee gegevens sets samen te voegen en de gekoppelde sleutel heeft een andere naam dan de twee tabellen:
```
Table1
| join ( Table2 ) 
on $left.key1 == $right.key2
```

## <a name="lookup-tables"></a>Opzoek tabellen
Bij het gebruik van samen voegingen wordt gebruikgemaakt van een statische toewijzing van waarden met behulp van `datatable` die u kan helpen bij het transformeren van de resultaten naar een te preseer bare manier. Als u bijvoorbeeld de gegevens van de beveiligings gebeurtenis wilt verrijken met de naam van de gebeurtenis voor elke gebeurtenis-ID.

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

![Samen voegen met een DataTable](media/joins/dim-table.png)

## <a name="join-kinds"></a>Typen samen voegen
Geef het type samen voeging met het argument _kind_ op. Elk type voert een andere overeenkomst uit tussen de records van de opgegeven tabellen, zoals beschreven in de volgende tabel.

| Type samen voeging | Beschrijving |
|:---|:---|
| innerunique | Dit is de standaard modus voor samen voegen. Eerst worden de waarden van de overeenkomende kolom in de linkertabel gevonden en worden dubbele waarden verwijderd.  Vervolgens wordt de reeks unieke waarden vergeleken met de rechter tabel. |
| wend | Alleen overeenkomende records in beide tabellen zijn opgenomen in de resultaten. |
| leftouter | Alle records in de linkertabel en overeenkomende records in de rechter tabel zijn opgenomen in de resultaten. Niet-overeenkomende uitvoer eigenschappen bevatten Null-waarden.  |
| leftanti | Records aan de linkerkant die geen overeenkomsten van rechts hebben, worden opgenomen in de resultaten. De resultaten tabel bevat alleen kolommen uit de linkertabel. |
| leftsemi | Records aan de linkerkant die aan de rechter kant overeenkomen, worden opgenomen in de resultaten. De resultaten tabel bevat alleen kolommen uit de linkertabel. |


## <a name="best-practices"></a>Aanbevolen procedures

Houd rekening met de volgende punten voor optimale prestaties:

- Gebruik een tijd filter voor elke tabel om de records te verminderen die moeten worden geëvalueerd voor de koppeling.
- Gebruik `where` en `project` om het aantal rijen en kolommen in de invoer tabellen te verminderen vóór de koppeling.
- Als de ene tabel altijd kleiner is dan de andere, gebruikt u deze als de linkerkant van de koppeling.


## <a name="next-steps"></a>Volgende stappen
Zie andere lessen voor het gebruik van Azure Monitor-logboek query's:

- [Teken reeks bewerkingen](string-operations.md)
- [Aggregatie functies](aggregations.md)
- [Geavanceerde aggregaties](advanced-aggregations.md)
- [JSON en gegevens structuren](json-data-structures.md)
- [Geavanceerde query's schrijven](advanced-query-writing.md)
- [Diagrammen](charts.md)