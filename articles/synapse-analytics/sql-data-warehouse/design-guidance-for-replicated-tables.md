---
title: Ontwerprichtlijnen voor gerepliceerde tabellen
description: Aanbevelingen voor het ontwerpen van gerepliceerde tabellen in SQL Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: bbf36f8f3aed9d8208c6182daa7237dc57ade67b
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349138"
---
# <a name="design-guidance-for-using-replicated-tables-in-sql-analytics"></a>Ontwerprichtlijnen voor het gebruik van gerepliceerde tabellen in SQL Analytics
In dit artikel worden aanbevelingen gedaan voor het ontwerpen van gerepliceerde tabellen in uw SQL Analytics-schema. Gebruik deze aanbevelingen om de queryprestaties te verbeteren door de gegevensverplaatsing en de complexiteit van query's te verminderen.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u bekend bent met concepten voor gegevensdistributie en gegevensverplaatsing in SQL Analytics.Zie voor meer informatie het [architectuurartikel.](massively-parallel-processing-mpp-architecture.md) 

Als onderdeel van tabelontwerp, begrijp zoveel mogelijk over uw gegevens en hoe de gegevens worden opgevraagd.Denk bijvoorbeeld aan deze vragen:

- Hoe groot is de tafel?   
- Hoe vaak wordt de tabel vernieuwd?   
- Heb ik feiten- en dimensioneringstabellen in een SQL Analytics-database?   

## <a name="what-is-a-replicated-table"></a>Wat is een gerepliceerde tabel?
Een gerepliceerde tabel heeft een volledige kopie van de tabel die toegankelijk is op elk Compute-knooppunt. Als een tabel wordt gerepliceerd, hoeven de gegevens niet meer te worden overgedragen tussen rekenknooppunten voordat er sprake is van een samenvoeging of aggregatie. Aangezien de tabel meerdere kopieën bevat, werken gerepliceerde tabellen het beste wanneer de tabelgrootte minder dan 2 GB is gecomprimeerd.  2 GB is geen harde limiet.  Als de gegevens statisch zijn en niet worden gewijzigd, u grotere tabellen repliceren.

In het volgende diagram ziet u een gerepliceerde tabel die toegankelijk is op elk Compute-knooppunt. In SQL Analytics wordt de gerepliceerde tabel volledig gekopieerd naar een distributiedatabase op elk Compute-knooppunt. 

![Gerepliceerde tabel](./media/design-guidance-for-replicated-tables/replicated-table.png "Gerepliceerde tabel")  

Gerepliceerde tabellen werken goed voor dimensietabellen in een sterschema. Dimensietabellen worden doorgaans samengevoegd met feitentabellen die anders zijn verdeeld dan de dimensietabel.  Afmetingen zijn meestal van een grootte die het mogelijk maakt om meerdere exemplaren op te slaan en te onderhouden. Dimensies slaan beschrijvende gegevens op die langzaam veranderen, zoals de naam en het adres van de klant en productgegevens. De langzaam veranderende aard van de gegevens leidt tot minder onderhoud van de gerepliceerde tabel. 

Overweeg een gerepliceerde tabel te gebruiken wanneer:

