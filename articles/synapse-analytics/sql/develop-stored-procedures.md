---
title: Opgeslagen procedures gebruiken
description: Tips voor het implementeren van opgeslagen procedures in Synapse SQL voor het ontwikkelen van oplossingen.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 09/23/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: f2046614f3665a699d02c76210676fb32f99fc73
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91288916"
---
# <a name="use-stored-procedures-in-synapse-sql"></a>Opgeslagen procedures in Synapse SQL gebruiken

Tips voor het implementeren van opgeslagen procedures in Synapse SQL pool (Data Warehouse) voor het ontwikkelen van oplossingen.

## <a name="what-to-expect"></a>Wat u kunt verwachten

Synapse SQL ondersteunt veel van de T-SQL-functies die worden gebruikt in SQL Server. Belang rijker is dat er specifieke functies zijn die u kunt gebruiken om de prestaties van uw oplossing te optimaliseren.

Voor het behoud van de schaal en prestaties van de SQL-groep zijn er ook enkele functies en functionaliteit die gedrags verschillen hebben en andere die niet worden ondersteund.

## <a name="stored-procedures-in-synapse-sql"></a>Opgeslagen procedures in Synapse SQL

Opgeslagen procedures zijn een uitstekende manier om uw SQL-code te integreren en de gegevens op te slaan in het Data Warehouse. Met opgeslagen procedures kunnen ontwikkel aars hun oplossingen modularizeen door de code te integreren in beheer bare eenheden, waardoor de code gemakkelijker te gebruiken is. Elke opgeslagen procedure kan ook para meters accepteren om ze nog flexibeler te maken. In het volgende voor beeld ziet u de procedures waarmee externe objecten worden verwijderd als deze bestaan in de Data Base:

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_tables WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL TABLE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_file_format_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_file_formats WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL FILE FORMAT ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
GO
CREATE PROCEDURE drop_external_data_source_if_exists @name SYSNAME
AS BEGIN
    IF (0 <> (SELECT COUNT(*) FROM sys.external_data_sources WHERE name = @name))
    BEGIN
        DECLARE @drop_stmt NVARCHAR(200) = N'DROP EXTERNAL DATA SOURCE ' + @name; 
        EXEC sp_executesql @tsql = @drop_stmt;
    END
END
```

Deze procedures kunnen worden uitgevoerd met behulp van `EXEC` de instructie waarbij u de procedure naam en de para meters kunt opgeven:

```sql
EXEC drop_external_table_if_exists 'mytest';
EXEC drop_external_file_format_if_exists 'mytest';
EXEC drop_external_data_source_if_exists 'mytest';
```

Synapse SQL biedt een vereenvoudigde en gestroomlijnde implementatie van opgeslagen procedures. Het grootste verschil ten opzichte van SQL Server is dat de opgeslagen procedure geen vooraf gecompileerde code is. In data warehouses is de compilatie tijd klein in vergelijking met de tijd die nodig is om query's uit te voeren op grote gegevens volumes. Het is belang rijker om ervoor te zorgen dat de opgeslagen procedure code correct is geoptimaliseerd voor grote query's. Het doel is om uren, minuten en seconden op te slaan, niet in milliseconden. Het is daarom handiger om opgeslagen procedures te beschouwen als containers voor SQL-logica.

Wanneer Synapse SQL uw opgeslagen procedure uitvoert, worden de SQL-instructies geparseerd, vertaald en geoptimaliseerd tijdens runtime. Tijdens dit proces wordt elke instructie geconverteerd naar gedistribueerde query's. De SQL-code die wordt uitgevoerd op basis van de gegevens, wijkt af van de query die is verzonden.

## <a name="encapsulate-validation-rules"></a>Validatie regels inkapselen

Met opgeslagen procedures kunt u validatie logica vinden in één module die is opgeslagen in SQL database. In het volgende voor beeld kunt u zien hoe u de waarden van para meters valideert en de standaard waarden wijzigt.

```sql
CREATE PROCEDURE count_objects_by_date_created 
                            @start_date DATETIME2,
                            @end_date DATETIME2
