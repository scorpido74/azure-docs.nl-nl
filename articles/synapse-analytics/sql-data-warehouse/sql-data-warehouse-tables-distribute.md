---
title: Richtlijnen voor het ontwerp van gedistribueerde tabellen
description: Aanbevelingen voor het ontwerpen van hash-distributed en round-robin gedistribueerde tabellen in Synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 04255fb6fdf83e7249fad01c75425943b580393c
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80742862"
---
# <a name="guidance-for-designing-distributed-tables-in-synapse-sql-pool"></a>Richtlijnen voor het ontwerpen van gedistribueerde tabellen in de Synapse SQL-groep

Aanbevelingen voor het ontwerpen van hash-distributed en round-robin gedistribueerde tabellen in Synapse SQL-pools.

In dit artikel wordt ervan uitgegaan dat u bekend bent met concepten voor gegevensdistributie en gegevensverplaatsing in synapse SQL-pool.Zie Azure Synapse Analytics voor meer informatie [over de MPP-architectuur (Massively Parallel Processing).](massively-parallel-processing-mpp-architecture.md)

## <a name="what-is-a-distributed-table"></a>Wat is een gedistribueerde tabel?

Een gedistribueerde tabel wordt weergegeven als één tabel, maar de rijen worden daadwerkelijk opgeslagen in 60 distributies. De rijen worden verdeeld met een hash of round-robin algoritme.  

**Door hash gedistribueerde tabellen** verbeteren de queryprestaties op grote feitentabellen en zijn de focus van dit artikel. **Round-robin tafels** zijn handig voor het verbeteren van de laadsnelheid. Deze ontwerpkeuzes hebben een aanzienlijke impact op het verbeteren van query- en laadprestaties.

Een andere optie voor tabelopslag is het repliceren van een kleine tabel over alle Compute-knooppunten. Zie [Ontwerprichtlijnen voor gerepliceerde tabellen voor](design-guidance-for-replicated-tables.md)meer informatie . Zie Gedistribueerde tabellen in het [overzicht van](sql-data-warehouse-tables-overview.md)tabellen om snel te kiezen uit de drie opties.

Als onderdeel van tabelontwerp, begrijp zoveel mogelijk over uw gegevens en hoe de gegevens worden opgevraagd.Denk bijvoorbeeld aan deze vragen:

- Hoe groot is de tafel?
- Hoe vaak wordt de tabel vernieuwd?
- Heb ik feit- en dimensiontabellen in een Synapse SQL-pool?

### <a name="hash-distributed"></a>Hash gedistribueerd

