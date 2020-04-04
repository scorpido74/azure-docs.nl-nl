---
title: Transacties gebruiken in de Synapse SQL-groep
description: Dit artikel bevat tips voor het implementeren van transacties en het ontwikkelen van oplossingen in Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/22/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: fdbffba7bee84c32d11f8b60431a35f185d9e637
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633433"
---
# <a name="use-transactions-in-synapse-sql-pool"></a>Transacties gebruiken in de Synapse SQL-groep
Dit artikel bevat tips voor het implementeren van transacties en het ontwikkelen van oplossingen in SQL-pool.

## <a name="what-to-expect"></a>Wat te verwachten
Zoals u zou verwachten, ondersteunt SQL-pool transacties als onderdeel van de werkbelasting van het gegevensmagazijn. Om ervoor te zorgen dat SQL-pool echter op schaal wordt onderhouden, zijn sommige functies beperkt in vergelijking met SQL Server. Dit artikel belicht de verschillen. 

## <a name="transaction-isolation-levels"></a>Transactieisolatieniveaus
SQL-groep implementeert ACID-transacties. Het isolatieniveau van de transactionele ondersteuning is standaard lezen NIET-VASTGELEGD.  U deze wijzigen in HET LEZEN VAN VASTGELEGDE MOMENTOPNAMEISOLATIE door de READ_COMMITTED_SNAPSHOT-databaseoptie in te schakelen voor een gebruikersdatabase wanneer deze is verbonden met de hoofddatabase.  

