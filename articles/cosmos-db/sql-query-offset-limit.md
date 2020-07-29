---
title: Component OFFSET LIMIT in Azure Cosmos DB
description: Meer informatie over het gebruik van de component OFFSET LIMIT voor het overs Laan en het uitvoeren van bepaalde waarden bij het uitvoeren van query's in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: 3d23676885323e370cee1e9cc9e98c7128faf2e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76771575"
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
  
  Zowel het `OFFSET` aantal als het `LIMIT` aantal is vereist in de `OFFSET LIMIT` component. Als er een optionele `ORDER BY` component wordt gebruikt, wordt de resultatenset gemaakt door de overs laan van de geordende waarden over te slaan. Anders retourneert de query een vaste volg orde van waarden.

  De RU-kosten van een query met `OFFSET LIMIT` worden verhoogd naarmate er meer termen worden gecompenseerd. Voor query's met meerdere pagina's met resultaten raden we u aan om vervolg tokens te gebruiken. Vervolg tokens zijn een ' blad wijzer ' voor de locatie waar de query later kan worden hervat. Als u gebruikt `OFFSET LIMIT` , is er geen blad wijzer. Als u de volgende pagina van de query wilt retour neren, moet u beginnen met het begin.
  
  Gebruik `OFFSET LIMIT` voor gevallen waarin u documenten volledig wilt overs Laan en client bronnen wilt opslaan. U moet bijvoorbeeld gebruiken `OFFSET LIMIT` Als u wilt door gaan naar het 1000th-query resultaat en geen resultaten meer wilt weer geven van 1 tot en met 999. Op de back-end wordt `OFFSET LIMIT` nog steeds elk document geladen, met inbegrip van de bestanden die worden overgeslagen. De prestaties profiteren van een besparing in client bronnen door te voor komen dat documenten worden verwerkt die niet nodig zijn.

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