- De tabelgrootte op schijf is minder dan 2 GB, ongeacht het aantal rijen. Als u de grootte van een tabel wilt vinden, u de opdracht [DBCC-PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) gebruiken: `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- De tabel wordt gebruikt in joins die anders gegevensverplaatsing vereisen. Wanneer u tabellen aansluit die niet op dezelfde kolom worden gedistribueerd, zoals een tabel die door hash is gedistribueerd naar een round-robin-tabel, is gegevensverplaatsing vereist om de query te voltooien.  Als een van de tabellen klein is, u een gerepliceerde tabel overwegen. We raden u aan in de meeste gevallen gerepliceerde tabellen te gebruiken in plaats van round-robin-tabellen. Als u gegevensverplaatsingsbewerkingen in queryplannen wilt weergeven, gebruikt u [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).  De BroadcastMoveOperation is de typische bewerking voor gegevensverplaatsing die kan worden geëlimineerd met behulp van een gerepliceerde tabel.  
 
Gerepliceerde tabellen leveren mogelijk niet de beste queryprestaties op wanneer:

- De tabel heeft frequente bewerkingen voor het invoegen, bijwerken en verwijderen.De DML-bewerkingen (Data Manipulation Language) vereisen een reconstructie van de gerepliceerde tabel.Vaak opnieuw opbouwen kan leiden tot tragere prestaties.
- De SQL Analytics-database wordt regelmatig geschaald. Als u een SQL Analytics-database opschaalt, wordt het aantal Compute-knooppunten gewijzigd, waardoor de gerepliceerde tabel opnieuw wordt opgebouwd.
- De tabel heeft een groot aantal kolommen, maar gegevensbewerkingen hebben meestal slechts toegang tot een klein aantal kolommen. In dit scenario is het mogelijk dat de tabel effectiever is om de tabel te distribueren en vervolgens een index te maken voor de veelgebruikte kolommen. Wanneer een query gegevensverplaatsing vereist, verplaatst SQL Analytics alleen gegevens voor de gevraagde kolommen. 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Gerepliceerde tabellen gebruiken met eenvoudige querypredicaten
Voordat u ervoor kiest een tabel te distribueren of te repliceren, moet u nadenken over de typen query's die u tegen de tabel wilt uitvoeren. Waar mogelijk,

- Gebruik gerepliceerde tabellen voor query's met eenvoudige querypredicaten, zoals gelijkheid of ongelijkheid.
- Gebruik gedistribueerde tabellen voor query's met complexe querypredicaten, zoals VIND-ik-leuk of niet-leuk.

CPU-intensieve query's presteren het beste wanneer het werk wordt verdeeld over alle Compute-knooppunten. Query's die bijvoorbeeld berekeningen uitvoeren in elke rij van een tabel, presteren beter op gedistribueerde tabellen dan gerepliceerde tabellen. Aangezien een gerepliceerde tabel volledig is opgeslagen op elk Compute-knooppunt, wordt een CPU-intensieve query tegen een gerepliceerde tabel uitgevoerd tegen de hele tabel op elk Compute-knooppunt. De extra berekening kan de queryprestaties vertragen.

Deze query heeft bijvoorbeeld een complex predicaat.  Het loopt sneller wanneer de gegevens zich in een gedistribueerde tabel bevindt in plaats van een gerepliceerde tabel. In dit voorbeeld kunnen de gegevens worden verspreid.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'
       
```       
           
## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Bestaande round-robin-tabellen converteren naar gerepliceerde tabellen
Als u al round-robin-tabellen hebt, raden we u aan deze te converteren naar gerepliceerde tabellen als deze voldoen aan de criteria die in dit artikel worden beschreven. Gerepliceerde tabellen verbeteren de prestaties ten opzichte van round-robin-tabellen, omdat ze de noodzaak voor gegevensverplaatsing elimineren.  Een round-robin tafel vereist altijd data beweging voor joins. 