Zodra deze is ingeschakeld, worden alle transacties in deze database uitgevoerd onder LEES COMMITTED SNAPSHOT ISOLATION en wordt het instellen van READ UNCOMMITTED op sessieniveau niet gehonoreerd. Schakel [opties voor WIJZIGINGDATABASESET (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest) in voor meer informatie.

## <a name="transaction-size"></a>Transactiegrootte
Een enkele transactie voor gegevenswijziging is beperkt in omvang. De limiet wordt per distributie toegepast. Daarom kan de totale toewijzing worden berekend door de limiet te vermenigvuldigen met het aantal verdelingen. 

Als u het maximumaantal rijen in de transactie wilt benaderen, deelt u de distributiedop door de totale grootte van elke rij. Voor kolommen met variabele lengte u overwegen een gemiddelde kolomlengte te nemen in plaats van de maximale grootte te gebruiken.

In de volgende tabel zijn twee veronderstellingen gemaakt:

* Er is een gelijkmatige verdeling van gegevens opgetreden 
* De gemiddelde rijlengte is 250 bytes

## <a name="gen2"></a>Gen2 Gen2

| [DWU](sql-data-warehouse-overview-what-is.md) | Cap per distributie (GB) | Aantal distributies | MAXIMAL-transactiegrootte (GB) | # Rijen per distributie | Maximale rijen per transactie |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4,000,000 |240,000,000 |
| DW200c |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300c |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400c |3 |60 |180 |12,000,000 |720,000,000 |
| DW500c |3,75 |60 |225 |15,000,000 |900,000,000 |
| DW1000c |7,5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1500c |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000c |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW2500c |18.75 |60 |1125 |75,000,000 |4,500,000,000 |
| DW3000c |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW5000c |37.5 |60 |2,250 |150,000,000 |9,000,000,000 |
| DW6000c |45 |60 |2,700 |180,000,000 |10,800,000,000 |
| DW7500c |56.25 |60 |3,375 |225,000,000 |13,500,000,000 |
| DW10000c |75 |60 |4.500 |300,000,000 |18,000,000,000 |
| DW15000c |112.5 |60 |6750 |450,000,000 |27,000,000,000 |
| DW30000c |225 |60 |13,500 |900,000,000 |54,000,000,000 |

## <a name="gen1"></a>Gen1 Gen1

| [DWU](sql-data-warehouse-overview-what-is.md) | Cap per distributie (GB) | Aantal distributies | MAXIMAL-transactiegrootte (GB) | # Rijen per distributie | Maximale rijen per transactie |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 |1.5 |60 |90 |6,000,000 |360,000,000 |
| DW300 |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3,75 |60 |225 |15,000,000 |900,000,000 |
| DW600 |4.5 |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7,5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

De limiet voor de transactiegrootte wordt per transactie of bewerking toegepast. Het wordt niet toegepast voor alle gelijktijdige transacties. Daarom is elke transactie toegestaan om deze hoeveelheid gegevens naar het logboek te schrijven. 

Raadpleeg het artikel Transacties best [practices](sql-data-warehouse-develop-best-practices-transactions.md) om de hoeveelheid gegevens die naar het logboek zijn geschreven te optimaliseren en te minimaliseren.

> [!WARNING]
> De maximale transactiegrootte kan alleen worden bereikt voor HASH of ROUND_ROBIN gedistribueerde tabellen waar de verspreiding van de gegevens gelijk is. Als de transactie op een scheve manier gegevens schrijft aan de distributies, wordt de limiet waarschijnlijk bereikt vóór de maximale transactiegrootte.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Transactiestatus
SQL-groep gebruikt de functie XACT_STATE() om een mislukte transactie te melden met de waarde -2. Deze waarde betekent dat de transactie is mislukt en alleen is gemarkeerd voor terugdraaien.

> [!NOTE]
> Het gebruik van -2 door de XACT_STATE functie om een mislukte transactie aan te duiden, vertegenwoordigt een ander gedrag dan SQL Server. SQL Server gebruikt de waarde -1 om een vrijblijvende transactie weer te geven. SQL Server kan bepaalde fouten in een transactie verdragen zonder dat deze als vrijblijvend hoeft te worden gemarkeerd. Bijvoorbeeld, `SELECT 1/0` zou leiden tot een fout, maar niet dwingen een transactie in een niet-committable staat. 

SQL Server maakt ook reads in de vrijblijvende transactie mogelijk. In SQL-pool u dit echter niet doen. Als er een fout optreedt binnen een SQL-pooltransactie, wordt automatisch de status -2 ingevoerd en u geen verdere selectieverklaringen uitvoeren totdat de instructie is teruggedraaid. 

Als zodanig is het belangrijk om te controleren of uw toepassingscode om te zien of het XACT_STATE() gebruikt, omdat u mogelijk codewijzigingen moet aanbrengen.


In SQL Server ziet u bijvoorbeeld een transactie die er als volgt uitziet:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

De voorgaande code geeft het volgende foutbericht:

Msg 111233, niveau 16, staat 1, lijn 1 111233; De huidige transactie is afgebroken en wijzigingen in behandeling zijn teruggedraaid. De oorzaak van dit probleem is dat een transactie in een terugdraaistatus niet expliciet wordt teruggedraaid vóór een DDL-, DML- of SELECT-instructie.

U krijgt de uitvoer van de ERROR_* functies niet.

In SQL-pool moet de code enigszins worden gewijzigd:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Het verwachte gedrag wordt nu waargenomen. De fout in de transactie wordt beheerd en de ERROR_* functies geven waarden zoals verwacht.

Het enige dat is veranderd, is dat de terugdraaiing van de transactie moest plaatsvinden voordat de foutinformatie in het CATCH-blok werd gelezen.

## <a name="error_line-function"></a>Error_Line()
Het is ook vermeldenswaard dat SQL-pool de functie ERROR_LINE() niet implementeert of ondersteunt. Als u dit in uw code hebt, moet u deze verwijderen om te voldoen aan SQL-pool. 

Gebruik querylabels in uw code in plaats daarvan om gelijkwaardige functionaliteit te implementeren. Zie het artikel [LABEL voor](sql-data-warehouse-develop-label.md) meer informatie.

## <a name="using-throw-and-raiserror"></a>Throw en RAISERROR gebruiken
THROW is de modernere implementatie voor het verhogen van uitzonderingen in SQL-pool, maar RAISERROR wordt ook ondersteund. Er zijn een paar verschillen die de moeite waard zijn aandacht te besteden aan echter.

* Door de gebruiker gedefinieerde foutberichtennummers kunnen niet in het bereik van 100.000 - 150.000 voor THROW
* RAISERROR foutmeldingen zijn vastgesteld op 50.000
* Het gebruik van sys.messages wordt niet ondersteund

## <a name="limitations"></a>Beperkingen
SQL-groep heeft een paar andere beperkingen die betrekking hebben op transacties.

De verschillen zijn als volgt:

* Geen gedistribueerde transacties
* Geen geneste transacties toegestaan
* Geen save points toegestaan
* Geen benoemde transacties
* Geen gemarkeerde transacties
* Geen ondersteuning voor DDL, zoals CREATE TABLE in een door de gebruiker gedefinieerde transactie

## <a name="next-steps"></a>Volgende stappen
Zie [Aanbevolen procedures voor](sql-data-warehouse-develop-best-practices-transactions.md)transacties voor meer informatie over het optimaliseren van transacties. Zie Best [practices voor SQL Pool](sql-data-warehouse-best-practices.md)voor meer informatie over andere best practices voor SQL-pool.

