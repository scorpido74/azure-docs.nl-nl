---
title: Azure Cosmos DB query taal omkeren
description: Meer informatie over de functie voor het terugdraaien van SQL-systemen in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: a22e1c8a5f4350bd2f966ee48f96368c648a4a1e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78302165"
---
# <a name="reverse-azure-cosmos-db"></a>OMGEKEERD (Azure Cosmos DB)
 Retourneert een tekenreekswaarde in de omgekeerde volgorde.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
REVERSE(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een teken reeks expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u kunt gebruiken `REVERSE` in een query.  
  
```sql
SELECT REVERSE("Abc") AS reverse  
```  
  
 Dit is de resultatenset.  
  
```json
[{"reverse": "cbA"}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
