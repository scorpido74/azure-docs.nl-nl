---
title: StringToNull in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie StringToNull in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7e17547f88465103b61eabec04978ea806ffa2c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296436"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
 Retourneert expressie vertaald naar null. Als expressie niet kan worden vertaald, wordt niet gedefinieerd geretourneerd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een tekenreeksexpressie die moet worden ontleed als een null-expressie.
  
## <a name="return-types"></a>Retourtypen
  
  Retourneert een null-expressie of niet gedefinieerd.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende `StringToNull` voorbeeld ziet u hoe zich zich gedraagt voor verschillende typen. 

Hieronder volgen voorbeelden met geldige invoer.

 Witruimte is alleen toegestaan voor of na "null".

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

Hieronder volgen voorbeelden met ongeldige invoer.

Null is hoofdlettergevoelig en moet worden geschreven met alle kleine letters, d.w.z. "null".

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{}]
```  

De doorgegeven expressie wordt ontleed als een null-expressie; deze ingangen evalueren niet om null te typen en dus ongedefinieerd terug te keren.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
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
