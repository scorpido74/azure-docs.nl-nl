---
title: COMPENSATIELIMIET-component in Azure Cosmos DB
description: Meer informatie over het gebruik van de clausule COMPENSATIELIMIET om bepaalde waarden over te slaan en te nemen bij het query's in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771575"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>COMPENSATIELIMIET-component in Azure Cosmos DB

De clausule OVERCOMPENSATIELIMIET is een optionele clausule om over te slaan en vervolgens een aantal waarden uit de query te halen. De telling VAN DE COMPENSATIE en het aantal LIMIET zijn vereist in de component COMPENSATIELIMIET.

Wanneer OFFSET LIMIT wordt gebruikt in combinatie met een ORDER BY-component, wordt de resultaatset geproduceerd door het overslaan te doen en de bestelde waarden over te nemen. Als er geen ORDER BY-clausule wordt gebruikt, zal dit resulteren in een deterministische volgorde van waarden.

## <a name="syntax"></a>Syntaxis
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argumenten

- `<offset_amount>`

   Hiermee geeft u het gehele aantal items op dat de queryresultaten moeten overslaan.

- `<limit_amount>`
  
   Hiermee geeft u het gehele aantal items op dat de queryresultaten moeten bevatten

## <a name="remarks"></a>Opmerkingen
  
  Zowel `OFFSET` de telling `LIMIT` als de `OFFSET LIMIT` telling zijn vereist in de clausule. Als een `ORDER BY` optionele clausule wordt gebruikt, wordt de resultaatset geproduceerd door de bestelde waarden over te slaan. Anders retourneert de query een vaste volgorde van waarden.

  De RU-kosten van `OFFSET LIMIT` een query met zal toenemen naarmate het aantal voorwaarden wordt gecompenseerd toeneemt. Voor query's met meerdere pagina's met resultaten raden we meestal aan om vervolgtokens te gebruiken. Vervolgtokens zijn een "bladwijzer" voor de plaats waar de query later kan worden hervat. Als u `OFFSET LIMIT`gebruikt, is er geen "bladwijzer". Als u de volgende pagina van de query wilt retourneren, moet u vanaf het begin beginnen.
  
  U moet `OFFSET LIMIT` gebruiken voor aanvragen wanneer u documenten volledig wilt overslaan en clientresources wilt opslaan. U moet bijvoorbeeld `OFFSET LIMIT` gebruiken als u wilt doorgaan naar het 1000e queryresultaat en de resultaten 1 tot en met 999 niet hoeft te bekijken. Op de backend laadt `OFFSET LIMIT` u nog steeds elk document, inclusief documenten die worden overgeslagen. Het prestatievoordeel is een besparing in de bronnen van klanten door het vermijden van het verwerken van documenten die niet nodig zijn.

## <a name="examples"></a>Voorbeelden

Hier vindt u bijvoorbeeld een query die de eerste waarde overslaat en de tweede waarde (in volgorde van de naam van de inwoner):

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

Hier is een query die de eerste waarde overslaat en de tweede waarde retourneert (zonder te bestellen):

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

- [Slag](sql-query-getting-started.md)
- [SELECT-component](sql-query-select.md)
- [ORDER BY-clausule](sql-query-order-by.md)
