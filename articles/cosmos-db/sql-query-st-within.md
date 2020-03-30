---
title: ST_WITHIN in Azure Cosmos DB-querytaal
description: Meer informatie over sql-systeemfunctie ST_WITHIN in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 07a339d82f5e4bea1ea0412a5d5b19522611b54a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78296113"
---
# <a name="st_within-azure-cosmos-db"></a>ST_WITHIN (Azure Cosmos DB)
 Geeft als resultaat een Booleaanse expressie die aangeeft of het object GeoJSON (Punt, Veelhoek of Lijntekenreeks) dat in het eerste argument is opgegeven, in het tweede argument binnen het argument GeoJSON (Punt, Veelhoek of Lijntekenreeks) valt.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ST_WITHIN (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*spatial_expr*  
   Is een objectexpressie geoJSON-punt, veelhoek of linestring.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een Booleaanse waarde.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld ziet u hoe `ST_WITHIN`u alle gezinsdocumenten in een veelhoek vinden met behulp van.  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_WITHIN(f.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{ "id": "WakefieldFamily" }]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie zal profiteren van een [georuimtelijke index](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Volgende stappen

- [Ruimtelijke functies Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
