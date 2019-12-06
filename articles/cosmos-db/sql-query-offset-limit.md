---
title: Component OFFSET LIMIT in Azure Cosmos DB
description: Meer informatie over het gebruik van de component OFFSET LIMIT voor het overs Laan en het uitvoeren van bepaalde waarden bij het uitvoeren van query's in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 68515c51862ada0b1aa794c09b3a6730504a57ee
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873247"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>Component OFFSET LIMIT in Azure Cosmos DB

De component OFFSET LIMIT is een optionele component waarmee u een aantal waarden uit de query kunt overs Laan. Het aantal OFFSET en het aantal LIMIETen zijn vereist in de component OFFSET LIMIT.

Wanneer de OFFSET limiet wordt gebruikt in combi natie met een ORDER BY-component, wordt de resultatenset gemaakt door overs Laan en de geordende waarden uit te voeren. Als er geen ORDER BY-component wordt gebruikt, resulteert dit in een deterministische volg orde van waarden.

## <a name="syntax"></a>Syntaxis
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumenten

- `<offset_amount>`

   Hiermee geeft u het aantal items op dat door de query resultaten moet worden overgeslagen.

- `<limit_amount>`
  
   Hiermee geeft u het gehele getal op van items die de query resultaten moeten bevatten

## <a name="remarks"></a>Opmerkingen
  
  Zowel het aantal VERSCHUIVINGen als het aantal LIMIETen zijn vereist in de component OFFSET LIMIT. Als er een optionele `ORDER BY`-component wordt gebruikt, wordt de resultatenset gemaakt door de overs laan van de geordende waarden over te slaan. Anders retourneert de query een vaste volg orde van waarden. Momenteel wordt deze component alleen ondersteund voor query's binnen één partitie, maar niet voor query's met meerdere partities.

## <a name="examples"></a>Voorbeelden

Hier volgt een query waarmee de eerste waarde wordt overgeslagen en de tweede waarde wordt geretourneerd (op volg orde van de naam van de residente plaats):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

U ziet deze uitvoer:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Hier volgt een query waarmee de eerste waarde wordt overgeslagen en de tweede waarde wordt geretourneerd (zonder volg orde):

```sql
   SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

U ziet deze uitvoer:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag](sql-query-getting-started.md)
- [SELECT-component](sql-query-select.md)
- [ORDER BY-component](sql-query-order-by.md)
