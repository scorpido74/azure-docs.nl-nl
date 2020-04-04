---
title: TABEL MAKEN ALS SELECTEREN (CTAS)
description: Uitleg en voorbeelden van de CTAS-verklaring (CREATE TABLE AS SELECT) in Synapse SQL voor het ontwikkelen van oplossingen.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/26/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seoapril2019, azure-synapse
ms.openlocfilehash: 8e1b75dfc6a979956ff4a2868027bb769bf7c4ed
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633553"
---
# <a name="create-table-as-select-ctas"></a>TABEL MAKEN ALS SELECTEREN (CTAS)

In dit artikel wordt de CTAS-instructie (CREATE TABLE AS SELECT) in Synapse SQL uitgelegd voor het ontwikkelen van oplossingen. Het artikel bevat ook codevoorbeelden.

## <a name="create-table-as-select"></a>CREATE TABLE AS SELECT

De CTAS-instructie [(CREATE TABLE AS SELECT)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) is een van de belangrijkste T-SQL-functies die beschikbaar zijn. CTAS is een parallelle bewerking die een nieuwe tabel maakt op basis van de uitvoer van een SELECT-instructie. CTAS is de eenvoudigste en snelste manier om met één opdracht gegevens te maken en in te voegen in een tabel.

## <a name="selectinto-vs-ctas"></a>Selecteer... INTO vs. CTAS

CTAS is een meer aanpasbare versie van de [SELECT ... INTO](/sql/t-sql/queries/select-into-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) verklaring.

Het volgende is een voorbeeld van een eenvoudige SELECT... In:

```sql
SELECT *
INTO    [dbo].[FactInternetSales_new]
FROM    [dbo].[FactInternetSales]
```

Selecteer... Into staat u niet toe om de distributiemethode of het indextype te wijzigen als onderdeel van de bewerking. U `[dbo].[FactInternetSales_new]` maakt met behulp van het standaarddistributietype ROUND_ROBIN en de standaardtabelstructuur van CLUSTERED COLUMNSTORE INDEX.

Met CTAS u daarentegen zowel de verdeling van de tabelgegevens als het type tabelstructuur opgeven. Ga als u het vorige voorbeeld converteert naar CTAS:

```sql
CREATE TABLE [dbo].[FactInternetSales_new]
WITH
(
 DISTRIBUTION = ROUND_ROBIN
 ,CLUSTERED COLUMNSTORE INDEX
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales];
```

> [!NOTE]
> Als u alleen de index in uw CTAS-bewerking probeert te wijzigen en de brontabel hash is gedistribueerd, houdt u dezelfde distributiekolom en hetzelfde gegevenstype. Dit voorkomt cross-distributie gegevens verkeer tijdens de operatie, die efficiënter is.

## <a name="use-ctas-to-copy-a-table"></a>CTAS gebruiken om een tabel te kopiëren

Misschien is een van de meest voorkomende toepassingen van CTAS is het maken van een kopie van een tabel om de DDL te veranderen. Stel dat u uw tabel `ROUND_ROBIN`oorspronkelijk hebt gemaakt als , en deze nu wilt wijzigen in een tabel die in een kolom is verdeeld. CTAS is hoe u de distributiekolom zou wijzigen. U CTAS ook gebruiken om partitie-, indexerings- of kolomtypen te wijzigen.

Stel dat u deze tabel hebt gemaakt met `ROUND_ROBIN`het standaarddistributietype , `CREATE TABLE`waarbij u geen distributiekolom opgeeft in de .

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25));
```

Nu wilt u een nieuwe kopie van `Clustered Columnstore Index`deze tabel maken, met een, zodat u profiteren van de prestaties van geclusterde kolomarchieftabellen. U wilt deze tabel `ProductKey`ook distribueren over , omdat u op joins in deze `ProductKey`kolom anticipeert en gegevensverplaatsing wilt voorkomen tijdens joins op . Ten slotte wilt u ook partitionering toevoegen aan `OrderDateKey`, zodat u snel oude gegevens verwijderen door oude partities te laten vallen. Hier is de CTAS-verklaring, die uw oude tabel kopieert in een nieuwe tabel.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Ten slotte u de naam van uw tabellen wijzigen, om te wisselen in uw nieuwe tabel en vervolgens uw oude tafel laten vallen.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

## <a name="use-ctas-to-work-around-unsupported-features"></a>CTAS gebruiken om niet-ondersteunde functies te omzeilen

U CTAS ook gebruiken om te werken rond een aantal van de niet-ondersteunde functies hieronder. Deze methode kan vaak nuttig zijn, omdat niet alleen uw code compatibel is, maar het zal vaak sneller draaien op Synapse SQL. Deze prestatie is het resultaat van het volledig parallelle ontwerp. Scenario's zijn onder andere:

* ANSI sluit zich aan bij updates
* ANSI JOINs op DELETEs
* Samenvoegen-instructie

> [!TIP]
> Probeer eerst 'CTAS' te denken. Het oplossen van een probleem met behulp van CTAS is over het algemeen een goede aanpak, zelfs als je het schrijven van meer gegevens als gevolg.

## <a name="ansi-join-replacement-for-update-statements"></a>ANSI sluit zich aan bij vervanging voor update-instructies

Mogelijk vindt u dat u een complexe update hebt. De update voegt meer dan twee tabellen samen met behulp van de syntaxis van ANSI join om de UPDATE of DELETE uit te voeren.

Stel je voor dat je deze tabel moest bijwerken:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
( [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
, [CalendarYear]                    SMALLINT        NOT NULL
, [TotalSalesAmount]                MONEY            NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
);
```

