---
title: StringToBoolean in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie StringToBoolean in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8719857dca16585a045f8174dbac8df455f38f38
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349268"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
 Retourneert een expressie die is vertaald naar een Booleaanse waarde. Als expressie niet kan worden vertaald, retourneert ongedefinieerd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie die moet worden geparseerd als een Boole-expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een booleaanse expressie of een niet-gedefinieerde waarde.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe `StringToBoolean` zich gedraagt in verschillende typen. 
 
 Hier volgen enkele voor beelden met geldige invoer.

Spatie is alleen toegestaan vóór of na "True"/"false".

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Hier volgt de resultatenset.  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

Hier volgen enkele voor beelden met ongeldige invoer.

 Boole-waarden zijn hoofdletter gevoelig en moeten met alle kleine letters worden geschreven, dat wil zeggen ' True ' en ' false '.

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Hier volgt de resultatenset.  
  
```json
[{}]
``` 

De door gegeven expressie wordt geparseerd als een Boole-expressie. deze invoer kan niet worden geëvalueerd om Booleaanse waarden te typen en retour neren dus niet-gedefinieerd.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Hier volgt de resultatenset.  
  
```json
[{}]
```  

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
