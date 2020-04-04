---
title: Dynamisch SQL gebruiken
description: Tips voor ontwikkelingsoplossingen met behulp van dynamische SQL in Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a9280bb8153204f86096cf8249ff053bee3f71cc
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633529"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>Dynamische SQL in Synapse SQL-pool

In dit artikel zijn tips voor ontwikkelingsoplossingen met behulp van dynamische SQL in SQL-pool.

## <a name="dynamic-sql-example"></a>Dynamisch SQL-voorbeeld

Bij het ontwikkelen van toepassingscode voor SQL-pool moet u mogelijk dynamische SQL gebruiken om flexibele, generieke en modulaire oplossingen te leveren. SQL-groep ondersteunt op dit moment geen blob-gegevenstypen.

Als blobgegevenstypen niet worden ondersteund, kan de grootte van uw tekenreeksen worden beperkt, aangezien blobgegevenstypen zowel varchar(max) als nvarchar(max)-typen bevatten.

Als u deze typen in uw toepassingscode hebt gebruikt om grote tekenreeksen te bouwen, moet u de code in brokken breken en in plaats daarvan de EXEC-instructie gebruiken.

Een eenvoudig voorbeeld:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Als de tekenreeks kort is, u [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) als normaal gebruiken.

> [!NOTE]
> Instructies die worden uitgevoerd als dynamische SQL zijn nog steeds onderworpen aan alle T-SQL-validatieregels.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer ontwikkelingstips [het ontwikkelingsoverzicht.](sql-data-warehouse-overview-develop.md)
