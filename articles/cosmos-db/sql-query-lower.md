---
title: LAGER in Azure Cosmos DB query taal
description: Meer informatie over de lagere SQL-systeem functie in Azure Cosmos DB om een teken reeks expressie te retour neren na het omzetten van een hoofd letter-teken in kleine letters
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 80dba57d4fe05630eb5ae4f8fc96bd0aa214c6d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78302267"
---
# <a name="lower-azure-cosmos-db"></a>LOWER (Azure Cosmos DB)
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
  
  In het volgende voor beeld ziet u `LOWER` hoe u kunt gebruiken in een query.  
  
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
