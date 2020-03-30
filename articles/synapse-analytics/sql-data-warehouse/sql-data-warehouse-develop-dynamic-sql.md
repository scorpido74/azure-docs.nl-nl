---
title: Dynamisch SQL gebruiken
description: Tips voor het gebruik van dynamische SQL in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.
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
ms.openlocfilehash: a44bec72029a50c2ef348bcdda497803e35f586d
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350557"
---
# <a name="dynamic-sql-in-sql-data-warehouse"></a>Dynamische SQL in SQL Data Warehouse
Tips voor het gebruik van dynamische SQL in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.

## <a name="dynamic-sql-example"></a>Dynamisch SQL-voorbeeld

Bij het ontwikkelen van toepassingscode voor SQL Data Warehouse moet u mogelijk dynamische sql gebruiken om flexibele, generieke en modulaire oplossingen te leveren. SQL Data Warehouse ondersteunt op dit moment geen blob-gegevenstypen. Als blobgegevenstypen niet worden ondersteund, kan de grootte van uw tekenreeksen worden beperkt, aangezien blobgegevenstypen zowel varchar(max) als nvarchar(max)-typen bevatten. Als u deze typen in uw toepassingscode hebt gebruikt om grote tekenreeksen te bouwen, moet u de code in brokken breken en in plaats daarvan de EXEC-instructie gebruiken.

Een eenvoudig voorbeeld:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

Als de tekenreeks kort is, u [sp_executesql](/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql) als normaal gebruiken.

> [!NOTE]
> Instructies die worden uitgevoerd als dynamische SQL zijn nog steeds onderworpen aan alle TSQL-validatieregels.
> 
> 

## <a name="next-steps"></a>Volgende stappen
Zie voor meer ontwikkelingstips [het ontwikkelingsoverzicht.](sql-data-warehouse-overview-develop.md)

