---
title: Azure Cosmos DB query taal aanmelden
description: Meer informatie over de SQL-functie voor logboek registratie in Azure Cosmos DB om de natuurlijke logaritme van de opgegeven numerieke expressie te retour neren
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9213ef03f383dec7109652246411fac154b4a7f9
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78302505"
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
  
  De natuurlijke logaritme is de logaritme van het grondtal **e**, waarbij **e** een Irrational-constante is die ongeveer gelijk is aan 2,718281828.  
  
  De natuurlijke logaritme van de exponentiële van een getal is het aantal zelf: LOG (EXP (n)) = n. En de exponentiële waarde van de natuurlijke logaritme van een getal is het aantal zelf: EXP (logboek (n)) = n.  
  
## <a name="examples"></a>Voorbeelden
  
  Het volgende voorbeeld wordt een variabele gedeclareerd en retourneert de logaritme-waarde van de opgegeven variabele (10).  
  
```sql
SELECT LOG(10) AS log  
```  
  
 Hier volgt de resultatenset.  
  
```json
[{log: 2.3025850929940459}]  
```  
  
 In het volgende voor beeld wordt de `LOG` voor de exponent van een getal berekend.  
  
```sql
SELECT EXP(LOG(10)) AS expLog  
```  
  
 Hier volgt de resultatenset.  
  
```json
[{expLog: 10.000000000000002}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
