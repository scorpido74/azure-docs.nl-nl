---
title: ST_ISVALID in Azure Cosmos DB-querytaal
description: Meer informatie over de SQL-systeemfunctie ST_ISVALID in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349360"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
 Retourneert een Booleaanse waarde die aangeeft of de opgegeven GeoJSON Point-, Polygon- of LineString-expressie geldig is.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*spatial_expr*  
   Is een expressie geoJSON-punt, veelhoek of lijntekenreeks.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een Booleaanse expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld ziet u hoe u controleren of een punt geldig is met behulp van ST_VALID.  
  
  Dit punt heeft bijvoorbeeld een breedtegraadwaarde die zich niet in het geldige waardenbereik bevindt [-90, 90], zodat de query false retourneert.  
  
  Voor veelhoeken vereist de GeoJSON-specificatie dat het laatste meegeleverde coördinatenpaar hetzelfde moet zijn als het eerste, om een gesloten vorm te maken. Punten binnen een veelhoek moeten tegen de klok in worden opgegeven. Een veelhoek die in de volgorde met de klok mee wordt opgegeven, vertegenwoordigt het omgekeerde van het gebied erin.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Ruimtelijke functies Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
