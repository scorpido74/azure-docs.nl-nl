---
title: Gegevenstypen definiëren
description: Aanbevelingen voor het definiëren van tabelgegevenstypen in Azure SQL Data Warehouse.
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
ms.openlocfilehash: 020b6fc08dad0dacb51215eca6f7baf127a9dba6
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351322"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Tabelgegevenstypen in Azure SQL Data Warehouse
Aanbevelingen voor het definiëren van tabelgegevenstypen in Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Wat zijn de gegevenstypen?

SQL Data Warehouse ondersteunt de meest gebruikte gegevenstypen. Zie [gegevenstypen](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) in de instructie TABEL MAKEN voor een lijst met ondersteunde gegevenstypen. 

## <a name="minimize-row-length"></a>De lengte van de rij minimaliseren
Als u de grootte van gegevenstypen minimaliseert, wordt de lengte van de rij verkort, wat leidt tot betere queryprestaties. Gebruik het kleinste gegevenstype dat werkt voor uw gegevens. 

- Vermijd het definiëren van tekenkolommen met een grote standaardlengte. Als de langste waarde bijvoorbeeld 25 tekens is, definieert u uw kolom als VARCHAR(25). 
- Vermijd het gebruik van [NVARCHAR][NVARCHAR] wanneer u alleen VARCHAR nodig hebt.
- Gebruik indien mogelijk NVARCHAR(4000) of VARCHAR(8000) in plaats van NVARCHAR(MAX) of VARCHAR(MAX).

Als u externe tabellen van PolyBase gebruikt om uw tabellen te laden, mag de gedefinieerde lengte van de tabelrij niet meer dan 1 MB bedragen. Wanneer een rij met gegevens over variabele lengte meer dan 1 MB bedraagt, u de rij laden met BCP, maar niet met PolyBase.

## <a name="identify-unsupported-data-types"></a>Niet-ondersteunde gegevenstypen identificeren
Als u uw database migreert vanuit een andere SQL-database, u gegevenstypen tegenkomen die niet worden ondersteund in SQL Data Warehouse. Gebruik deze query om niet-ondersteunde gegevenstypen in uw bestaande SQL-schema te ontdekken.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Tijdelijke oplossingen voor niet-ondersteunde gegevenstypen

In de volgende lijst worden de gegevenstypen weergegeven die SQL Data Warehouse niet ondersteunt en biedt het alternatieven die u gebruiken in plaats van de niet-ondersteunde gegevenstypen.

| Niet-ondersteund gegevenstype | Tijdelijke oplossing |
| --- | --- |
| [Geometrie](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Geografie](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [Afbeelding](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Tekst](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntekst](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Verdeel kolom in verschillende sterk getypte kolommen. |
| [Tabel](/sql/t-sql/data-types/table-transact-sql) |Converteren naar tijdelijke tabellen. |
| [Tijdstempel](/sql/t-sql/data-types/date-and-time-types) |Code opnieuw werken om [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) en de [functie CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) te gebruiken. Alleen constanten worden als standaardwaarden ondersteund, daarom kunnen current_timestamp niet worden gedefinieerd als een standaardbeperking. Als u rijversiewaarden uit een getypte tijdstempelkolom moet migreren, gebruikt u [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) of [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) voor waarden van DE NULL- of NULL-rijversie. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [door de gebruiker gedefinieerd type](/sql/relational-databases/native-client/features/using-user-defined-types) |Converteer waar mogelijk terug naar het native gegevenstype. |
| standaardwaarden | Standaardwaarden ondersteunen alleen liters en constanten. |


## <a name="next-steps"></a>Volgende stappen
Zie [Tabeloverzicht voor](sql-data-warehouse-tables-overview.md)meer informatie over het ontwikkelen van tabellen.
