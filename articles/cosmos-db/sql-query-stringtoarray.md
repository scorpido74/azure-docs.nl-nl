---
title: StringToArray in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie StringToArray in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 2d1f90da50950ac6ff4f87ffe96ebad9f3d811cc
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349279"
---
# <a name="stringtoarray-azure-cosmos-db"></a>StringToArray (Azure Cosmos DB)
 Retourneert een expressie die is vertaald naar een matrix. Als expressie niet kan worden vertaald, retourneert ongedefinieerd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql  
StringToArray(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie die moet worden geparseerd als een JSON-matrix expressie. 
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een matrix expressie of is niet gedefinieerd. 
  
## <a name="remarks"></a>Opmerkingen
  Geneste teken reeks waarden moeten worden geschreven met dubbele aanhalings tekens die een geldige JSON moeten hebben. Zie [JSON.org](https://json.org/) voor meer informatie over de JSON-indeling.
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe `StringToArray` zich gedraagt in verschillende typen. 
  
 Hier volgen enkele voor beelden met geldige invoer.

```sql
SELECT 
    StringToArray('[]') AS a1, 
    StringToArray("[1,2,3]") AS a2,
    StringToArray("[\"str\",2,3]") AS a3,
    StringToArray('[["5","6","7"],["8"],["9"]]') AS a4,
    StringToArray('[1,2,3, "[4,5,6]",[7,8]]') AS a5
```

Hier volgt de resultatenset.

```json
[{"a1": [], "a2": [1,2,3], "a3": ["str",2,3], "a4": [["5","6","7"],["8"],["9"]], "a5": [1,2,3,"[4,5,6]",[7,8]]}]
```

Hier volgt een voor beeld van ongeldige invoer. 
   
 Enkele aanhalings tekens in de matrix zijn geen geldige JSON.
Hoewel ze wel geldig zijn binnen een query, worden ze niet geparseerd naar geldige matrices. Teken reeksen binnen de teken reeks van de matrix moeten worden voorafgegaan door ' [\\ ' \\ '] of de omliggende aanhaling moet één ' ['] ' bevatten.

```sql
SELECT
    StringToArray("['5','6','7']")
```

Hier volgt de resultatenset.

```json
[{}]
```

Hier volgen enkele voor beelden van ongeldige invoer.
   
 De door gegeven expressie wordt geparseerd als een JSON-matrix. de volgende evaluaties zijn niet van het type matrix en daarom wordt ongedefinieerd geretourneerd.
   
```sql
SELECT
    StringToArray("["),
    StringToArray("1"),
    StringToArray(NaN),
    StringToArray(false),
    StringToArray(undefined)
```

Hier volgt de resultatenset.

```json
[{}]
```

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
