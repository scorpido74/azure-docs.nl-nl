---
title: Voorbeeld van in-geheugen
description: Probeer Azure SQL Database In-Memory-technologieën met OLTP- en columnstore-voorbeeld.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: e7e7fc44d5f8b46a66c698d3a33ceeab5b8625c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73810331"
---
# <a name="in-memory-sample"></a>Voorbeeld van in-geheugen

Met in-memorytechnologieën in Azure SQL Database u de prestaties van uw toepassing verbeteren en de kosten van uw database mogelijk verlagen. Door in-memory-technologieën in Azure SQL Database te gebruiken, u prestatieverbeteringen realiseren met verschillende workloads.

In dit artikel ziet u twee voorbeelden die het gebruik van In-Memory OLTP illustreren, evenals columnstore-indexen in Azure SQL Database.

Zie voor meer informatie:
- [In-Memory OLTP-overzichts- en gebruiksscenario's](https://msdn.microsoft.com/library/mt774593.aspx) (inclusief verwijzingen naar casestudies van klanten en informatie om aan de slag te gaan)
- [Documentatie voor in-memory OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [Columnstore Indexen Gids](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybride transactionele/analytische verwerking (HTAP), ook wel [real-time operationele analyse genoemd](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Installeer het in-memory OLTP-voorbeeld

U de AdventureWorksLT-voorbeelddatabase met een paar klikken maken in de [Azure-portal.](https://portal.azure.com/) Vervolgens leggen de stappen in deze sectie uit hoe u uw AdventureWorksLT-database verrijken met OLTP-objecten in het geheugen en prestatievoordelen aantonen.

Voor een meer simplistische, maar meer visueel aantrekkelijke performance demo voor In-Memory OLTP, zie:

- Release: [in-memory-oltp-demo-v1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Broncode: [in-memory-oltp-demo-source-code](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/in-memory/ticket-reservations)

#### <a name="installation-steps"></a>Installatiestappen

1. Maak in de [Azure-portal](https://portal.azure.com/)een Premium- of Bedrijfskritische database op een server. Stel de **bron** in op de AdventureWorksLT-voorbeelddatabase. Zie [Uw eerste Azure SQL-database maken](sql-database-single-database-get-started.md)voor gedetailleerde instructies.

2. Maak verbinding met de database met SQL Server Management Studio [(SSMS.exe).](https://msdn.microsoft.com/library/mt238290.aspx)

3. Kopieer het [IN-Memory OLTP Transact-SQL-script](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) naar het klembord. Het T-SQL-script maakt de benodigde In-Memory-objecten in de AdventureWorksLT-voorbeelddatabase die u in stap 1 hebt gemaakt.

4. Plak het T-SQL-script in SSMS en voer het script uit. De `MEMORY_OPTIMIZED = ON` clausule CREATE TABLE statements zijn cruciaal. Bijvoorbeeld:


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Fout 40536


Als u fout 40536 krijgt wanneer u het T-SQL-script uitvoert, voert u het volgende T-SQL-script uit om te controleren of de database In-Memory ondersteunt:


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Een resultaat van **0** betekent dat In-Memory niet wordt ondersteund en **1** betekent dat het wordt ondersteund. Als u het probleem wilt diagnosticeren, moet u ervoor zorgen dat de database zich op de servicelaag Premium bevindt.


#### <a name="about-the-created-memory-optimized-items"></a>Over de gemaakte items die zijn geoptimaliseerd voor geheugen

**Tabellen**: Het voorbeeld bevat de volgende tabellen die zijn geoptimaliseerd voor geheugen:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesorderheaderSeed
- Demo.DemoSalesOrderDetailseed


U geheugengeoptimaliseerde tabellen inspecteren via de **Object Explorer** in SSMS. Met de **Tables** > rechtermuisknop op**De filterinstellingen** > **voor tabellen** > **filteren is geheugen geoptimaliseerd**. De waarde is gelijk aan 1.


U ook de catalogusweergaven opvragen, zoals:


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Native gecompileerde opgeslagen procedure**: U SalesLT.usp_InsertSalesOrder_inmem inspecteren via een catalogusweergavequery:


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>De oltp-werkbelasting van het voorbeeld uitvoeren

Het enige verschil tussen de volgende twee *opgeslagen procedures* is dat de eerste procedure geheugengeoptimaliseerde versies van de tabellen gebruikt, terwijl de tweede procedure de reguliere tabellen op de schijf gebruikt:

- SalesLT **.** usp_InsertSalesOrder **_inmem**
- SalesLT **.** usp_InsertSalesOrder **_ondisk**


In deze sectie ziet u hoe u de handige **ostress.exe-hulpprogramma** gebruiken om de twee opgeslagen procedures op stressvolle niveaus uit te voeren. U vergelijken hoe lang het duurt voor de twee stress loopt te voltooien.


Wanneer u ostress.exe uitvoert, raden we u aan parameterwaarden door te geven die zijn ontworpen voor beide van de volgende opties:

- Voer een groot aantal gelijktijdige verbindingen uit met de n100.
- Heb elke verbindingslus honderden keren, met behulp van -r500.


U echter beginnen met veel kleinere waarden zoals -n10 en -r50 om ervoor te zorgen dat alles werkt.


### <a name="script-for-ostressexe"></a>Script voor ostress.exe


In deze sectie wordt het T-SQL-script weergegeven dat is ingesloten in onze opdrachtregel ostress.exe. Het script maakt gebruik van items die zijn gemaakt door het T-SQL-script dat u eerder hebt geïnstalleerd.


In het volgende script wordt een voorbeeldverkooporder met vijf regelitems ingevoegd in de volgende voor geheugen geoptimaliseerde *tabellen:*

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```sql
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;

INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);

WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Als u de *_ondisk-versie* van het voorgaande T-SQL-script voor ostress.exe wilt maken, vervangt u beide exemplaren van de *_inmem* substring door *_ondisk*. Deze vervangingen zijn van invloed op de namen van tabellen en opgeslagen procedures.


### <a name="install-rml-utilities-and-ostress"></a>RML-hulpprogramma's installeren en`ostress`


Idealiter zou u van plan zijn om ostress.exe uit te voeren op een Virtuele Azure-machine (VM). U maakt een [Azure VM](https://azure.microsoft.com/documentation/services/virtual-machines/) in dezelfde Azure-geografische regio waar uw AdventureWorksLT-database zich bevindt. Maar u ostress.exe draaien op uw laptop plaats.


Installeer de RML-hulpprogramma's (Replay Markup Language) op de virtuele machine, of op welke host u ook kiest. De nutsbedrijven omvatten ostress.exe.

Zie voor meer informatie:
- De ostress.exe discussie in [Sample Database voor In-Memory OLTP](https://msdn.microsoft.com/library/mt465764.aspx).
- [Voorbeelddatabase voor in-memory OLTP](https://msdn.microsoft.com/library/mt465764.aspx).
- De [blog voor het installeren van ostress.exe](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-_inmem-stress-workload-first"></a>Voer eerst de *_inmem* stresswerklast uit


U een *RML Cmd Prompt-venster* gebruiken om onze opdrachtregel ostress.exe uit te voeren. De opdrachtregelparameters `ostress` gaan rechtstreeks naar:

- Voer gelijktijdig 100 verbindingen uit (-n100).
- Laat elke verbinding het T-SQL-script 50 keer uitvoeren (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Ga als u de voorgaande opdrachtregel ostress.exe wilt uitvoeren:


1. De inhoud van de databasegegevens opnieuw instellen door de volgende opdracht in SSMS uit te voeren om alle gegevens te verwijderen die door eerdere uitvoeringen zijn ingevoegd:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Kopieer de tekst van de voorgaande opdrachtregel ostress.exe naar het klembord.

3. Vervang `<placeholders>` de parameters -S -U-P-d door de juiste reële waarden.

4. Voer de bewerkte opdrachtregel uit in een RML Cmd-venster.


#### <a name="result-is-a-duration"></a>Resultaat is een duur


Wanneer `ostress.exe` de looptijd is voltooid, wordt de duur van de uitvoering als de laatste uitvoerlijn in het RML Cmd-venster geschreven. Een kortere testrun duurde bijvoorbeeld ongeveer 1,5 minuut:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-_ondisk-then-rerun"></a>Opnieuw instellen, bewerken voor *_ondisk*en vervolgens opnieuw worden uitgevoerd


Nadat u het resultaat van de *_inmem* run hebt uitgevoerd, voert u de volgende stappen uit voor de *_ondisk* uitvoeren:


1. Stel de database opnieuw in door de volgende opdracht in SSMS uit te voeren om alle gegevens te verwijderen die door de vorige run zijn ingevoegd:
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Bewerk de opdrachtregel ostress.exe om alle *_inmem* te vervangen door *_ondisk*.

3. Voer ostress.exe voor de tweede keer opnieuw uit en leg het duurresultaat vast.

4. Nogmaals, reset de database (voor het op verantwoorde wijze verwijderen van wat een grote hoeveelheid testgegevens kan zijn).


#### <a name="expected-comparison-results"></a>Verwachte vergelijkingsresultaten

Onze In-Memory-tests hebben aangetoond dat de prestaties met `ostress` negen **keer** verbeterden voor deze simplistische werkbelasting, waarbij ze worden uitgevoerd op een Azure VM in dezelfde Azure-regio als de database.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Installeer het voorbeeld van In-Memory Analytics


In deze sectie vergelijkt u de IO- en statistiekenresultaten wanneer u een kolomarchiefindex gebruikt ten opzichte van een traditionele b-tree-index.


Voor realtime analyses op een OLTP-workload u vaak het beste een niet-geclusterde kolomarchiefindex gebruiken. Zie [Columnstore-indexen beschreven](https://msdn.microsoft.com/library/gg492088.aspx)voor meer informatie .



### <a name="prepare-the-columnstore-analytics-test"></a>De analysetest voor columnstore voorbereiden


1. Gebruik de Azure-portal om een nieuwe AdventureWorksLT-database te maken in het voorbeeld.
   - Gebruik die exacte naam.
   - Kies een Premium-servicelaag.

2. Kopieer de [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) naar uw klembord.
   - Het T-SQL-script maakt de benodigde In-Memory-objecten in de AdventureWorksLT-voorbeelddatabase die u in stap 1 hebt gemaakt.
   - Het script maakt de tabel Dimensie en twee feitentabellen. De feitlijsten worden bevolkt met 3.5 miljoen rijen elk.
   - Het kan 15 minuten duren voordat het script is voltooid.

3. Plak het T-SQL-script in SSMS en voer het script uit. Het **trefwoord COLUMNSTORE** in de instructie **CREATE INDEX** is cruciaal, zoals in:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Stel AdventureWorksLT in op compatibiliteitsniveau 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Niveau 130 is niet direct gerelateerd aan in-memory functies. Maar niveau 130 biedt over het algemeen snellere queryprestaties dan 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Sleuteltabellen en kolomarchiefindexen


- Dbo. FactResellerSalesXL_CCI is een tabel met een geclusterde kolomarchiefindex, die geavanceerde compressie op *gegevensniveau* heeft.

- Dbo. FactResellerSalesXL_PageCompressed is een tabel met een gelijkwaardige reguliere geclusterde index, die alleen op *paginaniveau* wordt gecomprimeerd.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Belangrijkste query's om de kolomarchiefindex te vergelijken


Er zijn [verschillende T-SQL-querytypen die u uitvoeren](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql) om prestatieverbeteringen te zien. In stap 2 in het T-SQL-script moet u aandacht besteden aan dit paar query's. Ze verschillen slechts op één regel:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Een geclusterde columnstore-index bevindt\_zich in de FactResellerSalesXL CCI-tabel.

In het volgende T-SQL-scriptfragment worden statistieken voor IO en TIME afgedrukt voor de query van elke tabel.


```sql
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a clustered columnstore index CCI
-- The comparison numbers are even more dramatic the larger the table is (this is an 11 million row table only)
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```

In een database met de P2-prijslaag u ongeveer negen keer de prestatiewinst voor deze query verwachten met behulp van de geclusterde kolomarchiefindex in vergelijking met de traditionele index. Met P15 u ongeveer 57 keer de prestatiewinst verwachten met behulp van de kolomarchiefindex.



## <a name="next-steps"></a>Volgende stappen

- [Quickstart 1: In-Memory OLTP-technologieën voor snellere T-SQL-prestaties](https://msdn.microsoft.com/library/mt694156.aspx)

- [In-Memory OLTP gebruiken in een bestaande Azure SQL-toepassing](sql-database-in-memory-oltp-migration.md)

- [Monitor In-Memory OLTP-opslag](sql-database-in-memory-oltp-monitoring.md) voor in-memory OLTP


## <a name="additional-resources"></a>Aanvullende bronnen

#### <a name="deeper-information"></a>Diepere informatie

- [Ontdek hoe Quorum de werkbelasting van de sleuteldatabase verdubbelt en DTU met 70% verlaagt met In-Memory OLTP in SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [In-Memory OLTP in Azure SQL Database Blog Post](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Meer informatie over in-memory OLTP](https://msdn.microsoft.com/library/dn133186.aspx)

- [Meer informatie over kolomarchiefindexen](https://msdn.microsoft.com/library/gg492088.aspx)

- [Meer informatie over realtime operationele analyses](https://msdn.microsoft.com/library/dn817827.aspx)

- Zie [algemene werkbelastingpatronen en migratieoverwegingen](https://msdn.microsoft.com/library/dn673538.aspx) (waarin werkbelastingpatronen worden beschreven waarin OLTP in het geheugen vaak aanzienlijke prestatieverbeteringen oplevert)

#### <a name="application-design"></a>Toepassingsontwerp

- [In-Memory OLTP (In-Memory Optimization)](https://msdn.microsoft.com/library/dn133186.aspx)

- [In-Memory OLTP gebruiken in een bestaande Azure SQL-toepassing](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Hulpprogramma's

- [Azure-portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
