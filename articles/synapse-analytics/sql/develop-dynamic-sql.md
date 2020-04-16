---
title: Dynamische SQL gebruiken in Synapse SQL
description: Tips voor het gebruik van dynamische SQL in Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: b546e6ba6967edcf41e2830a639e69d436827c40
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429601"
---
# <a name="dynamic-sql-in-synapse-sql"></a>Dynamische SQL in Synapse SQL
In dit artikel vindt u tips voor het gebruik van dynamische SQL en het ontwikkelen van oplossingen met Synapse SQL.

## <a name="dynamic-sql-example"></a>Dynamisch SQL-voorbeeld

Bij het ontwikkelen van toepassingscode moet u mogelijk dynamische SQL gebruiken om flexibele, generieke en modulaire oplossingen te leveren.

> [!NOTE]
> SQL-groep ondersteunt op dit moment geen blob-gegevenstypen. Als blobgegevenstypen niet worden ondersteund, kan de grootte van uw tekenreeksen worden beperkt, aangezien blobgegevenstypen zowel varchar(max) als nvarchar(max)-typen bevatten. Als u deze typen in uw toepassingscode hebt gebruikt om grote tekenreeksen te bouwen, moet u de code in brokken breken en in plaats daarvan de EXEC-instructie gebruiken.

Een eenvoudig voorbeeld:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Als de tekenreeks kort is, u [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) als normaal gebruiken.

> [!NOTE]
> Instructies die worden uitgevoerd als dynamische SQL zijn nog steeds onderworpen aan alle T-SQL-validatieregels.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer ontwikkelingstips [het ontwikkelingsoverzicht.](develop-overview.md)