AS BEGIN 

    IF( @start_date >= GETUTCDATE() )
    BEGIN
        THROW 51000, 'Invalid argument @start_date. Value should be in past.', 1;  
    END

    IF( @end_date IS NULL )
    BEGIN
        SET @end_date = GETUTCDATE();
    END

    IF( @start_date >= @end_date )
    BEGIN
        THROW 51000, 'Invalid argument @end_date. Value should be greater than @start_date.', 2;  
    END

    SELECT
         year = YEAR(create_date),
         month = MONTH(create_date),
         objects_created = COUNT(*)
    FROM
        sys.objects
    WHERE
        create_date BETWEEN @start_date AND @end_date
    GROUP BY
        YEAR(create_date), MONTH(create_date);
END
```

De logica in de SQL-procedure valideert de invoer parameters wanneer de procedure wordt aangeroepen.

```sql

EXEC count_objects_by_date_created '2020-08-01', '2020-09-01'

EXEC count_objects_by_date_created '2020-08-01', NULL

EXEC count_objects_by_date_created '2020-09-01', '2020-08-01'
-- Error
-- Invalid argument @end_date. Value should be greater than @start_date.

EXEC count_objects_by_date_created '2120-09-01', NULL
-- Error
-- Invalid argument @start_date. Value should be in past.
```

## <a name="nesting-stored-procedures"></a>Opgeslagen procedures nesten

Wanneer opgeslagen procedures andere opgeslagen procedures aanroepen of dynamische SQL uitvoeren, wordt de interne opgeslagen procedure of het aanroepen van de code genest.
Een voor beeld van een geneste procedure wordt weer gegeven in de volgende code:

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS BEGIN
    EXEC drop_external_table_if_exists @name;
    EXEC drop_external_file_format_if_exists @name;
    EXEC drop_external_data_source_if_exists @name;
END
```

Met deze procedure wordt een para meter geaccepteerd die een naam vertegenwoordigt en vervolgens andere procedures aanroept om de objecten met deze naam neer te zetten.
De SQL-pool Synapse ondersteunt Maxi maal acht geneste niveaus. Deze functie wijkt enigszins af van SQL Server. Het nest niveau in SQL Server is 32.

De op het hoogste niveau opgeslagen procedure aanroep is gelijk aan nest niveau 1.

```sql
EXEC clean_up 'mytest'
```

Als de opgeslagen procedure ook een andere EXEC-aanroep maakt, neemt het nest niveau toe tot twee.

```sql
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

Als de tweede procedure vervolgens een aantal dynamische SQL-procedures uitvoert, neemt het nest niveau toe tot drie.

```sql
CREATE PROCEDURE drop_external_table_if_exists @name SYSNAME
AS BEGIN
    /* See full code in the previous example */
    EXEC sp_executesql @tsql = @drop_stmt;  -- This call is nest level 3
END
GO
CREATE PROCEDURE clean_up @name SYSNAME
AS
    EXEC drop_external_table_if_exists @name  -- This call is nest level 2
GO
EXEC clean_up 'mytest'  -- This call is nest level 1
```

> [!NOTE]
> Synapse SQL biedt momenteel geen ondersteuning [voor @NESTLEVEL @](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). U moet het nest niveau bijhouden. Het is niet onwaarschijnlijk dat u de limiet van acht geneste niveaus overschrijdt, maar als u dat wel doet, moet u uw code opnieuw instellen zodat deze overeenkomt met de geneste niveaus binnen deze limiet.

## <a name="insertexecute"></a>INSERT..EXESCHATTIGE

Met Synapse SQL kunt u de resultatenset van een opgeslagen procedure niet gebruiken met een instructie INSERT. Er is een alternatieve benadering die u kunt gebruiken. Zie het artikel over [tijdelijke tabellen](develop-tables-temporary.md)voor een voor beeld.

## <a name="limitations"></a>Beperkingen

Er zijn een aantal aspecten van opgeslagen Transact-SQL-procedures die niet zijn geïmplementeerd in Synapse SQL, zoals:

* tijdelijke opgeslagen procedures
* genummerde opgeslagen procedures
* uitgebreide opgeslagen procedures
* Opgeslagen CLR-procedures
* versleutelings optie
* replicatie optie
* tabelwaardeparameter
* alleen-lezen-para meters
* standaard parameters (in de ingerichte pool)
* uitvoerings contexten
* instructie return

## <a name="next-steps"></a>Volgende stappen

Zie [ontwikkelings overzicht](develop-overview.md)voor meer tips voor ontwikkel aars.
