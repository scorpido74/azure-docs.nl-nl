---
title: Type controleren van functies in Azure Cosmos DB query taal
description: Meer informatie over het type controle van SQL-functies in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: bafc599ddb502d5714b08ff58ed942e9c17557a8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93093755"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Type controle functies (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Met de functies voor type controles kunt u het type van een expressie in een SQL-query controleren. U kunt type-controle functies gebruiken om de typen eigenschappen binnen items te bepalen, wanneer deze worden gevariable of onbekend. 

## <a name="functions"></a>Functies

Hier volgt een tabel met ondersteunde ingebouwde functies voor type controle:

Met de volgende functies wordt het type controle op basis van invoer waarden ondersteund en wordt een Booleaanse waarde geretourneerd.  

* [IS_ARRAY](sql-query-is-array.md)
* [IS_BOOL](sql-query-is-bool.md)
* [IS_DEFINED](sql-query-is-defined.md)
* [IS_NULL](sql-query-is-null.md)
* [IS_NUMBER](sql-query-is-number.md)
* [IS_OBJECT](sql-query-is-object.md)
* [IS_PRIMITIVE](sql-query-is-primitive.md)
* [IS_STRING](sql-query-is-string.md)

## <a name="next-steps"></a>Volgende stappen

- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Door de gebruiker gedefinieerde functies](sql-query-udfs.md)
- [Statistische functies](sql-query-aggregates.md)
