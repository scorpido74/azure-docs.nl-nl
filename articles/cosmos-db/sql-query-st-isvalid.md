---
title: ST_ISVALID in Azure Cosmos DB query taal
description: Meer informatie over de functie ST_ISVALID van SQL-systeem in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8fbddbe82ae13585b8259a66dffaeef8024baf5d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
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
   Is een geojson Point-, veelhoek-of lines Tring-expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een Boole-expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u kunt controleren of een punt geldig is met behulp van ST_VALID.  
  
  Dit punt heeft bijvoorbeeld een waarde van een breedte die niet voor komt in het geldige bereik van waarden [-90, 90], waardoor de query onwaar retourneert.  
  
  Voor veelhoeken moet de geojson-specificatie overeenkomen met de naam van het laatste coördinaten paar, om een gesloten vorm te maken. Punten binnen een veelhoek moeten worden opgegeven in de volg orde van linksom. Een veelhoek die in de volg orde van de klok wordt opgegeven, vertegenwoordigt de inverse van de regio.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Dit is de resultatenset.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Ruimtelijke functies Azure Cosmos DB](sql-query-spatial-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
