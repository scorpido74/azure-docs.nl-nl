---
title: Azure Cosmos DB query taal aanmelden
description: Meer informatie over de functie voor SQL-systeem functies in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 16f2321eb48afacbc9636b5c0588b1ea3a01a284
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349734"
---
# <a name="log-azure-cosmos-db"></a>LOGBOEK (Azure Cosmos DB)
 Retourneert de natuurlijke logaritme van de opgegeven numerieke expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Een numerieke expressie is.  
  
*Baseer*  
   Optionele numerieke argument dat Hiermee stelt u de basis voor de logaritme.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="remarks"></a>Opmerkingen
  
  Standaard retourneert LOG() de natuurlijke logaritme. U kunt het grondtal van de logaritme met een andere waarde wijzigen met behulp van de optionele parameter basis.  
  
  De natuurlijke logaritme is de logaritme voor de base **e**, waarbij **e** is een constante irrational ongeveer gelijk zijn aan 2.718281828.  
  
  De natuurlijke logaritme van de exponentiële waarde van een getal is het getal zelf: LOG (EXP (n)) = n. En de exponentiële waarde van de natuurlijke logaritme van een getal is het getal zelf: EXP (logboek (n)) = n.  
  
## <a name="examples"></a>Voorbeelden
  
  Het volgende voorbeeld wordt een variabele gedeclareerd en retourneert de logaritme-waarde van de opgegeven variabele (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Hier volgt de resultatenset.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 In het volgende voor beeld wordt de `LOG` berekend voor de exponent van een getal.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Hier volgt de resultatenset.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
