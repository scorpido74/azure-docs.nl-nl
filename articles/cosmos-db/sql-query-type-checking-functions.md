---
title: Type controleren van functies in Azure Cosmos DB query taal
description: Meer informatie over het type controle van SQL-functies in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349074"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Type controle functies (Azure Cosmos DB)

Met de functies voor type controles kunt u het type van een expressie in een SQL-query controleren. U kunt type-controle functies gebruiken om de typen eigenschappen binnen items te bepalen, wanneer deze worden gevariable of onbekend. 

## <a name="functions"></a>Functies

Hier volgt een tabel met ondersteunde ingebouwde functies voor type controle:

De volgende functies ondersteunen controleren op basis van de invoerwaarden van het type en elke een Booleaanse waarde retourneren.  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>Volgende stappen

- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Door de gebruiker gedefinieerde functies](sql-query-udfs.md)
- [Aggregates](sql-query-aggregates.md)
