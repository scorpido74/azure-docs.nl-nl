---
title: LOG IN Azure Cosmos DB-querytaal
description: Meer informatie over de functie LOG SQL-systeem in Azure Cosmos DB om de natuurlijke logaritme van de opgegeven numerieke expressie terug te geven
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9213ef03f383dec7109652246411fac154b4a7f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302505"
---
# <a name="log-azure-cosmos-db"></a>LOG (Azure Cosmos DB)
 Geeft als resultaat de natuurlijke logaritme van de opgegeven numerieke expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
LOG (<numeric_expr> [, <base>])  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
*base*  
   Optioneel numeriek argument dat de basis voor de logaritme instelt.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="remarks"></a>Opmerkingen
  
  Log() retourneert standaard de natuurlijke logaritme. U de basis van de logaritme wijzigen in een andere waarde met behulp van de optionele basisparameter.  
  
  De natuurlijke logaritme is de logaritme aan de basis **e**, waar **e** is een irrationele constante ongeveer gelijk aan 2,718281828.  
  
  De natuurlijke logaritme van de exponentiële van een getal is het getal zelf: LOG( EXP( n ) = n. En de exponentiële van de natuurlijke logaritme van een getal is het getal zelf: EXP( LOG( n ) = n.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld wordt een variabele gedeclareerd en wordt de logaritmewaarde van de opgegeven variabele (10) geretourneerd.  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 In het volgende `LOG` voorbeeld wordt de voordeexponent van een getal berekend.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
