---
title: Voor beeld in het geheugen
description: Probeer Azure SQL Database in-Memory technologieën met OLTP en column Store-voor beeld.
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
ms.openlocfilehash: b707d67c88eb550d397134b2294c1c5b0e1f7f7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80528221"
---
# <a name="in-memory-sample"></a>Voor beeld in het geheugen

In de geheugen technologieën in Azure SQL Database kunt u de prestaties van uw toepassing verbeteren en mogelijk de kosten van uw data base verminderen. Door gebruik te maken van in-Memory technologieën in Azure SQL Database kunt u prestatie verbeteringen door diverse werk belastingen krijgen.

In dit artikel ziet u twee voor beelden die het gebruik van in-Memory OLTP en column Store-indexen in Azure SQL Database illustreren.

Zie voor meer informatie:
- [Overzicht van in-Memory OLTP en gebruiks scenario's](https://msdn.microsoft.com/library/mt774593.aspx) (bevat verwijzingen naar casestudy's van klanten en informatie om aan de slag te gaan)
- [Documentatie voor in-Memory OLTP](https://msdn.microsoft.com/library/dn133186.aspx)
- [Hand leiding voor column Store-indexen](https://msdn.microsoft.com/library/gg492088.aspx)
- Hybride transactionele/analytische verwerking (HTAP), ook wel bekend als [real-time Operational Analytics](https://msdn.microsoft.com/library/dn817827.aspx)

<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="1-install-the-in-memory-oltp-sample"></a>1. Installeer het voor beeld in het geheugen OLTP

U kunt de AdventureWorksLT-voorbeeld database maken met enkele klikken in de [Azure Portal](https://portal.azure.com/). In de stappen in dit gedeelte wordt uitgelegd hoe u uw AdventureWorksLT-Data Base kunt verrijken met in-Memory OLTP-objecten en de prestatie voordelen demonstreert.

Voor een meer vereenvoudigde, maar visueel aantrekkelijke prestatie demo voor in-Memory OLTP, zie:

- Release: [in-Memory-OLTP-demo-v 1.0](https://github.com/Microsoft/sql-server-samples/releases/tag/in-memory-oltp-demo-v1.0)
- Bron code: [in-Memory-OLTP-demo-bron code](https://github.com/microsoft/sql-server-samples/tree/master/samples/features/in-memory-database)

#### <a name="installation-steps"></a>Installatiestappen

1. Maak in de [Azure Portal](https://portal.azure.com/)een Premium-of bedrijfskritiek-Data Base op een server. Stel de **bron** in op de voorbeeld database AdventureWorksLT. Zie [uw eerste Azure-SQL database maken](sql-database-single-database-get-started.md)voor gedetailleerde instructies.

2. Maak verbinding met de data base met SQL Server Management Studio [(SSMS. exe)](https://msdn.microsoft.com/library/mt238290.aspx).

3. Kopieer het [Transact-SQL-script van OLTP in het geheugen](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_oltp_sample.sql) naar het klem bord. Het T-SQL-script maakt de benodigde in-Memory objecten in de AdventureWorksLT-voorbeeld database die u in stap 1 hebt gemaakt.

4. Plak het T-SQL-script in SSMS en voer het script uit. De `MEMORY_OPTIMIZED = ON` component CREATE TABLE-instructies zijn van cruciaal belang. Bijvoorbeeld:


```sql
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Fout 40536


Als u fout 40536 krijgt wanneer u het T-SQL-script uitvoert, voert u het volgende T-SQL-script uit om te controleren of de data base in-Memory ondersteunt:


```sql
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Een resultaat van **0** betekent dat het geheugen niet wordt ondersteund en **1** betekent dat het wordt ondersteund. Zorg ervoor dat de data base zich in de laag Premium bevindt om de oorzaak van het probleem vast te stellen.


#### <a name="about-the-created-memory-optimized-items"></a>Over de gemaakte items die zijn geoptimaliseerd voor geheugen

**Tables**: het voor beeld bevat de volgende tabellen die zijn geoptimaliseerd voor geheugen:

- Tabel saleslt. Product_inmem
- Tabel saleslt. SalesOrderHeader_inmem
- Tabel saleslt. SalesOrderDetail_inmem
- Demo. DemoSalesOrderHeaderSeed
- Demo. DemoSalesOrderDetailSeed


U kunt tabellen die zijn geoptimaliseerd voor geheugen, inspecteren via de **objectverkenner** in SSMS. Klik met de rechter muisknop op **tabellen** > **filter** > **filter instellingen** > **is geoptimaliseerd voor geheugen**. De waarde is gelijk aan 1.


U kunt ook een query uitvoeren op de catalogus weergaven, zoals:


```sql
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Systeem eigen, gecompileerde, opgeslagen procedure**: u kunt tabel saleslt. usp_InsertSalesOrder_inmem inspecteren met een query in de catalogus weergave:


```sql
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

### <a name="run-the-sample-oltp-workload"></a>De voor beeld van de OLTP-workload uitvoeren

Het enige verschil tussen de volgende twee *opgeslagen procedures* is dat in de eerste procedure de versies van de tabellen die zijn geoptimaliseerd voor geheugen, worden gebruikt, terwijl de tweede procedure gebruikmaakt van de normale on-disk tabellen:

- Tabel saleslt **.** usp_InsertSalesOrder **_inmem**
- Tabel saleslt **.** usp_InsertSalesOrder **_ondisk**


In deze sectie ziet u hoe u het handige **ostress. exe** -hulp programma gebruikt om de twee opgeslagen procedures op stress niveau uit te voeren. U kunt vergelijken hoe lang het duurt om de twee stress-uitvoeringen te volt ooien.


Wanneer u ostress. exe uitvoert, wordt u aangeraden parameter waarden door te geven die zijn ontworpen voor het volgende:

- Voer een groot aantal gelijktijdige verbindingen uit met behulp van-N100.
- Laat elke verbinding honderden keer worden herhaald met behulp van-R500.


Het is echter verstandig om te beginnen met veel kleinere waarden, zoals-N10 en-R50, om ervoor te zorgen dat alles werkt.


### <a name="script-for-ostressexe"></a>Script voor ostress. exe


In deze sectie wordt het T-SQL-script weer gegeven dat is inge sloten in onze ostress. exe-opdracht regel. Het script maakt gebruik van items die zijn gemaakt door het T-SQL-script dat u eerder hebt geïnstalleerd.


Met het volgende script wordt een voor beeld van een verkoop order met vijf regel items ingevoegd in de volgende *tabellen*die zijn geoptimaliseerd voor geheugen:

- Tabel saleslt. SalesOrderHeader_inmem
- Tabel saleslt. SalesOrderDetail_inmem


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


Als u de *_ondisk* versie van het vorige T-SQL-script voor ostress. exe wilt maken, vervangt u beide exemplaren van de *_inmem* subtekenreeks door *_ondisk*. Deze vervangingen zijn van invloed op de namen van tabellen en opgeslagen procedures.


### <a name="install-rml-utilities-and-ostress"></a>Installeer RML-hulpprogram ma's en`ostress`


In het ideale geval wilt u ostress. exe uitvoeren op een virtuele Azure-machine (VM). U maakt een [virtuele Azure-machine](https://azure.microsoft.com/documentation/services/virtual-machines/) in dezelfde Azure-geografische regio waar uw AdventureWorksLT-data base zich bevindt. Maar u kunt in plaats daarvan ostress. exe uitvoeren op uw laptop.


Installeer de RML-hulpprogram ma's (Replay Markup Language) op de virtuele machine, of op welke host u ook kiest. De hulpprogram ma's bevatten ostress. exe.

Zie voor meer informatie:
- De ostress. exe-discussie in de [voorbeeld database voor in-Memory OLTP](https://msdn.microsoft.com/library/mt465764.aspx).
- [Voorbeeld database voor in-Memory OLTP](https://msdn.microsoft.com/library/mt465764.aspx).
- De [blog voor het installeren van ostress. exe](https://blogs.msdn.com/b/psssql/archive/20../../cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx).



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(https://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(https://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-_inmem-stress-workload-first"></a>Voer eerst de *_inmem* stress-workload uit


U kunt een *RML cmd prompt* -venster gebruiken om de ostress. exe-opdracht regel uit te voeren. De opdracht regel parameters direct `ostress` naar:

- 100-verbindingen gelijktijdig uitvoeren (-N100).
- Laat elke verbinding het T-SQL-script 50 keer (-R50) uitvoeren.


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


De voor gaande ostress. exe-opdracht regel uitvoeren:


1. De inhoud van de database gegevens opnieuw instellen door de volgende opdracht uit te voeren in SSMS, om alle gegevens te verwijderen die zijn ingevoegd door eerdere uitvoeringen:

    ``` tsql
    EXECUTE Demo.usp_DemoReset;
    ```

2. Kopieer de tekst van de voor gaande ostress. exe-opdracht regel naar het klem bord.

3. Vervang de `<placeholders>` para meters-S-U-P-d door de juiste werkelijke waarden.

4. Voer de bewerkte opdracht regel uit in een RML CMD-venster.


#### <a name="result-is-a-duration"></a>Resultaat is een duur


Als `ostress.exe` u klaar bent, wordt de uitvoerings duur als laatste uitvoer regel geschreven in het RML CMD-venster. Een korte test uitvoering heeft bijvoorbeeld ongeveer 1,5 minuten geduren:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-_ondisk-then-rerun"></a>Opnieuw instellen, bewerken voor *_ondisk*en vervolgens opnieuw uitvoeren


Nadat u het resultaat van de *_inmem* uitvoeren hebt, voert u de volgende stappen uit om de *_ondisk* uit te voeren:


1. Stel de data base opnieuw in door de volgende opdracht uit te voeren in SSMS om alle gegevens te verwijderen die zijn ingevoegd door de vorige uitvoering:
   ```sql
   EXECUTE Demo.usp_DemoReset;
   ```

2. Bewerk de ostress. exe-opdracht regel om alle *_inmem* te vervangen door *_ondisk*.

3. Voer ostress. exe voor de tweede keer opnieuw uit en leg het resultaat van de duur vast.

4. Stel de data base opnieuw in (voor vergoedd verwijderen wat kan een grote hoeveelheid test gegevens zijn).


#### <a name="expected-comparison-results"></a>Verwachte vergelijkings resultaten

Onze tests in het geheugen hebben laten zien dat de prestaties zijn verbeterd door **negen keer** voor deze vereenvoudigde `ostress` -werk belasting, waarbij wordt uitgevoerd op een virtuele Azure-machine in dezelfde Azure-regio als de data base.

<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;

## <a name="2-install-the-in-memory-analytics-sample"></a>2. Installeer het voor beeld in het geheugen analyse


In deze sectie gaat u de resultaten van de i/o-en statistieken vergelijken wanneer u een column store-index gebruikt in plaats van een traditionele b-structuur index.


Voor realtime analyses van een OLTP-werk belasting is het vaak het beste om een niet-geclusterde column store-index te gebruiken. Zie [Column Store-indexen](https://msdn.microsoft.com/library/gg492088.aspx)die worden beschreven voor meer informatie.



### <a name="prepare-the-columnstore-analytics-test"></a>De column Store Analytics-test voorbereiden


1. Gebruik de Azure Portal om een nieuwe AdventureWorksLT-data base te maken op basis van het voor beeld.
   - Gebruik die exacte naam.
   - Kies een Premium-servicelaag.

2. Kopieer de [sql_in-memory_analytics_sample](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/sql_in-memory_analytics_sample.sql) naar het klem bord.
   - Het T-SQL-script maakt de benodigde in-Memory objecten in de AdventureWorksLT-voorbeeld database die u in stap 1 hebt gemaakt.
   - Het script maakt de dimensie tabel en twee feiten tabellen. De feiten tabellen worden gevuld met 3.500.000 rijen.
   - Het uitvoeren van het script kan 15 minuten duren.

3. Plak het T-SQL-script in SSMS en voer het script uit. Het sleutel woord **Column Store** in de instructie **Create Index** is van cruciaal belang, zoals in:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Stel AdventureWorksLT in op compatibiliteits niveau 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`

    Niveau 130 is niet direct gerelateerd aan de functies in het geheugen. Maar niveau 130 biedt doorgaans snellere query prestaties dan 120.


#### <a name="key-tables-and-columnstore-indexes"></a>Sleutel tabellen en column Store-indexen


- dbo. FactResellerSalesXL_CCI is een tabel met een geclusterde column store-index, die een geavanceerde compressie heeft op het *gegevens* niveau.

- dbo. FactResellerSalesXL_PageCompressed is een tabel die een equivalente reguliere geclusterde index heeft, die alleen op *pagina* niveau wordt gecomprimeerd.


#### <a name="key-queries-to-compare-the-columnstore-index"></a>Belangrijkste query's voor het vergelijken van de column store-index


Er zijn [verschillende typen T-SQL-query's die u kunt uitvoeren](https://raw.githubusercontent.com/microsoft/sql-server-samples/master/samples/features/in-memory-database/in-memory-oltp/t-sql-scripts/clustered_columnstore_sample_queries.sql) om prestatie verbeteringen te bekijken. In stap 2 van het T-SQL-script moet u aandacht best Eden aan dit paar query's. Ze verschillen alleen op één regel:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


Een geclusterde column store-index bevindt zich in de FactResellerSalesXL\_CCI-tabel.

In het volgende T-SQL-script fragment worden de statistieken voor de i/o en het tijdstip voor de query van elke tabel afgedrukt.


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

In een Data Base met de prijs categorie P2 kunt u ongeveer negen keer de prestatie winst voor deze query verwachten door de geclusterde column store-index te gebruiken in vergelijking met de traditionele index. Met P15 kunt u verwachten dat de prestaties ongeveer 57 keer zo goed mogelijk zijn door gebruik te maken van de column store-index.



## <a name="next-steps"></a>Volgende stappen

- [Snelstartgids 1: OLTP-technologieën in het geheugen voor snellere T-SQL-prestaties](https://msdn.microsoft.com/library/mt694156.aspx)

- [In-Memory OLTP gebruiken in een bestaande Azure SQL-toepassing](sql-database-in-memory-oltp-migration.md)

- [OLTP-opslag in het geheugen bewaken](sql-database-in-memory-oltp-monitoring.md) voor OLTP in het geheugen


## <a name="additional-resources"></a>Aanvullende bronnen

#### <a name="deeper-information"></a>Dieper informatie

- [Meer informatie over hoe quorum de werk belasting van de Key Data Base verdubbelt tijdens het verlagen van DTU met 70% met in-Memory OLTP in SQL Database](https://customers.microsoft.com/story/quorum-doubles-key-databases-workload-while-lowering-dtu-with-sql-database)

- [In-Memory OLTP in Azure SQL Database blog bericht](https://azure.microsoft.com/blog/in-memory-oltp-in-azure-sql-database/)

- [Meer informatie over in-Memory OLTP](https://msdn.microsoft.com/library/dn133186.aspx)

- [Meer informatie over column Store-indexen](https://msdn.microsoft.com/library/gg492088.aspx)

- [Meer informatie over real-time operationele analyses](https://msdn.microsoft.com/library/dn817827.aspx)

- Bekijk [algemene werk belasting patronen en migratie overwegingen](https://msdn.microsoft.com/library/dn673538.aspx) (waarin de werkbelasting patronen worden beschreven waarbij in-Memory OLTP doorgaans aanzienlijke prestatie verbeteringen oplevert)

#### <a name="application-design"></a>Toepassingsontwerp

- [OLTP in het geheugen (in-Memory optimalisatie)](https://msdn.microsoft.com/library/dn133186.aspx)

- [In-Memory OLTP gebruiken in een bestaande Azure SQL-toepassing](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Hulpprogramma's

- [Azure Portal](https://portal.azure.com/)

- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx)

- [SQL Server Data Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx)