Een tabel met hash-gedistribueerde tabel verdeelt tabelrijen over de Compute-knooppunten met behulp van een deterministische hashfunctie om elke rij aan één [distributie](massively-parallel-processing-mpp-architecture.md#distributions)toe te wijzen.

![Gedistribueerde tabel](./media/sql-data-warehouse-tables-distribute/hash-distributed-table.png "Gedistribueerde tabel")  

Omdat identieke waarden altijd hash naar dezelfde distributie, het data warehouse heeft ingebouwde kennis van de rij locaties. In Synapse SQL-pool wordt deze kennis gebruikt om gegevensverplaatsing tijdens query's te minimaliseren, wat de queryprestaties verbetert.

Hash-gedistribueerde tabellen werken goed voor grote feitentabellen in een sterschema. Ze kunnen zeer grote aantallen rijen hebben en nog steeds hoge prestaties leveren. Er zijn natuurlijk een aantal ontwerpoverwegingen die u helpen om de prestaties van het gedistribueerde systeem te krijgen is ontworpen om te bieden. Het kiezen van een goede distributiekolom is een dergelijke overweging die wordt beschreven in dit artikel.

Overweeg een hash-gedistribueerde tabel te gebruiken wanneer:

- De tabelgrootte op schijf is meer dan 2 GB.
- De tabel heeft frequente bewerkingen voor het invoegen, bijwerken en verwijderen.

### <a name="round-robin-distributed"></a>Round-robin verdeeld

Een ronde-robin verdeelde lijst verdeelt lijstrijen gelijk over alle distributies. De toewijzing van rijen aan distributies is willekeurig. In tegenstelling tot door hash gedistribueerde tabellen, worden rijen met gelijke waarden niet gegarandeerd toegewezen aan dezelfde verdeling.

Als gevolg hiervan moet het systeem soms een bewerking voor gegevensverplaatsing aanroepen om uw gegevens beter te ordenen voordat het een query kan oplossen.  Deze extra stap kan uw query's vertragen. Als u bijvoorbeeld lid wordt van een round-robin-tabel, moet u de rijen opnieuw schuiven, wat een prestatiehit is.

Overweeg de round-robin-verdeling voor uw tabel te gebruiken in de volgende scenario's:

- Bij het starten als een eenvoudig uitgangspunt, omdat het de standaard
- Als er geen duidelijke verbindingssleutel is
- Als er geen goede kandidaatkolom is voor hash die de tabel distribueert
- Als de tabel geen gemeenschappelijke join-toets deelt met andere tabellen
- Als de join minder belangrijk is dan andere joins in de query
- Wanneer de tabel een tijdelijke faseringstabel is

De tutorial [Load New York taxi gegevens](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) geeft een voorbeeld van het laden van gegevens in een round-robin staging tabel.

## <a name="choosing-a-distribution-column"></a>Een distributiekolom kiezen

Een hash-gedistribueerde tabel heeft een distributiekolom die de hash-sleutel is. Met de volgende code wordt bijvoorbeeld een hash-gedistribueerde tabel met ProductKey als distributiekolom.

```sql
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

Het kiezen van een distributiekolom is een belangrijke ontwerpbeslissing, omdat de waarden in deze kolom bepalen hoe de rijen worden verdeeld. De beste keuze hangt af van verschillende factoren, en meestal gaat het om afwegingen. Als u echter de eerste keer niet de beste kolom kiest, u [TABEL MAKEN ALS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) gebruiken om de tabel opnieuw te maken met een andere distributiekolom.

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>Kies een distributiekolom waarvoor geen updates nodig zijn

U een distributiekolom niet bijwerken, tenzij u de rij verwijdert en een nieuwe rij met de bijgewerkte waarden invoegt. Selecteer daarom een kolom met statische waarden.

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Kies een distributiekolom met gegevens die gelijkmatig worden verdeeld

Voor de beste prestaties moeten alle distributies ongeveer hetzelfde aantal rijen hebben. Wanneer een of meer distributies een onevenredig aantal rijen hebben, voltooien sommige distributies hun gedeelte van een parallelle query vóór andere. Aangezien de query niet kan worden voltooid totdat alle distributies zijn voltooid, is elke query slechts zo snel als de langzaamste distributie.

- Gegevensscheefheid betekent dat de gegevens niet gelijkmatig over de distributies worden verdeeld
- Verwerking scheeftrekken betekent dat sommige distributies langer duren dan andere bij het uitvoeren van parallelle query's. Dit kan gebeuren wanneer de gegevens scheef zijn.
  
Als u de parallelle verwerking wilt in evenwicht brengen, selecteert u een distributiekolom die:

- **Heeft veel unieke waarden.** De kolom kan een aantal dubbele waarden hebben. Alle rijen met dezelfde waarde worden echter aan dezelfde verdeling toegewezen. Aangezien er 60 distributies zijn, moet de kolom ten minste 60 unieke waarden hebben.  Meestal is het aantal unieke waarden veel groter.
- **Heeft geen NULLs, of heeft slechts een paar NULLs.** Als alle waarden in de kolom BIJVOORBEELD NULL zijn, worden alle rijen aan dezelfde verdeling toegewezen. Als gevolg hiervan wordt de queryverwerking scheefgetrokken naar één distributie en profiteert deze niet van parallelle verwerking.
- **Is geen datumkolom**. Alle gegevens voor dezelfde datum worden in dezelfde verdeling verwerkt. Als meerdere gebruikers allemaal filteren op dezelfde datum, dan is slechts 1 van de 60 distributies doen al het verwerkingswerk.

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Kies een distributiekolom die de gegevensverplaatsing minimaliseert

Als u de juiste queryresultatenquery's wilt ophalen, kunnen gegevens van het ene Compute-knooppunt naar het andere worden verplaatst. Gegevensverplaatsing gebeurt vaak wanneer query's joins en aggregaties hebben op gedistribueerde tabellen. Het kiezen van een distributiekolom die helpt bij het minimaliseren van gegevensverplaatsing is een van de belangrijkste strategieën voor het optimaliseren van de prestaties van uw Synapse SQL-pool.

Als u de gegevensverplaatsing wilt minimaliseren, selecteert u een distributiekolom die:

- Wordt gebruikt `JOIN` `GROUP BY`in `DISTINCT` `OVER`, `HAVING` , , en clausules. Wanneer twee grote feitentabellen vaak worden samengevoegd, worden de queryprestaties verbeterd wanneer u beide tabellen distribueert op een van de joinkolommen.  Wanneer een tabel niet wordt gebruikt in joins, u overwegen `GROUP BY` de tabel te verdelen over een kolom die vaak in de component staat.
- Wordt *niet* `WHERE` gebruikt in clausules. Dit kan de query beperken tot niet op alle distributies worden uitgevoerd.
- Is *geen* datumkolom. WAAR clausules filteren vaak op datum.  Wanneer dit gebeurt, kan alle verwerking worden uitgevoerd op slechts een paar distributies.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Wat te doen als geen van de kolommen een goede distributiekolom is

Als geen van uw kolommen voldoende verschillende waarden voor een distributiekolom heeft, u een nieuwe kolom maken als een samenstelling van een of meer waarden. Als u gegevensverplaatsing tijdens query-uitvoering wilt voorkomen, gebruikt u de samengestelde distributiekolom als joinkolom in query's.

Zodra u een tabel met hash-gedistribueerde gegevens ontwerpt, is de volgende stap het laden van gegevens in de tabel.  Zie [Overzicht laden](design-elt-data-loading.md)voor laadrichtlijnen .

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Hoe weet u of uw distributiekolom een goede keuze is

Nadat gegevens in een door hash gedistribueerde tabel zijn geladen, controleert u hoe gelijkmatig de rijen over de 60 distributies zijn verdeeld. De rijen per distributie kunnen tot 10% variëren zonder een merkbare invloed op de prestaties.

### <a name="determine-if-the-table-has-data-skew"></a>Bepalen of de tabel gegevens scheeftrekken heeft

Een snelle manier om te controleren op gegevens scheeftrekken is het gebruik van [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Met de volgende SQL-code wordt het aantal tabelrijen geretourneerd dat in elk van de 60 distributies is opgeslagen. Voor evenwichtige prestaties moeten de rijen in de gedistribueerde tabel gelijkmatig over alle distributies worden verdeeld.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Ga als u bepalen welke tabellen meer dan 10% gegevens scheeftrekken:

1. Maak de weergave dbo.vTableSizes die wordt weergegeven in het [overzichtsartikel Tabellen.](sql-data-warehouse-tables-overview.md#table-size-queries)  
2. Voer de volgende query uit:

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having (max(row_count * 1.000) - min(row_count * 1.000))/max(row_count * 1.000) >= .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Queryplannen controleren voor gegevensverplaatsing

Een goede distributiekolom stelt joins en aggregaties in staat om minimale gegevensverplaatsing te hebben. Dit is van invloed op de manier waarop joins moeten worden geschreven. Als u minimale gegevensverplaatsing wilt krijgen voor een join op twee door hash gedistribueerde tabellen, moet een van de joinkolommen de distributiekolom zijn.  Wanneer twee door hash gedistribueerde tabellen deelnemen aan een distributiekolom van hetzelfde gegevenstype, vereist de join geen gegevensverplaatsing. Joins kunnen extra kolommen gebruiken zonder gegevensverkeer te maken.

Ga als lid niet in op gegevensverplaatsing tijdens een join:

- De tabellen die betrokken zijn bij de join moeten hash worden verspreid over **een** van de kolommen die deelnemen aan de join.
- De gegevenstypen van de joinkolommen moeten overeenkomen tussen beide tabellen.
- De kolommen moeten worden samengevoegd met een gelijkaan operator.
- Het jointype is `CROSS JOIN`mogelijk geen .

Als u wilt zien of query's gegevensbewegingen ervaren, u het queryplan bekijken.  

## <a name="resolve-a-distribution-column-problem"></a>Probleem met een distributiekolom oplossen

Het is niet nodig om alle gevallen van gegevensscheefheid op te lossen. Het distribueren van gegevens is een kwestie van het vinden van de juiste balans tussen het minimaliseren van dataskew en dataverkeer. Het is niet altijd mogelijk om zowel gegevensscheefheid als gegevensverplaatsing te minimaliseren. Soms is het voordeel van het hebben van de minimale gegevensbeweging kan opwegen tegen de impact van het hebben van gegevens scheef.

Als u wilt weten of u gegevensscheeftrekking in een tabel moet oplossen, moet u zoveel mogelijk inzicht krijgen in de gegevensvolumes en query's in uw werkbelasting. U de stappen in het artikel [Querybewaking](sql-data-warehouse-manage-monitor.md) gebruiken om de impact van scheeftrekking op de queryprestaties te controleren. Kijk in het bijzonder hoe lang het duurt voordat grote query's zijn voltooid op afzonderlijke distributies.

Aangezien u de distributiekolom in een bestaande tabel niet wijzigen, u de wijziging van gegevens op een andere manier oplossen door de tabel opnieuw te maken met een andere distributiekolom.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>De tabel opnieuw maken met een nieuwe distributiekolom

In dit voorbeeld wordt [TABEL MAKEN ALS SELECT gebruikt](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om een tabel met een andere hash-distributiekolom opnieuw te maken.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

## <a name="next-steps"></a>Volgende stappen

Als u een gedistribueerde tabel wilt maken, gebruikt u een van de volgende instructies:

- [TABEL MAKEN (Synapsische SQL-groep)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [TABEL MAKEN ALS SELECT (Synapsische SQL-groep)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
