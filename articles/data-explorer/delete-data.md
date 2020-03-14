---
title: Gegevens verwijderen uit Azure Data Explorer
description: In dit artikel wordt beschreven hoe u de verwijderings scenario's in azure Data Explorer, inclusief opschonen, neerzet gebieden en op basis van Bewaar termijnen verwijdert.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 681cfd71d2666630b192935d66ba32eaf16c92de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204610"
---
# <a name="delete-data-from-azure-data-explorer"></a>Gegevens verwijderen uit Azure Data Explorer

Azure Data Explorer ondersteunt diverse verwijderings scenario's die in dit artikel worden beschreven. 

## <a name="delete-data-using-the-retention-policy"></a>Gegevens verwijderen met het Bewaar beleid

Azure Data Explorer verwijdert automatisch gegevens op basis van het [Bewaar beleid](/azure/kusto/management/retentionpolicy). Deze methode is de meest efficiënte en gedoe bare manier om gegevens te verwijderen. Stel het Bewaar beleid in op het niveau van de data base of tabel.

Houd rekening met een Data Base of tabel die is ingesteld op 90 dagen retentie. Als er slechts 60 dagen aan gegevens nodig zijn, verwijdert u de oudere gegevens als volgt:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Gegevens verwijderen door gebieden neer te zetten

[Mate (Data Shard)](/azure/kusto/management/extents-overview) is de interne structuur waarin gegevens worden opgeslagen. Elk gebied kan Maxi maal miljoenen records bevatten. Gebieden kunnen afzonderlijk of als een groep worden verwijderd met behulp van de opdrachten voor het [neerzet gebied (en)](/azure/kusto/management/extents-commands#drop-extents). 

### <a name="examples"></a>Voorbeelden

U kunt alle rijen in een tabel of alleen een bepaalde grootte verwijderen.

* Alle rijen in een tabel verwijderen:

    ```kusto
    .drop extents from TestTable
    ```

* Een specifieke omvang verwijderen:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Afzonderlijke rijen verwijderen met leegmaken

Het [opschonen van gegevens](/azure/kusto/management/data-purge) kan worden gebruikt voor het verwijderen van afzonderlijke rijen. Verwijdering is niet onmiddellijk en vereist aanzienlijke systeem bronnen. Dit wordt alleen aanbevolen voor nalevings scenario's.  

