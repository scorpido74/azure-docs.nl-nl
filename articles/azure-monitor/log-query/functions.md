---
title: Functies in Azure Monitor-logboek query's | Microsoft Docs
description: In dit artikel wordt beschreven hoe u functies gebruikt voor het aanroepen van een query vanuit een andere logboek query in Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: bwren
ms.openlocfilehash: 75beb7b66863efd2fb3679f034a3663dca4a6d2f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076706"
---
# <a name="using-functions-in-azure-monitor-log-queries"></a>Functies in Azure Monitor-logboek query's gebruiken

Als u een logboek query met een andere query wilt gebruiken, kunt u deze opslaan als een functie. Zo kunt u complexe query's vereenvoudigen door ze te splitsen in onderdelen en kunt u algemene code gebruiken met meerdere query's.

## <a name="create-a-function"></a>Een functie maken

Maak een functie met Log Analytics in het Azure Portal door op **Opslaan** te klikken en vervolgens de informatie in de volgende tabel op te geven.

| Instelling | Description |
|:---|:---|
| Name           | Weergave naam voor de query in **query Explorer**. |
| Opslaan als        | Function |
| Functiealias | Korte naam voor het gebruik van de functie in andere query's. Mag geen spaties bevatten en moet uniek zijn. |
| Categorie       | Een categorie voor het ordenen van opgeslagen query's en functies in **query Explorer**. |

> [!NOTE]
> Een functie in Azure Monitor kan geen andere functie bevatten.




## <a name="use-a-function"></a>Een functie gebruiken
Gebruik een functie door de bijbehorende alias in een andere query op te nemen. Het kan worden gebruikt als elke andere tabel.

## <a name="example"></a>Voorbeeld
De volgende voorbeeld query retourneert alle ontbrekende beveiligings updates die in de afgelopen dag zijn gerapporteerd. Sla deze query op als een functie met de alias _security_updates_last_day_. 

```Kusto
Update
| where TimeGenerated > ago(1d) 
| where Classification == "Security Updates" 
| where UpdateState == "Needed"
```

Maak een andere query en Raadpleeg de functie _security_updates_last_day_ om te zoeken naar aan SQL gerelateerde vereiste beveiligings updates.

```Kusto
security_updates_last_day | where Title contains "SQL"
```

## <a name="next-steps"></a>Volgende stappen
Zie andere lessen voor het schrijven van Azure Monitor logboek query's:

- [Teken reeks bewerkingen](string-operations.md)
- [Datum-en tijd bewerkingen](datetime-operations.md)
- [Aggregatie functies](aggregations.md)
- [Geavanceerde aggregaties](advanced-aggregations.md)
- [JSON en gegevens structuren](json-data-structures.md)
- [Joins](joins.md)
- [Diagrammen](charts.md)