De oorspronkelijke query zag er mogelijk ongeveer zo uit als dit voorbeeld:

```sql
UPDATE    acs
SET        [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT [EnglishProductCategoryName]
        , [CalendarYear]
        , SUM([SalesAmount])                AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]        AS s
        JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
        JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
        WHERE     [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,        [CalendarYear]
        ) AS fis
ON    [acs].[EnglishProductCategoryName]    = [fis].[EnglishProductCategoryName]
AND    [acs].[CalendarYear]                = [fis].[CalendarYear];
```

Synapse SQL ondersteunt geen ANSI-joins in de `FROM` clausule van een `UPDATE` instructie, dus u het vorige voorbeeld niet gebruiken zonder het te wijzigen.

U een combinatie van een CTAS en een impliciete join gebruiken om het vorige voorbeeld te vervangen:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT    ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0) AS [EnglishProductCategoryName]
, ISNULL(CAST([CalendarYear] AS SMALLINT),0)  AS [CalendarYear]
, ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)  AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]        AS s
JOIN    [dbo].[DimDate]                    AS d    ON s.[OrderDateKey]                = d.[DateKey]
JOIN    [dbo].[DimProduct]                AS p    ON s.[ProductKey]                = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]    AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]        AS c    ON u.[ProductCategoryKey]        = c.[ProductCategoryKey]
WHERE     [CalendarYear] = 2004
GROUP BY [EnglishProductCategoryName]
, [CalendarYear];

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]  = AnnualCategorySales.[CalendarYear] ;

--Drop the interim table
DROP TABLE CTAS_acs;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>ANSI sluit zich aan bij vervanging voor verwijderingsverklaringen

Soms is de beste aanpak voor het verwijderen van `DELETE` gegevens is het gebruik van CTAS, met name voor instructies die gebruik maken van ANSI join syntax. Synapse SQL ondersteunt namelijk geen ANSI-joins `FROM` in `DELETE` de clausule van een verklaring. In plaats van de gegevens te verwijderen, selecteert u de gegevens die u wilt bewaren.

Het volgende is een `DELETE` voorbeeld van een geconverteerde instructie:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you want to keep
SELECT p.ProductKey
, p.EnglishProductName
,  p.Color
FROM  dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON p.ProductKey = s.ProductKey;

RENAME OBJECT dbo.DimProduct TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Samenvoeginstructies vervangen

U samenvoegensinstructies, althans gedeeltelijk, vervangen door CTAS te gebruiken. U `INSERT` de `UPDATE` en de in een enkele verklaring te combineren. Verwijderde records moeten worden beperkt `SELECT` tot de instructie om de resultaten weg te laten.

Het volgende voorbeeld `UPSERT`is:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT s.[ProductKey]
, s.[EnglishProductName]
, s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
, p.[EnglishProductName]
, p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
);

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimProduct_upsert]  TO [DimProduct];
```

## <a name="explicitly-state-data-type-and-nullability-of-output"></a>Gegevenstype expliciet vermelden en niet-aansprakelijkheid van uitvoer vermelden

Bij het migreren van code u merken dat u dit type coderingspatroon volgt:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f;
```

Je zou denken dat je deze code moet migreren naar CTAS, en je zou juist zijn. Er is echter een verborgen probleem hier.

