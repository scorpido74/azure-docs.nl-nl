---
title: Component OFFSET LIMIT in Azure Cosmos DB
description: Meer informatie over het gebruik van de component OFFSET LIMIT voor het overs Laan en het uitvoeren van bepaalde waarden bij het uitvoeren van query's in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: a8df220be211c3c8d8cdeab8a8aebfd35e77ebf8
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732583"
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
  
  Zowel het aantal VERSCHUIVINGen als het aantal LIMIETen zijn vereist in de component OFFSET LIMIT. Als er een optionele `ORDER BY`-component wordt gebruikt, wordt de resultatenset gemaakt door de overs laan van de geordende waarden over te slaan. Anders retourneert de query een vaste volg orde van waarden. Deze component wordt nu ondersteund voor query's binnen één partitie, evenals query's voor meerdere partities.

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
