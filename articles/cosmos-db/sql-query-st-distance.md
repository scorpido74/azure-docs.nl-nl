---
title: ST_DISTANCE in Azure Cosmos DB-querytaal
description: Meer informatie over sql-systeemfunctie ST_DISTANCE in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/12/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 02844569137a46ea030b2189191b84a9db24ed22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537292"
---
# <a name="st_distance-azure-cosmos-db"></a>ST_DISTANCE (Azure Cosmos DB)
 Geeft als resultaat de afstand tussen de twee expressies GeoJSON Point, Polygon, MultiPolygon of LineString. Zie het artikel [Geospatial en GeoJSON locatiegegevens](sql-query-geospatial-intro.md) voor meer informatie.
  
## <a name="syntax"></a>Syntaxis
  
```sql
ST_DISTANCE (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*spatial_expr*  
   Is een geldige objectexpressie van GeoJSON Point, Polygon of LineString.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie die de afstand bevat. Dit wordt uitgedrukt in meters voor het standaardreferentiesysteem.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld ziet u hoe u alle gezinsdocumenten die `ST_DISTANCE` zich binnen 30 km van de opgegeven locatie bevinden, retourneren met behulp van de ingebouwde functie. .  
  
```sql
SELECT f.id
FROM Families f
WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{  
  "id": "WakefieldFamily"  
}]  
```

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie zal profiteren van een [georuimtelijke index](index-policy.md#spatial-indexes).

## <a name="next-steps"></a>Volgende stappen

- [Ruimtelijke functies Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
