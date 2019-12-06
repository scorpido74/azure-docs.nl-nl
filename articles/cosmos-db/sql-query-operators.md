---
title: SQL-query operators voor Azure Cosmos DB
description: Meer informatie over SQL-Opera tors zoals gelijkheid, vergelijking en logische Opera tors die door Azure Cosmos DB worden ondersteund.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: f3efe4bee749f0d3132206ca68a33a60f0e16b81
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870935"
---
# <a name="operators-in-azure-cosmos-db"></a>Opera tors in Azure Cosmos DB

In dit artikel vindt u meer informatie over de verschillende Opera tors die door Azure Cosmos DB worden ondersteund.

## <a name="equality-and-comparison-operators"></a>Gelijkheids-en vergelijkings operators

In de volgende tabel ziet het resultaat van gelijkheidsvergelijkingen in de SQL-API voor elk paar JSON-typen.

| **Op** | **Undefined** | **Null** | **Boolean** | **Number** | **String** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd |
| **Null** | Niet gedefinieerd | **Ok** | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd |
| **Boolean** | Niet gedefinieerd | Niet gedefinieerd | **Ok** | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd |
| **Number** | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | **Ok** | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd |
| **String** | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | **Ok** | Niet gedefinieerd | Niet gedefinieerd |
| **Object** | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | **Ok** | Niet gedefinieerd |
| **Array** | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | Niet gedefinieerd | **Ok** |

Voor vergelijkings operatoren als `>`, `>=`, `!=`, `<`en `<=`, vergelijkt vergelijking tussen typen of tussen twee objecten of matrices `Undefined`.  

Als het resultaat van de scalaire expressie is `Undefined`, wordt het item niet opgenomen in het resultaat omdat `Undefined` niet gelijk is aan `true`.

## <a name="logical-and-or-and-not-operators"></a>Logische operatoren (EN, OF en NIET)

Logische operatoren worden uitgevoerd op Booleaanse waarden. In de volgende tabellen ziet u de logische waarheids tabellen voor deze opera tors:

**Operator OF**

| OF | Waar | Onwaar | Niet gedefinieerd |
| --- | --- | --- | --- |
| Waar |Waar |Waar |Waar |
| Onwaar |Waar |Onwaar |Niet gedefinieerd |
| Niet gedefinieerd |Waar |Niet gedefinieerd |Niet gedefinieerd |

**Operator EN**

| EN | Waar | Onwaar | Niet gedefinieerd |
| --- | --- | --- | --- |
| Waar |Waar |Onwaar |Niet gedefinieerd |
| Onwaar |Onwaar |Onwaar |Onwaar |
| Niet gedefinieerd |Niet gedefinieerd |Onwaar |Niet gedefinieerd |

**Operator NIET**

| NIET |  |
| --- | --- |
| Waar |Onwaar |
| Onwaar |Waar |
| Niet gedefinieerd |Niet gedefinieerd |


## <a name="-operator"></a>*-operator

De speciale operator * projecteert het hele item in de vorm van. Als u deze operator gebruikt, moet dit het enige geprojecteerde veld zijn. Een query zoals `SELECT * FROM Families f` is geldig, maar `SELECT VALUE * FROM Families f` en `SELECT *, f.id FROM Families f` zijn niet geldig.

## <a name="-and--operators"></a>? en?? operatoren

U kunt de Opera tors (?) en Coalesce (?) gebruiken om voorwaardelijke expressies te maken, zoals in programmeer talen zoals C# en Java script. 

U kunt de gebruiken? operator voor het maken van nieuwe JSON-eigenschappen. De volgende query classificeert bijvoorbeeld het kwaliteits niveau in `elementary` of `other`:

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

Gebruikt u de? operator om efficiënt te controleren op een eigenschap in een item bij het uitvoeren van query's op semi-gestructureerde of gegevens van een gemengd type. De volgende query retourneert bijvoorbeeld `lastName` indien aanwezig of `surname` als `lastName` niet aanwezig is.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Volgende stappen

- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [trefwoorden](sql-query-keywords.md)
- [SELECT-component](sql-query-select.md)
