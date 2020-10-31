---
title: LAGER in Azure Cosmos DB query taal
description: Meer informatie over de lagere SQL-systeem functie in Azure Cosmos DB om een teken reeks expressie te retour neren na het omzetten van een hoofd letter-teken in kleine letters
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 163b893b74b5f6d3950ae92e209f96abde8cd90e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075276"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een tekenreeksexpressie na het converteren van tekens in hoofdletters naar kleine letters.  

De lagere systeem functie maakt geen gebruik van de index. Als u van plan bent om niet-hoofdletter gevoelige vergelijkingen te maken, kan de functie lagere systeem een aanzienlijke hoeveelheid van RU verbruiken. Als dit het geval is, kunt u in plaats van het gebruik van de functie lagere systeem gegevens elke keer voor vergelijkingen te normaliseren, de behuizing tijdens het invoegen normaliseren. Vervolgens wordt een query zoals SELECT * FROM c waarbij LOWER (c. name) = ' Bob ' gewoon geselecteerd * van c waarbij c.name = ' Bob '.

## <a name="syntax"></a>Syntaxis
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een teken reeks expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een teken reeks expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u kunt gebruiken `LOWER` in een query.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Dit is de resultatenset.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Teken reeks functies Azure Cosmos DB](sql-query-string-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
