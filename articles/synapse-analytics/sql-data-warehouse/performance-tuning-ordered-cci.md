---
title: Prestaties afstemmen met geordende en geclusterde columnstore-index
description: Aanbevelingen en overwegingen die u moet weten als u de geordende geclusterde kolomarchiefindex gebruikt om uw queryprestaties te verbeteren.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 2113e5ac3563a22c5f2c6b755230b05fb9a2cb35
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583860"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Prestaties afstemmen met geordende en geclusterde columnstore-index  

Wanneer gebruikers een kolomarchieftabel in synapsengroep opvragen, controleert de optimizer de minimum- en maximumwaarden die in elk segment zijn opgeslagen.  Segmenten die zich buiten de grenzen van het querypredicaat bevinden, worden niet gelezen van schijf naar geheugen.  Een query kan sneller presteren als het aantal segmenten dat moet worden gelezen en de totale grootte ervan klein is.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Geordend e-order versus niet-geordende geclusterde columnstore-index

Standaard maakt een interne component (indexbouwer) voor elke tabel die zonder indexoptie is gemaakt, een niet-geordende geclusterde kolomarchiefindex (CCI).  Gegevens in elke kolom worden gecomprimeerd in een afzonderlijk SEGMENT van de CCI-rijgroep.  Er zijn metagegevens op het waardebereik van elk segment, zodat segmenten die zich buiten de grenzen van het querypredicaat bevinden, niet van de schijf worden gelezen tijdens het uitvoeren van query's.  CCI biedt het hoogste niveau van gegevenscompressie en vermindert de grootte van segmenten om te lezen, zodat query's sneller kunnen worden uitgevoerd. Omdat de indexbouwer gegevens echter niet sorteert voordat ze worden gecomprimeerd in segmenten, kunnen segmenten met overlappende waardebereiken optreden, waardoor query's meer segmenten van de schijf kunnen lezen en langer duren om te voltooien.  

Bij het maken van een geordende CCI sorteert de Synapse SQL-engine de bestaande gegevens in het geheugen door de ordersleutel(s) voordat de indexbouwer ze comprimeert in indexsegmenten.  Met gesorteerde gegevens wordt segmentoverlapping verminderd, waardoor query's een efficiëntere segmenteliminatie en dus snellere prestaties kunnen hebben omdat het aantal segmenten dat van de schijf moet worden gelezen kleiner is.  Als alle gegevens in één keer in het geheugen kunnen worden gesorteerd, kan segmentoverlapping worden vermeden.  Vanwege grote tabellen in datawarehouses gebeurt dit scenario niet vaak.  

Als u de segmentbereiken voor een kolom wilt controleren, voert u deze opdracht uit met de tabelnaam en de kolomnaam:

```sql
SELECT o.name, pnp.index_id, 
cls.row_count, pnp.data_compression_desc, 
pnp.pdw_node_id, pnp.distribution_id, cls.segment_id, 
cls.column_id, 
cls.min_data_id, cls.max_data_id, 
cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table Name>' and cols.name = '<Column Name>'  and TMap.physical_name  not like '%HdTable%'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id 


```

> [!NOTE] 
> In een geordende CCI-tabel worden de nieuwe gegevens die voortvloeien uit dezelfde batch DML of gegevenslaadbewerkingen binnen die batch gesorteerd, er is geen globale sortering voor alle gegevens in de tabel.  Gebruikers kunnen de bestelde CCI opnieuw opbouwen om alle gegevens in de tabel te sorteren.  In Synapse SQL is de kolomarchiefindex HERBOUWEN een offlinebewerking.  Voor een partitietabel wordt de herbouw één partitie tegelijk uitgevoerd.  Gegevens in de partitie die wordt herbouwd, zijn 'offline' en niet beschikbaar totdat de herbouw voor die partitie is voltooid. 

## <a name="query-performance"></a>Queryprestaties

De prestatiewinst van een query van een geordende CCI is afhankelijk van de querypatronen, de grootte van gegevens, hoe goed de gegevens worden gesorteerd, de fysieke structuur van segmenten en de DWU- en resourceklasse die is gekozen voor de queryuitvoering.  Gebruikers moeten al deze factoren bekijken voordat ze de bestelkolommen kiezen bij het ontwerpen van een geordende CCI-tabel.

Query's met al deze patronen lopen meestal sneller met geordende CCI.  
1. De query's hebben gelijkheid, ongelijkheid of bereikpredicaten
1. De predicaatkolommen en de geordende CCI-kolommen zijn hetzelfde.  
1. De predicaatkolommen worden gebruikt in dezelfde volgorde als de kolomordinal van geordende CCI-kolommen.  
 
In dit voorbeeld heeft tabel T1 een geclusterde kolomarchiefindex die is geordend in de volgorde van Col_C, Col_B en Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

De uitvoering van query 1 kan meer profiteren van de bestelde CCI dan de andere drie query's. 

