---
title: SQL-queryoperatoren voor Azure Cosmos DB
description: Meer informatie over SQL-operators zoals gelijkheid, vergelijking en logische operatoren die worden ondersteund door Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: tisande
ms.openlocfilehash: 8ef41edb687a5df39243880c897d12e83c008ec9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063560"
---
# <a name="operators-in-azure-cosmos-db"></a>Operators in Azure Cosmos DB

In dit artikel worden de verschillende operators beschreven die worden ondersteund door Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Actoren op het gebied van gelijkheid en vergelijking

In de volgende tabel ziet het resultaat van gelijkheidsvergelijkingen in de SQL-API voor elk paar JSON-typen.

| **Op** | **Undefined** | **Null** | **Booleaanse** | **Nummer** | **Tekenreeks** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Null** | Undefined | **OK** | Undefined | Undefined | Undefined | Undefined | Undefined |
| **Booleaanse** | Undefined | Undefined | **OK** | Undefined | Undefined | Undefined | Undefined |
| **Nummer** | Undefined | Undefined | Undefined | **OK** | Undefined | Undefined | Undefined |
| **Tekenreeks** | Undefined | Undefined | Undefined | Undefined | **OK** | Undefined | Undefined |
| **Object** | Undefined | Undefined | Undefined | Undefined | Undefined | **OK** | Undefined |
| **Array** | Undefined | Undefined | Undefined | Undefined | Undefined | Undefined | **OK** |

Voor vergelijkingsoperatoren `>=` `!=`zoals `<` `>`, `<=`, , en , vergelijking tussen `Undefined`typen of tussen twee objecten of arrays produceert .  

Als het resultaat van de `Undefined`scalaire expressie is, wordt `Undefined` het item `true`niet opgenomen in het resultaat, omdat het niet gelijk is .

## <a name="logical-and-or-and-not-operators"></a>Logische operatoren (EN, OF en NIET)

Logische operatoren worden uitgevoerd op Booleaanse waarden. In de volgende tabellen worden de logische waarheidstabellen voor deze operatoren weergegeven:

**Operator OF**

Retourneert `true` wanneer een `true`van de voorwaarden is .

|  | **True** | **False** | **Undefined** |
| --- | --- | --- | --- |
| **True** |True |True |True |
| **False** |True |False |Undefined |
| **Undefined** |True |Undefined |Undefined |

**Operator EN**

Retourneert `true` wanneer `true`beide expressies .

|  | **True** | **False** | **Undefined** |
| --- | --- | --- | --- |
| **True** |True |False |Undefined |
| **False** |False |False |False |
| **Undefined** |Undefined |False |Undefined |

**Operator NIET**

Hiermee keert u de waarde van een Booleaanse expressie om.

|  | **Niet** |
| --- | --- |
| **True** |False |
| **False** |True |
| **Undefined** |Undefined |

**Operatorprioriteit**

De logische `OR` `AND`operatoren `NOT` , en hebben het voorrangsniveau hieronder weergegeven:

| **Operator** | **Prioriteit** |
| --- | --- |
| **Niet** |1 |
| **En** |2 |
| **Of** |3 |

## <a name="-operator"></a>* operator

De speciale operator * projecteert het hele item zoals het is. Als u deze operator gebruikt, moet dit het enige geprojecteerde veld zijn. Een query `SELECT * FROM Families f` als is `SELECT VALUE * FROM Families f` `SELECT *, f.id FROM Families f` geldig, maar niet geldig.

## <a name="-and--operators"></a>? En?? operatoren

U de beheerders Ternary (?) en Coalesce (??) gebruiken om voorwaardelijke expressies te bouwen, zoals in programmeertalen zoals C# en JavaScript.

U kunt de ? operator om nieuwe JSON-eigenschappen on the fly te bouwen. In de volgende query worden bijvoorbeeld `elementary` cijferniveaus ingedeeld in of: `other`

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

U ook nesten oproepen naar de? operator, zoals in de volgende query: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Net als bij andere queryoperatoren, de ? als de operator items uitsluit als de waarnaar wordt verwezen of de typen die worden vergeleken, verschillen.

Gebruik de ?? operator om efficiënt te controleren op een eigenschap in een item bij het opvragen tegen semi-gestructureerde of gemengde gegevens. De volgende query wordt `lastName` bijvoorbeeld geretourneerd `surname` `lastName` als deze aanwezig is of als deze niet aanwezig is.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Volgende stappen

- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Trefwoorden](sql-query-keywords.md)
- [SELECT-component](sql-query-select.md)
