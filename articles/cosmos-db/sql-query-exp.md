---
title: EXP in Azure Cosmos DB-querytaal
description: Meer informatie over de SQL-systeemfunctie Exponent (EXP) in Azure Cosmos DB om de exponentiële waarde van de opgegeven numerieke expressie te retourneren
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 76d614264124e1ce4138663b702ff6d899b3aa4e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873315"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
 Geeft als resultaat de exponentiële waarde van de opgegeven numerieke expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="remarks"></a>Opmerkingen
  
  De constante **e** (2.718281...), is de basis van natuurlijke logaritmes.  
  
  De exponent van een getal is de constante **e** verhoogd tot de kracht van het getal. EXP(1.0) = e^1.0 = 2,71828182845905 en EXP(10) = e^10 = 22026.4657948067.  
  
  De exponentiële van de natuurlijke logaritme van een getal is het getal zelf: EXP (LOG (n)) = n. En de natuurlijke logaritme van de exponentiële van een getal is het getal zelf: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld wordt een variabele gedeclareerd en wordt de exponentiële waarde van de opgegeven variabele (10) geretourneerd.  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 In het volgende voorbeeld wordt de exponentiële waarde van de natuurlijke logaritme van 20 en de natuurlijke logaritme van de exponentiële van 20 geretourneerd. Omdat deze functies omgekeerde functies van elkaar zijn, is de retourwaarde met afronding voor floating point math in beide gevallen 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
