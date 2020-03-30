---
title: ST_INTERSECTS in Azure Cosmos DB-querytaal
description: Meer informatie over sql-systeemfunctie ST_INTERSECTS in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f3c3878956b90ffb45556ed819046af9eb7618f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303134"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Azure Cosmos DB)
 Geeft als resultaat een Booleaanse expressie die aangeeft of het object GeoJSON (Punt, Veelhoek of Lijntekenreeks) dat in het eerste argument is opgegeven, de GeoJSON (Punt, Veelhoek of Lijntekenreeks) in het tweede argument kruist.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*spatial_expr*  
   Is een objectexpressie geoJSON-punt, veelhoek of linestring.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een Booleaanse waarde.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld ziet u hoe u alle gebieden vinden die met de opgegeven veelhoek kruisen.  
  
```sql
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{ "id": "IntersectingPolygon" }]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie zal profiteren van een [georuimtelijke index](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Volgende stappen

- [Ruimtelijke functies Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
