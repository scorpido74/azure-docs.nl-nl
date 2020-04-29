---
title: Dynamische SQL gebruiken
description: Tips voor ontwikkel oplossingen die gebruikmaken van dynamische SQL in Synapse SQL-pool.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80633529"
---
# <a name="dynamic-sql-in-synapse-sql-pool"></a>Dynamische SQL-groep in Synapse SQL-pool

In dit artikel vindt u tips voor het ontwikkelen van oplossingen met behulp van dynamische SQL in SQL-groep.

## <a name="dynamic-sql-example"></a>Voor beeld van dynamische SQL

Bij het ontwikkelen van toepassings code voor SQL-groep moet u mogelijk gebruikmaken van dynamische SQL om flexibele, algemene en modulaire oplossingen te leveren. De SQL-pool ondersteunt op dit moment geen BLOB-gegevens typen.

Het ondersteunen van BLOB-gegevens typen kan de grootte van uw teken reeksen beperken omdat de BLOB-gegevens typen zowel varchar (max) als nvarchar (max)-typen bevatten.

Als u deze typen in de toepassings code hebt gebruikt om grote teken reeksen te maken, moet u de code in segmenten afsplitsen en in plaats daarvan de instructie EXEC gebruiken.

Een eenvoudig voor beeld:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Als de teken reeks kort is, kunt u [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) als normaal gebruiken.

> [!NOTE]
> Voor de instructies die worden uitgevoerd als dynamische SQL gelden alle regels voor T-SQL-validatie.

## <a name="next-steps"></a>Volgende stappen

Zie [ontwikkelings overzicht](sql-data-warehouse-overview-develop.md)voor meer tips voor ontwikkel aars.
