---
title: StringToNumber in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie StringToNumber in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 5ca8d0c4a6d244823dda6f0f79a3cf5c743a12a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296419"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
 Retourneert expressie die is vertaald naar een getal. Als expressie niet kan worden vertaald, wordt niet gedefinieerd geretourneerd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een tekenreeksexpressie die moet worden ontleed als een JSON-getalexpressie. Getallen in JSON moeten een geheel getal of een zwevend punt zijn. Zie [json.org](https://json.org/)  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een getalexpressie of niet gedefinieerd.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende `StringToNumber` voorbeeld ziet u hoe zich zich gedraagt voor verschillende typen. 

Witruimte is alleen toegestaan voor of na het nummer.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Hier is het resultaat ingesteld.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

In JSON moet een geldig getal een geheel getal of een zwevend puntgetal zijn.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Hier is het resultaat ingesteld.  
  
```json
{{}}
```  

De doorgegeven expressie wordt ontleed als een getalexpressie; deze ingangen evalueren niet om getal te typen en keren dus ongedefinieerd terug. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Hier is het resultaat ingesteld.  
  
```json
{{}}
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Tekenreeksfuncties Azure Cosmos DB](sql-query-string-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
