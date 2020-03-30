---
title: StringToBoolean in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie StringToBoolean in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ce11db91eff51e669f0917fbf34b1d560d0e9f07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296538"
---
# <a name="stringtoboolean-azure-cosmos-db"></a>StringToBoolean (Azure Cosmos DB)
 Retourneert expressie vertaald naar een Booleaan. Als expressie niet kan worden vertaald, wordt niet gedefinieerd geretourneerd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
StringToBoolean(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een tekenreeksexpressie die moet worden ontleed als een Booleaanse expressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een Booleaanse expressie of niet gedefinieerd.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende `StringToBoolean` voorbeeld ziet u hoe zich zich gedraagt voor verschillende typen. 
 
 Hieronder volgen voorbeelden met geldige invoer.

Witruimte is alleen toegestaan voor of na "waar"/"false".

```sql
SELECT 
    StringToBoolean("true") AS b1, 
    StringToBoolean("    false") AS b2,
    StringToBoolean("false    ") AS b3
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"b1": true, "b2": false, "b3": false}]
```  

Hieronder volgen voorbeelden met ongeldige invoer.

 Booleaanse zijn hoofdlettergevoelig en moeten worden geschreven met alle kleine letters, d.w.z. "waar" en "vals".

```sql
SELECT 
    StringToBoolean("TRUE"),
    StringToBoolean("False")
```  

Hier is het resultaat ingesteld.  
  
```json
[{}]
``` 

De doorgegeven expressie wordt ontleed als een Booleaanse expressie; deze ingangen evalueren niet om Booleaanse te typen en keren dus ongedefinieerd terug.

```sql
SELECT 
    StringToBoolean("null"),
    StringToBoolean(undefined),
    StringToBoolean(NaN), 
    StringToBoolean(false), 
    StringToBoolean(true)
```  

Hier is het resultaat ingesteld.  
  
```json
[{}]
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Tekenreeksfuncties Azure Cosmos DB](sql-query-string-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
