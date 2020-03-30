---
title: Functies in Azure Monitor-logboekquery's | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u functies gebruikt om een query aan te roepen vanuit een andere logboekquery in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2018
ms.openlocfilehash: 7d94e53abbe8f4d2953729aa2363c3906ce94f74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670216"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Functies gebruiken in azure monitor-logboekquery's

Als u een logboekquery met een andere query wilt gebruiken, u deze opslaan als functie. Hiermee u complexe query's vereenvoudigen door ze in onderdelen op te splitsen en u algemene code opnieuw gebruiken met meerdere query's.

## <a name="create-a-function"></a>Een functie maken

Maak een functie met Log Analytics in de Azure-portal door op **Opslaan te** klikken en vervolgens de informatie in de volgende tabel op te geven.

| Instelling | Beschrijving |
|:---|:---|
| Name           | Weergavenaam voor de query in **Query explorer**. |
| Opslaan als        | Functie |
| Functiealias | Korte naam om de functie in andere query's te gebruiken. Mag geen spaties bevatten en moet uniek zijn. |
| Categorie       | Een categorie om opgeslagen query's en functies te ordenen in **Query explorer**. |

> [!NOTE]
> Een functie in Azure Monitor kan geen andere functie bevatten.




## <a name="use-a-function"></a>Een functie gebruiken
Gebruik een functie door de alias in een andere query op te nemen. Het kan worden gebruikt zoals elke andere tafel.

## <a name="example"></a>Voorbeeld
De volgende voorbeeldquery retourneert alle ontbrekende beveiligingsupdates die in de laatste dag zijn gerapporteerd. Sla deze query op als functie met de alias _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Maak een andere query en verwijs naar de _security_updates_last_day_ functie om te zoeken naar SQL-gerelateerde benodigde beveiligingsupdates.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Volgende stappen
Bekijk andere lessen voor het schrijven van Azure Monitor-logboekquery's:

- [Tekenreeksbewerkingen](string-operations.md)
- [Datum- en tijdbewerkingen](datetime-operations.md)
- [Aggregatiefuncties](aggregations.md)
- [Geavanceerde aggregaties](advanced-aggregations.md)
- [JSON en gegevensstructuren](json-data-structures.md)
- [Joins](joins.md)
- [Grafieken](charts.md)
