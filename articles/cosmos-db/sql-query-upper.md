---
title: BOVENSTE in Azure Cosmos DB query taal
description: Meer informatie over de functie van SQL-systeem in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5129b4fffafb6918f655263cac2f5564635acf36
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303967"
---
# <a name="upper-azure-cosmos-db"></a>UPPER (Azure Cosmos DB)
 Retourneert een tekenreeksexpressie na het converteren van tekens in kleine letters naar hoofdletters.  

De bovenste systeem functie maakt geen gebruik van de index. Als u regel matig niet-hoofdletter gevoelige vergelijkingen wilt maken, kan de functie van het bovenste systeem een aanzienlijke hoeveelheid van RU verbruiken. Als dit het geval is, kunt u, in plaats van de functie voor het bovenste systeem, om gegevens telkens te normaliseren voor vergelijkingen, het hoofdletter gebruik tijdens het invoegen normaliseren. Vervolgens wordt een query zoals SELECT * FROM c waarbij UPPER (c. name) = ' BOB ' gewoon geselecteerd * van c waarbij c.name = ' BOB '.

## <a name="syntax"></a>Syntaxis
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een tekenreeksexpressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u `UPPER` gebruikt in een query  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Hier volgt de resultatenset.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
