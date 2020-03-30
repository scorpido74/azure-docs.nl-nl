---
title: Gegevens verwijderen uit Azure Data Explorer
description: In dit artikel worden scenario's voor het verwijderen in Azure Data Explorer beschreven, waaronder verwijderings-, droppings- en retentiegebaseerde verwijderingen.
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: dd0f8740d148a7817bcfe2fbad591ceeb1610d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501408"
---
# <a name="delete-data-from-azure-data-explorer"></a>Gegevens verwijderen uit Azure Data Explorer

Azure Data Explorer ondersteunt verschillende verwijderingsscenario's die in dit artikel worden beschreven. 

## <a name="delete-data-using-the-retention-policy"></a>Gegevens verwijderen met behulp van het bewaarbeleid

Azure Data Explorer verwijdert automatisch gegevens op basis van het [bewaarbeleid](/azure/kusto/management/retentionpolicy). Deze methode is de meest efficiënte en probleemloze manier om gegevens te verwijderen. Stel het bewaarbeleid in op database- of tabelniveau.

Overweeg een database of tabel die is ingesteld voor 90 dagen retentie. Als er slechts 60 dagen aan gegevens nodig zijn, verwijdert u de oudere gegevens als volgt:

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>Gegevens verwijderen door hoeveelheden te laten vallen

[Extent (data shard)](/azure/kusto/management/extents-overview) is de interne structuur waar gegevens worden opgeslagen. Elke omvang kan tot miljoenen records bevatten. De omvang kan individueel of als groep worden verwijderd gebruikend [dalingsomvang(s) bevelen](/azure/kusto/management/extents-commands#drop-extents). 

### <a name="examples"></a>Voorbeelden

U alle rijen in een tabel of slechts een specifieke omvang verwijderen.

* Alle rijen in een tabel verwijderen:

    ```kusto
    .drop extents from TestTable
    ```

* Een specifieke omvang verwijderen:

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>Afzonderlijke rijen verwijderen met zuivering

[Gegevenswissen](/azure/kusto/concepts/data-purge) kan worden gebruikt voor het verwijderen van individuele rijen. Verwijdering is niet onmiddellijk en vereist aanzienlijke systeembronnen. Als zodanig wordt het alleen aangeraden voor nalevingsscenario's.  