```sql
-- Query #1: 

SELECT * FROM T1 WHERE Col_C = 'c' AND Col_B = 'b' AND Col_A = 'a';

-- Query #2

SELECT * FROM T1 WHERE Col_B = 'b' AND Col_C = 'c' AND Col_A = 'a';

-- Query #3
SELECT * FROM T1 WHERE Col_B = 'b' AND Col_A = 'a';

-- Query #4
SELECT * FROM T1 WHERE Col_A = 'a' AND Col_C = 'c';

```

## <a name="data-loading-performance"></a>Prestaties voor het laden van gegevens

De prestaties van het laden van gegevens in een geordende CCI-tabel zijn vergelijkbaar met een partitietabel.  Het laden van gegevens in een geordende CCI-tabel kan langer duren dan een niet-geordende CCI-tabel vanwege de gegevenssorteerbewerking, maar query's kunnen daarna sneller worden uitgevoerd met geordende CCI.  

Hier is een voorbeeld prestatievergelijking van het laden van gegevens in tabellen met verschillende schema's.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Hier is een voorbeeld query prestaties vergelijking tussen CCI en bestelde CCI.

![Performance_comparison_data_loading](./media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Overlapping van segmenten verminderen

Het aantal overlappende segmenten is afhankelijk van de grootte van de gegevens die moeten worden gesorteerd, het beschikbare geheugen en de maximale mate van parallellisme (MAXDOP) tijdens de bestelde CCI-creatie. Hieronder staan opties om segmentoverlapping te verminderen bij het maken van geordende CCI.

- Gebruik de xlargerc-resourceklasse op een hogere DWU om meer geheugen toe te staan voor het sorteren van gegevens voordat de indexbouwer de gegevens comprimeert in segmenten.  Eenmaal in een indexsegment kan de fysieke locatie van de gegevens niet worden gewijzigd.  Er wordt geen gegevensgesorteerd binnen een segment of in verschillende segmenten.  

- Maak bestelde CCI met MAXDOP = 1.  Elke thread die wordt gebruikt voor geordende CCI-creatie werkt op een subset van gegevens en sorteert deze lokaal.  Er is geen globale sortering voor gegevens gesorteerd op verschillende threads.  Het gebruik van parallelle threads kan de tijd verkorten om een geordende CCI te maken, maar genereert meer overlappende segmenten dan het gebruik van één thread.  Momenteel wordt de maxdop-optie alleen ondersteund bij het maken van een geordende CCI-tabel met de opdracht TABEL MAKEN ALS SELECT.  Het maken van een geordende CCI via CREATE INDEX of CREATE TABLE-opdrachten ondersteunt de maxdop-optie niet. Bijvoorbeeld:

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```

- Sorteer de gegevens vooraf op de sorteertoets(en) voordat u ze in tabellen laadt.

Hier volgt een voorbeeld van een geordende CCI-tabeldistributie met nulsegmentdat overlappende punten volgen. De bestelde CCI-tabel wordt gemaakt in een DWU1000c-database via CTAS vanuit een heaptabel van 20 GB met MAXDOP 1 en xlargerc.  De CCI wordt besteld op een BIGINT kolom zonder duplicaten.  

![Segment_No_Overlapping](./media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Bestelde CCI maken op grote tafels

Het maken van een bestelde CCI is een offline bewerking.  Voor tabellen zonder partities zijn de gegevens pas toegankelijk voor gebruikers als het geordende CCI-creatieproces is voltooid.   Voor partitietabellen, omdat de engine de geordende CCI-partitie per partitie maakt, kunnen gebruikers nog steeds toegang krijgen tot de gegevens in partities waar de bestelde CCI-creatie niet wordt uitgevoerd.   U deze optie gebruiken om de downtime tijdens het maken van een CCI-creatie op grote tafels te minimaliseren: 

1.    Partities maken op de doeltabel (Table_A genoemd).
2.    Maak een lege geordende CCI-tabel (Table_B genoemd) met hetzelfde tabel- en partitieschema als tabel A.
3.    Eén partitie van tabel A naar tabel B schakelen.
4.    Voer>>>> VAN DE>>>>>>>>> in om de ingeschakelde partitie opnieuw op te bouwen, <Ordered_CCI_Index <> op> <Table_B Partition_ID <herbouwen.  
5.    Herhaal stap 3 en 4 voor elke partitie in Table_A.
6.    Zodra alle partities zijn overgeschakeld van Table_A naar Table_B en zijn herbouwd, laat u Table_A vallen en wijzigt u Table_B naar Table_A. 

## <a name="examples"></a>Voorbeelden

**A. Controleer op geordende kolommen en bestelordinal:**

```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Als u de kolommenordinal wilt wijzigen, kolommen toevoegen of verwijderen uit de bestellijst of wilt wijzigen van CCI naar geordende CCI:**

```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer ontwikkelingstips [het ontwikkelingsoverzicht.](sql-data-warehouse-overview-develop.md)
