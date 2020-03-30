---
title: Typ controlefuncties in Azure Cosmos DB-querytaal
description: Meer informatie over het controleren van SQL-systeemfuncties in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: cac8be4c39737ca209653532d00375c8c9bee48e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349074"
---
# <a name="type-checking-functions-azure-cosmos-db"></a>Typecontrolefuncties (Azure Cosmos DB)

Met de functies voor typecontrole u het type expressie in een SQL-query controleren. U functies voor typecontrole gebruiken om de typen eigenschappen binnen items on the fly te bepalen, wanneer ze variabel of onbekend zijn. 

## <a name="functions"></a>Functions

Hier vindt u een tabel met ondersteunde ingebouwde typecontrolefuncties:

De volgende functies ondersteunen typecontrole ten opzichte van invoerwaarden en elke keer een Booleaanse waarde.  
  
||||  
|-|-|-|  
|[IS_ARRAY](sql-query-is-array.md)|[IS_BOOL](sql-query-is-bool.md)|[IS_DEFINED](sql-query-is-defined.md)|  
|[IS_NULL](sql-query-is-null.md)|[IS_NUMBER](sql-query-is-number.md)|[IS_OBJECT](sql-query-is-object.md)|  
|[IS_PRIMITIVE](sql-query-is-primitive.md)|[IS_STRING](sql-query-is-string.md)||  
  

## <a name="next-steps"></a>Volgende stappen

- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Door de gebruiker gedefinieerde functies](sql-query-udfs.md)
- [Aggregaties](sql-query-aggregates.md)