In dit voorbeeld wordt [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) gebruikt om de tabel DimSalesTerritory te wijzigen in een gerepliceerde tabel. Dit voorbeeld werkt ongeacht of DimSalesTerritory hash-distributed of round-robin is.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
``` 
    
### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Queryprestaties voorbeeld voor round-robin versus gerepliceerd 
    
Voor een gerepliceerde tabel is geen gegevensverplaatsing vereist voor joins, omdat de hele tabel al aanwezig is op elk Compute-knooppunt. Als de dimensietabellen round-robin zijn gedistribueerd, kopieert een join de dimensietabel volledig naar elk Compute-knooppunt. Als u de gegevens wilt verplaatsen, bevat het queryplan een bewerking met de naam BroadcastMoveOperation. Dit type gegevensverplaatsingsbewerking vertraagt de queryprestaties en wordt geëlimineerd met behulp van gerepliceerde tabellen. Als u queryplanstappen wilt weergeven, gebruikt u de systeemcatalogusweergave [sys.dm_pdw_request_steps.](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql)  

In het volgende van query's tegen `FactInternetSales` het AdventureWorks-schema wordt de tabel bijvoorbeeld verspreid over hash. De `DimDate` `DimSalesTerritory` en de tabellen zijn kleinere dimensie tabellen. Met deze query wordt de totale omzet in Noord-Amerika voor het boekjaar 2004 geretourneerd:

```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```
We hebben `DimDate` opnieuw `DimSalesTerritory` gemaakt en als round-robin tafels. Als gevolg hiervan toonde de query het volgende queryplan, dat meerdere broadcast move-bewerkingen heeft: 
 
![Round-robin queryplan](./media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

We hebben `DimDate` opnieuw `DimSalesTerritory` gemaakt en als gerepliceerde tabellen, en liep de query opnieuw. Het resulterende queryplan is veel korter en heeft geen broadcastmoves.

![Gerepliceerd queryplan](./media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Prestatieoverwegingen voor het wijzigen van gerepliceerde tabellen
SQL Analytics implementeert een gerepliceerde tabel door een hoofdversie van de tabel te behouden. Het kopieert de hoofdversie naar één distributiedatabase op elk Compute-knooppunt. Wanneer er een wijziging is, werkt SQL Analytics eerst de hoofdtabel bij. Vervolgens worden de tabellen op elk Compute-knooppunt opnieuw opgebouwd. Een reconstructie van een gerepliceerde tabel omvat het kopiëren van de tabel naar elk Compute-knooppunt en vervolgens het bouwen van de indexen.  Een gerepliceerde tabel op een DW400 bevat bijvoorbeeld 5 kopieën van de gegevens.  Een stramienkopie en een volledige kopie op elk Compute-knooppunt.  Alle gegevens worden opgeslagen in distributiedatabases. SQL Analytics gebruikt dit model om snellere gegevenswijzigingsinstructies en flexibele schalingsbewerkingen te ondersteunen. 

Reconstructies zijn nodig na:
- Gegevens worden geladen of gewijzigd
- De SQL Analytics-instantie wordt geschaald naar een ander niveau
- Tabeldefinitie wordt bijgewerkt

Reconstructies zijn niet nodig na:
- Bewerking onderbreken
- Hervattingsbewerking

De herbouw gebeurt niet onmiddellijk nadat gegevens zijn gewijzigd. In plaats daarvan wordt de reconstructie geactiveerd wanneer een query voor het eerst in de tabel wordt geselecteerd.  De query die de reconstructie heeft geactiveerd, wordt onmiddellijk gelezen vanuit de hoofdversie van de tabel, terwijl de gegevens asynchroon worden gekopieerd naar elk Compute-knooppunt. Totdat de gegevenskopie is voltooid, blijven volgende query's de hoofdversie van de tabel gebruiken.  Als er een activiteit optreedt tegen de gerepliceerde tabel die een andere herbouw dwingt, wordt de gegevenskopie ongeldig verklaard en wordt de volgende selecte instructie geactiveerd voor gegevens die opnieuw worden gekopieerd. 

### <a name="use-indexes-conservatively"></a>Indexen conservatief gebruiken
Standaardindexeringspraktijken zijn van toepassing op gerepliceerde tabellen. SQL Analytics herbouwt elke gerepliceerde tabelindex als onderdeel van de wederopbouw. Gebruik alleen indexen wanneer de prestatiewinst opweegt tegen de kosten van de wederopbouw van de indexen.  
 
### <a name="batch-data-loads"></a>Batchgegevens ladingen
Probeer bij het laden van gegevens in gerepliceerde tabellen de reconstructies te minimaliseren door ladingen samen te batcheren. Voer alle batchte ladingen uit voordat u bepaalde instructies uitvoert.

Dit belastingspatroon laadt bijvoorbeeld gegevens uit vier bronnen en roept vier reconstructies op. 

        Load from source 1.
- Selecteer instructietriggers opnieuw opbouwen 1.
        Belasting vanaf bron 2.
- Selecteer instructietriggers opnieuw opbouwen 2.
- Belasting vanaf bron 3.
- Selecteer instructietriggers opnieuw opbouwen 3.
- Belasting vanaf bron 4.
- Selecteer instructietriggers opnieuw opbouwen 4.

Dit belastingspatroon laadt bijvoorbeeld gegevens uit vier bronnen, maar roept slechts één herbouwen aan.

- Belasting vanaf bron 1.
- Belasting vanaf bron 2.
- Belasting vanaf bron 3.
- Belasting vanaf bron 4.
- Selecteer instructietriggers opnieuw opbouwen.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Een gerepliceerde tabel opnieuw opbouwen na batchbelasting
Als u consistente uitvoeringstijden voor query's wilt garanderen, u overwegen de verzameling van de gerepliceerde tabellen te forceren na een batchbelasting. Anders gebruikt de eerste query nog steeds gegevensverplaatsing om de query te voltooien. 

Deze query gebruikt de [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) DMV om de gerepliceerde tabellen weer te geven die zijn gewijzigd, maar niet opnieuw zijn opgebouwd.

```sql 
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id 
  JOIN sys.pdw_table_distribution_properties p 
    ON p.object_id = t.object_id 
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```
 
Als u een reconstructie wilt activeren, voert u de volgende instructie uit op elke tabel in de vorige uitvoer. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>Volgende stappen 
Als u een gerepliceerde tabel wilt maken, gebruikt u een van de volgende instructies:

- [TABEL MAKEN (SQL Analytics)](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [TABEL MAKEN ALS SELECTEREN (SQL Analytics)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

Zie [gedistribueerde tabellen voor](sql-data-warehouse-tables-distribute.md)een overzicht van gedistribueerde tabellen.