De volgende code levert niet hetzelfde resultaat op:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result;
```

Merk op dat de kolom "resultaat" het gegevenstype en de nullability-waarden van de expressie vooruitdraagt. Het naar voren dragen van het gegevenstype kan leiden tot subtiele verschillen in waarden als u niet voorzichtig bent.

Probeer dit voorbeeld:

```sql
SELECT result,result*@d
from result;

SELECT result,result*@d
from ctas_r;
```

De waarde die is opgeslagen voor het resultaat is anders. Omdat de aanhoudende waarde in de resultaatkolom in andere expressies wordt gebruikt, wordt de fout nog groter.

![Schermafbeelding van CTAS-resultaten](./media/sql-data-warehouse-develop-ctas/ctas-results.png)

Dit is belangrijk voor datamigraties. Hoewel de tweede query aantoonbaar nauwkeuriger is, is er een probleem. De gegevens zouden anders zijn dan het bronsysteem, en dat leidt tot integriteitsvragen in de migratie. Dit is een van die zeldzame gevallen waar de "verkeerde" antwoord is eigenlijk de juiste!

De reden waarom we zien een verschil tussen de twee resultaten is te wijten aan impliciete type casting. In het eerste voorbeeld definieert de tabel de kolomdefinitie. Wanneer de rij wordt ingevoegd, vindt een impliciete typeconversie plaats. In het tweede voorbeeld is er geen impliciete typeconversie omdat de expressie het gegevenstype van de kolom definieert.

Merk ook op dat de kolom in het tweede voorbeeld is gedefinieerd als een NULLable kolom, terwijl in het eerste voorbeeld niet. Toen de tabel in het eerste voorbeeld werd gemaakt, werd de tenietbaarheid van de kolom expliciet gedefinieerd. In het tweede voorbeeld werd het aan de expressie overgelaten en zou het standaard resulteren in een NULL-definitie.

Als u deze problemen wilt oplossen, moet u de typeconversie en nullability expliciet instellen in het SELECT-gedeelte van de CTAS-instructie. U deze eigenschappen niet instellen in 'TABEL MAKEN'.
In het volgende voorbeeld wordt uitgelegd hoe u de code herstellen:

```sql
DECLARE @d decimal(7,2) = 85.455
, @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Houd rekening met het volgende:

* U CAST of CONVERT gebruiken.
* Gebruik ISNULL, niet COALESCE, om NULLability te forceren. Zie de volgende notitie.
* ISNULL is de buitenste functie.
* Het tweede deel van de ISNULL is een constante, 0.

> [!NOTE]
> Om de nietigheid correct te kunnen instellen, is het van vitaal belang om ISNULL te gebruiken en niet COALESCE. COALESCE is geen deterministische functie, en dus zal het resultaat van de uitdrukking altijd NULLable zijn. ISNULL is anders. Het is deterministisch. Wanneer het tweede deel van de FUNCTIE ISNULL een constante of een letterlijke functie is, is de resulterende waarde dus NIET NULL.

Het waarborgen van de integriteit van uw berekeningen is ook belangrijk voor het schakelen van tafelpartitie. Stel je voor dat je deze tabel hebt gedefinieerd als een feitentabel:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
, [product]   INT     NOT NULL
, [store]     INT     NOT NULL
, [quantity]  INT     NOT NULL
, [price]     MONEY   NOT NULL
, [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
);
```

Het veld bedrag is echter een berekende expressie. Het maakt geen deel uit van de brongegevens.

Als u uw partitiegegevensset wilt maken, u de volgende code gebruiken:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]
,   [product]
,   [store]
,   [quantity]
,   [price]
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

De query zou perfect draaien. Het probleem komt wanneer u probeert om de partitie schakelaar te doen. De tabeldefinities komen niet overeen. Als u de tabeldefinities wilt laten overeenkomen, wijzigt u de CTAS om een `ISNULL` functie toe te voegen om het kenmerk nullability van de kolom te behouden.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH
( DISTRIBUTION = HASH([product])
, PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
  [date]
, [product]
, [store]
, [quantity]
, [price]
, ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

U zien dat typeconsistentie en het handhaven van nullability-eigenschappen op een CTAS een technische best practice is. Het helpt om de integriteit in uw berekeningen te behouden, en zorgt er ook voor dat partitieschakelen mogelijk is.

CTAS is een van de belangrijkste uitspraken in Synapse SQL. Zorg ervoor dat je het goed begrijpt. Zie de [CTAS documentatie](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="next-steps"></a>Volgende stappen

Zie voor meer ontwikkelingstips het [ontwikkelingsoverzicht.](sql-data-warehouse-overview-develop.md)
