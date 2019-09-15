---
title: SQL-query operators voor Azure Cosmos DB
description: Meer informatie over SQL-Opera tors voor Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mjbrown
ms.openlocfilehash: 899355ad7331a3df8cd5d647a573dc15e3a0bb14
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003382"
---
# <a name="operators-in-azure-cosmos-db"></a>Opera tors in Azure Cosmos DB

In dit artikel vindt u meer informatie over de verschillende Opera tors die door Azure Cosmos DB worden ondersteund.

## <a name="equality-and-comparison-operators"></a>Gelijkheids-en vergelijkings operators

In de volgende tabel ziet het resultaat van gelijkheidsvergelijkingen in de SQL-API voor elk paar JSON-typen.

| **Op** | **Undefined** | **Null** | **Boolean** | **Number** | **String** | **Object** | **Array** |
|---|---|---|---|---|---|---|---|
| **Undefined** | Niet gedefinieerd | Undefined | Undefined | Undefined | Undefined | Undefined | Niet gedefinieerd |
| **Null** | Niet gedefinieerd | **Ok** | Niet gedefinieerd | Undefined | Undefined | Undefined | Niet gedefinieerd |
| **Boolean** | Niet gedefinieerd | Niet gedefinieerd | **Ok** | Niet gedefinieerd | Undefined | Undefined | Niet gedefinieerd |
| **Number** | Niet gedefinieerd | Undefined | Niet gedefinieerd | **Ok** | Niet gedefinieerd | Undefined | Niet gedefinieerd |
| **String** | Niet gedefinieerd | Undefined | Undefined | Niet gedefinieerd | **Ok** | Niet gedefinieerd | Niet gedefinieerd |
| **Object** | Niet gedefinieerd | Undefined | Undefined | Undefined | Niet gedefinieerd | **Ok** | Niet gedefinieerd |
| **Array** | Niet gedefinieerd | Undefined | Undefined | Undefined | Undefined | Niet gedefinieerd | **Ok** |

Voor vergelijkings operatoren `>`zoals `>=` `!=`, `<`,, en `<=`wordt vergelijking tussen typen of tussen twee objecten of matrices gegenereerd `Undefined`.  

Als het resultaat van de scalaire expressie `Undefined`is, wordt het item niet opgenomen in het resultaat `Undefined` omdat het `true`niet gelijk is aan.

## <a name="logical-and-or-and-not-operators"></a>Logische operatoren (EN, OF en NIET)

Logische operatoren worden uitgevoerd op Booleaanse waarden. In de volgende tabellen ziet u de logische waarheids tabellen voor deze opera tors:

**Operator OF**

| OR | Waar | False | Niet gedefinieerd |
| --- | --- | --- | --- |
| Waar |Waar |Waar |Waar |
| False |Waar |False |Niet gedefinieerd |
| Niet gedefinieerd |Waar |Niet gedefinieerd |Undefined |

**Operator EN**

| EN | Waar | False | Niet gedefinieerd |
| --- | --- | --- | --- |
| Waar |Waar |False |Niet gedefinieerd |
| False |False |False |False |
| Niet gedefinieerd |Niet gedefinieerd |False |Niet gedefinieerd |

**Operator NIET**

| NIET |  |
| --- | --- |
| Waar |False |
| False |Waar |
| Niet gedefinieerd |Niet gedefinieerd |


## <a name="-operator"></a>*-operator

De speciale operator * projecteert het hele item in de vorm van. Als u deze operator gebruikt, moet dit het enige geprojecteerde veld zijn. Een query zoals `SELECT * FROM Families f` is geldig, `SELECT *, f.id FROM Families f` maar `SELECT VALUE * FROM Families f` is niet geldig.

## <a name="-and--operators"></a>? en?? Dienen

U kunt de Opera tors (?) en Coalesce (?) gebruiken om voorwaardelijke expressies te maken, zoals in programmeer talen zoals C# en Java script. 

U kunt de gebruiken? operator voor het maken van nieuwe JSON-eigenschappen. Met de volgende query worden bijvoorbeeld de classificatie niveaus geclassificeerd in `elementary` of: `other`

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
- [trefwoorden](sql-query-keywords.md)
- [SELECT-component](sql-query-select.md)
