---
title: EXP in Azure Cosmos DB query taal
description: Meer informatie over de SQL-systeem functie exponent (EXP) in Azure Cosmos DB om de exponentiële waarde van de opgegeven numerieke expressie te retour neren
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 76d614264124e1ce4138663b702ff6d899b3aa4e
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873315"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
 Retourneert de exponentiële waarde van de opgegeven numerieke expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Een numerieke expressie is.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="remarks"></a>Opmerkingen
  
  De constante **e** (2.718281...) vormt de basis van natuurlijke logaritmen.  
  
  De exponent van een getal is de constante **e** verheven tot de macht van het getal. Bijvoorbeeld, EXP(1.0) = e ^ 1.0 = 2.71828182845905 en EXP(10) = e ^ 10 = 22026.4657948067.  
  
  De exponentiële waarde van de natuurlijke logaritme van een getal is het aantal zelf: EXP (logboek (n)) = n. En de natuurlijke logaritme van de exponentiële van een getal is het aantal zelf: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Voorbeelden
  
  Het volgende voorbeeld wordt een variabele gedeclareerd en berekent de exponentiële waarde van de opgegeven variabele (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Hier volgt de resultatenset.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 Het volgende voorbeeld retourneert de exponentiële waarde van de natural logarithm van 20 en de natuurlijke logaritme van de exponentiële van 20. Omdat deze functies inverse functies van elkaar zijn, is de geretourneerde waarde met de afronding voor zwevende punt math in beide gevallen 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Hier volgt de resultatenset.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
