---
title: Gegevens typen definiëren
description: Aanbevelingen voor het definiëren van tabel gegevens typen in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a1d88d8fabd5ff32cc1ca5cca4e2a57a86c8dcb3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692486"
---
# <a name="table-data-types-in-azure-sql-data-warehouse"></a>Tabel gegevens typen in Azure SQL Data Warehouse
Aanbevelingen voor het definiëren van tabel gegevens typen in Azure SQL Data Warehouse. 

## <a name="what-are-the-data-types"></a>Wat zijn de gegevens typen?

SQL Data Warehouse ondersteunt de meest gebruikte gegevens typen. Zie [gegevens typen](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) in de CREATE TABLE-instructie voor een lijst met ondersteunde gegevens typen. 

## <a name="minimize-row-length"></a>Lengte van rij minimaliseren
Het minimaliseren van de grootte van gegevens typen verkort de lengte van de rij, wat leidt tot betere query prestaties. Gebruik het kleinste gegevens type dat voor uw gegevens werkt. 

- Vermijd het definiëren van teken kolommen met een grote standaard lengte. Als de langste waarde bijvoorbeeld 25 tekens is, definieert u uw kolom als VARCHAR (25). 
- Vermijd het gebruik van [NVARCHAR] [NVARCHAR] wanneer u alleen VARCHAR nodig hebt.
- Gebruik, indien mogelijk, NVARCHAR (4000) of VARCHAR (8000) in plaats van NVARCHAR (MAX) of VARCHAR (MAX).

Als u poly base externe tabellen gebruikt om tabellen te laden, mag de gedefinieerde lengte van de tabelrij niet meer zijn dan 1 MB. Wanneer een rij met gegevens van variabele lengte meer dan 1 MB overschrijdt, kunt u de rij laden met BCP, maar niet met poly base.

## <a name="identify-unsupported-data-types"></a>Niet-ondersteunde gegevens typen identificeren
Als u uw data base migreert vanuit een andere SQL database, kunt u gegevens typen tegen komen die niet worden ondersteund in SQL Data Warehouse. Gebruik deze query om niet-ondersteunde gegevens typen in uw bestaande SQL-schema te detecteren.

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="unsupported-data-types"></a>Tijdelijke oplossingen voor niet-ondersteunde gegevens typen

In de volgende lijst worden de gegevens typen weer gegeven die SQL Data Warehouse geen ondersteuning biedt voor alternatieven en die u kunt gebruiken in plaats van de niet-ondersteunde gegevens typen.

| Niet-ondersteund gegevens type | Tijdelijke oplossing |
| --- | --- |
| [geometrie](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Geografie](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [bitmapafbeelding](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [SMS](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Kolom splitsen in meerdere sterk getypeerde kolommen. |
| [table](/sql/t-sql/data-types/table-transact-sql) |Converteren naar tijdelijke tabellen. |
| [Neem](/sql/t-sql/data-types/date-and-time-types) |Herwerkings code voor het gebruik van [DATETIME2](/sql/t-sql/data-types/datetime2-transact-sql) en de functie [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql) . Alleen constanten worden ondersteund als standaard waarden, daarom kan CURRENT_TIMESTAMP niet worden gedefinieerd als een default-beperking. Als u de waarden van de rij-versie wilt migreren uit een opgegeven time stamp-kolom, gebruikt u [binary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) of [varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) voor versie waarden van null of null. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [door de gebruiker gedefinieerd type](/sql/relational-databases/native-client/features/using-user-defined-types) |Converteer indien mogelijk terug naar het systeem eigen gegevens type. |
| standaard waarden | Standaard waarden ondersteunen alleen literals en constanten. |


## <a name="next-steps"></a>Volgende stappen
Zie [tabel Overzicht](sql-data-warehouse-tables-overview.md)voor meer informatie over het ontwikkelen van tabellen.
