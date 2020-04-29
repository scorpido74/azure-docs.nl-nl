---
title: Gegevens typen definiëren
description: Aanbevelingen voor het definiëren van tabel gegevens typen in Synapse SQL-pool.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81413759"
---
# <a name="table-data-types-in-synapse-sql-pool"></a>Tabel gegevens typen in Synapse SQL-pool

In dit artikel vindt u aanbevelingen voor het definiëren van tabel gegevens typen in een SQL-groep.

## <a name="supported-data-types"></a>Ondersteunde gegevens typen

De SQL-groep ondersteunt het meest gebruikte gegevens type. Zie [gegevens typen](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#DataTypes) in de CREATE TABLE-instructie voor een lijst met ondersteunde gegevens typen.

## <a name="minimize-row-length"></a>Lengte van rij minimaliseren

Het minimaliseren van de grootte van gegevens typen verkort de lengte van de rij, wat leidt tot betere query prestaties. Gebruik het kleinste gegevens type dat voor uw gegevens werkt.

- Vermijd het definiëren van teken kolommen met een grote standaard lengte. Als de langste waarde bijvoorbeeld 25 tekens is, definieert u uw kolom als VARCHAR (25).
- Vermijd het gebruik van [NVARCHAR](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) wanneer u alleen varchar nodig hebt.
- Gebruik, indien mogelijk, NVARCHAR (4000) of VARCHAR (8000) in plaats van NVARCHAR (MAX) of VARCHAR (MAX).

Als u poly base externe tabellen gebruikt om tabellen te laden, mag de gedefinieerde lengte van de tabelrij niet groter zijn dan 1 MB. Wanneer een rij met gegevens van variabele lengte meer dan 1 MB overschrijdt, kunt u de rij laden met BCP, maar niet met poly base.

## <a name="identify-unsupported-data-types"></a>Niet-ondersteunde gegevens typen identificeren

Als u de data base migreert van een andere SQL database, vindt u mogelijk gegevens typen die niet worden ondersteund in de SQL-groep. Gebruik de volgende query om niet-ondersteunde gegevens typen in uw bestaande SQL-schema te detecteren:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```

## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Tijdelijke oplossingen voor niet-ondersteunde gegevens typen

De volgende lijst bevat de gegevens typen die SQL-groep niet ondersteunt en biedt nuttige alternatieven voor niet-ondersteunde gegevens typen.

| Niet-ondersteund gegevens type | Tijdelijke oplossing |
| --- | --- |
| [geometrie](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [Geografie](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)(4000) |
| [bitmapafbeelding](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [SMS](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Kolom splitsen in meerdere sterk getypeerde kolommen. |
| [tabel](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Converteren naar tijdelijke tabellen. |
| [Neem](/sql/t-sql/data-types/date-and-time-types) |Herwerkings code voor het gebruik van [DATETIME2](/sql/t-sql/data-types/datetime2-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) en de functie [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) . Alleen constanten worden ondersteund als standaard waarden, dus current_timestamp kan niet worden gedefinieerd als een default-beperking. Als u de waarden van de rij-versie wilt migreren vanuit een ingevoerde time stamp-kolom, gebruikt u [binary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)(8) of [varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)(8) voor versie waarden van null of null. |
| [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |
| [door de gebruiker gedefinieerd type](/sql/relational-databases/native-client/features/using-user-defined-types?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) |Converteer indien mogelijk terug naar het systeem eigen gegevens type. |
| standaard waarden | Standaard waarden ondersteunen alleen literals en constanten. |

## <a name="next-steps"></a>Volgende stappen

Zie [tabel Overzicht](sql-data-warehouse-tables-overview.md)voor meer informatie over het ontwikkelen van tabellen.
