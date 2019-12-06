---
title: Query's met para meters in Azure Cosmos DB
description: Meer informatie over hoe SQL-query's met para meters krachtige verwerking en Escapes van gebruikers invoer bieden en voor komen dat gegevens per ongeluk worden blootgesteld via SQL-injectie.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870816"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Query's met para meters in Azure Cosmos DB

Cosmos DB ondersteunt query's met para meters die worden uitgedrukt in de vertrouwde @-notatie. SQL met para meters biedt een robuuste afhandeling en Escape van de invoer van gebruikers en voor komt onopzettelijke bloot stelling van gegevens via SQL-injectie.

## <a name="examples"></a>Voorbeelden

U kunt bijvoorbeeld een query schrijven die `lastName` en `address.state` als para meters gebruikt, en deze uitvoeren voor diverse waarden van `lastName` en `address.state` op basis van gebruikers invoer.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

U kunt deze aanvraag vervolgens naar Cosmos DB verzenden als een geparametriseerde JSON-query, zoals in het volgende:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

In het volgende voor beeld wordt het bovenste argument met een query met para meters ingesteld: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Parameter waarden kunnen bestaan uit een geldige JSON: teken reeksen, getallen, Booleaanse waarden, null, zelfs matrices of geneste JSON. Omdat Cosmos DB schemaloos is, worden para meters niet gevalideerd op basis van elk type.


## <a name="next-steps"></a>Volgende stappen

- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Document gegevens model leren](modeling-data.md)
