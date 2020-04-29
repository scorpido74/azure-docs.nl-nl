---
title: SQL-query operators voor Azure Cosmos DB
description: Meer informatie over SQL-Opera tors zoals gelijkheid, vergelijking en logische Opera tors die door Azure Cosmos DB worden ondersteund.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80063560"
---
# <a name="operators-in-azure-cosmos-db"></a>Opera tors in Azure Cosmos DB

In dit artikel vindt u meer informatie over de verschillende Opera tors die door Azure Cosmos DB worden ondersteund.

## <a name="equality-and-comparison-operators"></a>Gelijkheids-en vergelijkings operators

In de volgende tabel ziet het resultaat van gelijkheidsvergelijkingen in de SQL-API voor elk paar JSON-typen.

| **Op** | **Undefined** | **Null** | **Booleaans** | **Telwoord** | **Tekenreeks** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **OK** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Booleaans** | Undefined | Undefined | **OK** | Undefined | Undefined | Undefined | Undefined |
| **Telwoord** | Undefined | Undefined | Undefined | **OK** | Undefined | Undefined | Undefined |
| **Tekenreeks** | Undefined | Undefined | Undefined | Undefined | **OK** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **OK** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **OK** |

Voor vergelijkings operatoren `>`zoals `>=`, `!=` `<`,, en `<=`wordt vergelijking tussen typen of tussen twee objecten of matrices gegenereerd `Undefined`.  

Als het resultaat van de scalaire expressie `Undefined`is, wordt het item niet opgenomen in het resultaat `Undefined` omdat het `true`niet gelijk is aan.

## <a name="logical-and-or-and-not-operators"></a>Logische operatoren (EN, OF en NIET)

Logische operatoren worden uitgevoerd op Booleaanse waarden. In de volgende tabellen ziet u de logische waarheids tabellen voor deze opera tors:

**Operator OF**

Retourneert `true` wanneer een van de voor waarden `true`is.

|  | **True** | **False** | **Undefined** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **False** |True |False |Undefined |
| **Undefined** |True |Undefined |Undefined |

**Operator EN**

Retourneert `true` wanneer beide expressies zijn `true`.

|  | **True** | **False** | **Undefined** |
| --- | --- | --- | --- |
| **True** |True |False |Undefined |
| **False** |False |False |False |
| **Undefined** |Undefined |False |Undefined |

**Operator NIET**

Keert de waarde van een booleaanse expressie om.

|  | **TEN** |
| --- | --- |
| **True** |False |
| **False** |True |
| **Undefined** |Undefined |

**Operator prioriteit**

De logische Opera `OR`tors `AND`, en `NOT` hebben het prioriteits niveau dat hieronder wordt weer gegeven:

| **And** | **Prioriteiten** |
| --- | --- |
| **TEN** |1 |
| **MAAR** |2 |
| **OF** |3 |

## <a name="-operator"></a>*-operator

De speciale operator * projecteert het hele item in de vorm van. Als u deze operator gebruikt, moet dit het enige geprojecteerde veld zijn. Een query zoals `SELECT * FROM Families f` is geldig, maar `SELECT VALUE * FROM Families f` `SELECT *, f.id FROM Families f` is niet geldig.

## <a name="-and--operators"></a>? en?? operatoren

U kunt de Opera tors van ternaire (?) en Coalesce (?) gebruiken om voorwaardelijke expressies te maken, zoals in programmeer talen zoals C# en Java script.

U kunt de ? operator voor het maken van nieuwe JSON-eigenschappen. Met de volgende query worden bijvoorbeeld de classificatie niveaus geclassificeerd in `elementary` of: `other`

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

U kunt ook aanroepen nesten naar de? , zoals in de volgende query: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Net als bij andere query operators, de? de operator sluit items uit als de eigenschappen van de verwijzing ontbreken of de typen die worden vergeleken, verschillen.

Gebruikt u de? operator om efficiënt te controleren op een eigenschap in een item bij het uitvoeren van query's op semi-gestructureerde of gegevens van een gemengd type. De volgende query retourneert bijvoorbeeld, indien `lastName` aanwezig, of `surname` als `lastName` deze niet aanwezig is.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Volgende stappen

- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Trefwoorden](sql-query-keywords.md)
- [SELECT-component](sql-query-select.md)
