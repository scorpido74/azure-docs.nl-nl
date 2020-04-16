---
title: Gegevenstypen definiëren
description: Aanbevelingen voor het definiëren van tabelgegevenstypen in de Synapsische SQL-groep.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 01/06/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 2c99bf77246463dd84c41218afbb5e50f4335f9f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413759"
---
# <a name="table-data-types-in-synapse-sql-pool"></a>Tabelgegevenstypen in de Synapsische SQL-groep

In dit artikel zijn aanbevelingen opgenomen voor het definiëren van tabelgegevenstypen in SQL-groep.

## <a name="supported-data-types"></a>Ondersteunde gegevenstypen

SQL-groep ondersteunt de meest gebruikte gegevenstypen. Zie [gegevenstypen](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes) in de instructie TABEL MAKEN voor een lijst met ondersteunde gegevenstypen.

## <a name="minimize-row-length"></a>De lengte van de rij minimaliseren

Als u de grootte van gegevenstypen minimaliseert, wordt de lengte van de rij verkort, wat leidt tot betere queryprestaties. Gebruik het kleinste gegevenstype dat werkt voor uw gegevens.

- Vermijd het definiëren van tekenkolommen met een grote standaardlengte. Als de langste waarde bijvoorbeeld 25 tekens is, definieert u uw kolom als VARCHAR(25).
- Vermijd het gebruik van [NVARCHAR](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) wanneer u alleen VARCHAR nodig hebt.
- Gebruik indien mogelijk NVARCHAR(4000) of VARCHAR(8000) in plaats van NVARCHAR(MAX) of VARCHAR(MAX).

Als u externe tabellen van PolyBase gebruikt om uw tabellen te laden, mag de gedefinieerde lengte van de tabelrij niet meer dan 1 MB bedragen. Wanneer een rij met gegevens over variabele lengte meer dan 1 MB bedraagt, u de rij laden met BCP, maar niet met PolyBase.

## <a name="identify-unsupported-data-types"></a>Niet-ondersteunde gegevenstypen identificeren

Als u uw database migreert vanuit een andere SQL-database, u gegevenstypen vinden die niet worden ondersteund in SQL-groep. Gebruik de volgende query om niet-ondersteunde gegevenstypen in uw bestaande SQL-schema te ontdekken:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Tijdelijke oplossingen voor niet-ondersteunde gegevenstypen

In de volgende lijst worden de gegevenstypen weergegeven die SQL-groep niet ondersteunt en worden nuttige alternatieven weergegeven voor niet-ondersteunde gegevenstypen.

| Niet-ondersteund gegevenstype | Tijdelijke oplossing |
| --- | --- |
| [Geometrie](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Geografie](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)(4000) |
| [Afbeelding](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Tekst](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [ntekst](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Verdeel kolom in verschillende sterk getypte kolommen. |
| [Tabel](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Converteren naar tijdelijke tabellen. |
| [Tijdstempel](/sql/t-sql/data-types/date-and-time-types) |Code opnieuw werken om [datetime2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) en de [functie CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) te gebruiken. Alleen constanten worden standaard ondersteund, zodat current_timestamp niet kan worden gedefinieerd als een standaardbeperking. Als u rijversiewaarden uit een getypte tijdstempelkolom moet migreren, gebruikt u [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)(8) of [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)(8) voor waarden van DE NULL- of NULL-rijversie. |
| [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [door de gebruiker gedefinieerd type](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Converteer waar mogelijk terug naar het native gegevenstype. |
| standaardwaarden | Standaardwaarden ondersteunen alleen liters en constanten. |

## <a name="next-steps"></a>Volgende stappen

Zie [Tabeloverzicht voor](sql-data-warehouse-tables-overview.md)meer informatie over het ontwikkelen van tabellen.
