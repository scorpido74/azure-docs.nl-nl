---
title: Prestaties afstemmen met Azure SQL Data Warehouse geordende column store-index | Microsoft Docs
description: Aanbevelingen en overwegingen die u moet weten wanneer u geordende geclusterde column store-index gebruikt om de query prestaties te verbeteren.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 0acdf1496151df57d4097ce5bc71d782dc465873
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554544"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Prestaties afstemmen met een geordende geclusterde column store-index  

Wanneer gebruikers een query uitvoeren op een column Store-tabel in Azure SQL Data Warehouse, controleert de Optimizer de minimum-en maximum waarden die zijn opgeslagen in elk segment.  Segmenten die zich buiten de grenzen van het query-predikaat bevinden, worden niet van de schijf naar het geheugen gelezen.  Een query kan betere prestaties krijgen als het aantal segmenten dat moet worden gelezen en de totale grootte klein zijn.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Besteld versus niet-bestelde geclusterde column store-index 
Standaard maakt een intern onderdeel (Indexing Builder) voor elke Azure Data Warehouse-tabel die is gemaakt zonder een index optie, een niet-bestelde geclusterde column store-index (CCI).  De gegevens in elke kolom worden gecomprimeerd in een afzonderlijk CCI Rijg roep-segment.  Er zijn meta gegevens van de waarden van elk segment, waardoor segmenten die zich buiten de grenzen van het query predicaat bevinden, niet kunnen worden gelezen van de schijf tijdens de uitvoering van de query.  CCI biedt het hoogste niveau van gegevens compressie en vermindert de grootte van segmenten om te lezen zodat query's sneller kunnen worden uitgevoerd. Omdat de opbouw functie voor indexen echter geen gegevens sorteert voordat ze naar segmenten worden gecomprimeerd, kunnen segmenten met overlappende cellenbereiken optreden, waardoor query's meer segmenten van de schijf worden gelezen en langer duren.  

Wanneer u een besteld CCI maakt, sorteert de Azure SQL Data Warehouse engine de bestaande gegevens in het geheugen op basis van de volg orde van de sleutel (s) voordat de index Builder ze comprimeert in index segmenten.  Met gesorteerde gegevens is het segment overlappend, zodat query's een efficiëntere segment eliminatie kunnen hebben, waardoor de prestaties sneller worden, omdat het aantal segmenten dat moet worden gelezen van de schijf kleiner is.  Als alle gegevens in een keer in het geheugen kunnen worden gesorteerd, kan segment overlappend worden vermeden.  Gezien de grote omvang van gegevens in Data Warehouse-tabellen, gebeurt dit niet vaak.  

Als u de segment bereiken voor een kolom wilt controleren, voert u deze opdracht uit met de naam van de tabel en de naam van de kolom:

```sql
SELECT o.name, pnp.index_id, cls.row_count, pnp.data_compression_desc, pnp.pdw_node_id, 
pnp.distribution_id, cls.segment_id, cls.column_id, cls.min_data_id, cls.max_data_id, cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
   JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<Table_Name>' and cols.name = '<Column_Name>' 
ORDER BY o.name, pnp.distribution_id, cls.min_data_id

```

> [!NOTE] 
> In een geordende CCI-tabel worden nieuwe gegevens die voortkomen uit DML of het laden van gegevens, niet automatisch gesorteerd.  Gebruikers kunnen het bestelde CCI opnieuw bouwen om alle gegevens in de tabel te sorteren.  In Azure SQL Data Warehouse is column store-index opnieuw opgebouwd een offline bewerking.  Voor een gepartitioneerde tabel is het opnieuw maken van één partitie per keer voltooid.  Gegevens in de partitie die opnieuw worden opgebouwd, zijn offline en zijn pas beschikbaar als het opnieuw opbouwen is voltooid voor die partitie. 

## <a name="query-performance"></a>Queryprestaties

De prestaties van een query van een bewerkte CCI zijn afhankelijk van de query patronen, de grootte van de gegevens, hoe goed de gegevens worden gesorteerd, de fysieke structuur van segmenten en de DWU en de resource klasse die zijn gekozen voor de uitvoering van de query.  Gebruikers moeten al deze factoren bekijken voordat ze de volg orde van de kolommen kiezen bij het ontwerpen van een bewerkte CCI-tabel.

Query's met al deze patronen worden doorgaans sneller uitgevoerd met het bewerkte CCI.  
1. De query's hebben gelijkheid, ongelijkheid of bereik predikaten
1. De kolommen voor het predicaat en de bestelde CCI-kolommen zijn hetzelfde.  
1. De predikaten worden gebruikt in dezelfde volg orde als het kolom rangtelwoord van de geordende CCI-kolommen.  
 
In dit voor beeld heeft Table T1 een geclusterde column store-index die is geordend in de volg orde van Col_C, Col_B en Col_A.

```sql

CREATE CLUSTERED COLUMNSTORE INDEX MyOrderedCCI ON  T1
ORDER (Col_C, Col_B, Col_A)

```

De prestaties van query 1 kunnen meer profiteren van besteld CCI dan de andere drie query's. 

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

## <a name="data-loading-performance"></a>Prestaties van het laden van gegevens

De prestaties van het laden van gegevens in een geordende CCI-tabel zijn vergelijkbaar met een gepartitioneerde tabel.  Het laden van gegevens in een geordende CCI-tabel kan langer duren dan een niet-bestelde CCI-tabel vanwege de sorteer bewerking voor gegevens, maar query's kunnen later sneller worden uitgevoerd met het bewerkte CCI.  

Hier volgt een voor beeld van de prestatie vergelijking van het laden van gegevens in tabellen met verschillende schema's.

![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/cci-data-loading-performance.png)


Hier volgt een voor beeld van een query prestatie vergelijking tussen CCI en besteld CCI.

![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/occi_query_performance.png)

 
## <a name="reduce-segment-overlapping"></a>Segment overlappend verminderen

Het aantal overlappende segmenten is afhankelijk van de grootte van de gegevens die moeten worden gesorteerd, het beschik bare geheugen en de maximale mate van parallellisme (MAXDOP) tijdens het maken van de geordende CCI. Hieronder vindt u opties voor het verminderen van segment overlap ping bij het maken van besteld CCI.

- Gebruik de resource klasse xlargerc op een hogere DWU om meer geheugen voor het sorteren van gegevens toe te staan voordat de index Builder de gegevens in segmenten comprimeert.  Eenmaal in een index segment kan de fysieke locatie van de gegevens niet worden gewijzigd.  Er wordt geen sortering van gegevens binnen een segment of tussen segmenten.  

- Maak een geordend CCI met MAXDOP = 1.  Elke thread die wordt gebruikt voor het bewerkte CCI maakt, werkt met een subset van gegevens en sorteert deze lokaal.  Er is geen algemene sorteer volgorde voor gegevens die door verschillende threads worden gesorteerd.  Het gebruik van parallelle threads kan de tijd verminderen voor het maken van een geordend CCI, maar er worden meer overlappende segmenten gegenereerd dan met behulp van één thread.  Op dit moment wordt de optie MAXDOP alleen ondersteund voor het maken van een geordende CCI-tabel met behulp van CREATE TABLE als SELECT-opdracht.  Het maken van een geordend CCI via CREATE INDEX of CREATE TABLE opdrachten biedt geen ondersteuning voor de optie MAXDOP. Bijvoorbeeld:

```sql
CREATE TABLE Table1 WITH (DISTRIBUTION = HASH(c1), CLUSTERED COLUMNSTORE INDEX ORDER(c1) )
AS SELECT * FROM ExampleTable
OPTION (MAXDOP 1);
```
- Sorteer de gegevens vooraf op de sorteer sleutel (s) voordat u deze in Azure SQL Data Warehouse tabellen laadt.


Hier volgt een voor beeld van een geordende CCI-tabel distributie met geen segment overlappende de bovenstaande aanbevelingen. De bestelde CCI-tabel wordt met behulp van MAXDOP 1 en xlargerc gemaakt in een DWU1000c-data base via CTAS uit een heap-tabel van 20 GB.  Het CCI bevindt zich op een kolom BIGINT zonder dubbele waarden.  

![Segment_No_Overlapping](media/performance-tuning-ordered-cci/perfect-sorting-example.png)

## <a name="create-ordered-cci-on-large-tables"></a>Besteld CCI maken voor grote tabellen
Het maken van een bestelde CCI is een offline bewerking.  Voor tabellen zonder partities zijn de gegevens niet toegankelijk voor gebruikers totdat het bestelde CCI-proces is voltooid.   Voor gepartitioneerde tabellen, omdat de engine de bestelde CCI-partitie per partitie maakt, hebben gebruikers nog steeds toegang tot de gegevens in partities waar het maken van een CCI niet in behandeling is.   U kunt deze optie gebruiken om de downtime te minimaliseren tijdens het maken van een geordende CCI op grote tabellen: 

1.  Maak partities op de grote doel tabel (met de naam Table_A).
2.  Maak een lege geordende CCI-tabel (met de naam Table_B) met hetzelfde tabel-en partitie schema als tabel A.
3.  Een partitie van tabel A naar tabel B overschakelen.
4.  Voer ALTER INDEX < Ordered_CCI_Index > uit op < Table_B > opnieuw te bouwen partitie = < Partition_ID > op tabel B om de switch in de partitie opnieuw op te bouwen.  
5.  Herhaal stap 3 en 4 voor elke partitie in Table_A.
6.  Wanneer alle partities zijn overgeschakeld van Table_A naar Table_B en opnieuw zijn opgebouwd, verwijdert u Table_A en wijzigt u de naam van Table_B in Table_A. 

>[!NOTE]
>Tijdens de preview-versie van de geordende geclusterde column store-index (CCI) in Azure SQL Data Warehouse kunnen dubbele gegevens worden gegenereerd als de bestelde CCI is gemaakt of opnieuw is samengesteld via een geclusterde column Store-INDEX in een gepartitioneerde tabel. Er is geen gegevens verlies betrokken. Binnenkort is er een oplossing voor dit probleem beschikbaar. Voor een tijdelijke oplossing kunnen gebruikers besteld CCI maken op een gepartitioneerde tabel met behulp van de opdracht CTAS


## <a name="examples"></a>Voorbeelden

**A. om te controleren op geordende kolommen en rang telwoord voor bestellingen:**
```sql
SELECT object_name(c.object_id) table_name, c.name column_name, i.column_store_order_ordinal 
FROM sys.index_columns i 
JOIN sys.columns c ON i.object_id = c.object_id AND c.column_id = i.column_id
WHERE column_store_order_ordinal <>0
```

**B. Als u het Volg nummer van de kolom wilt wijzigen, kunt u kolommen toevoegen aan of verwijderen uit de lijst met bestellingen of van CCI naar besteld CCI:**
```sql
CREATE CLUSTERED COLUMNSTORE INDEX InternetSales ON  InternetSales
ORDER (ProductKey, SalesAmount)
WITH (DROP_EXISTING = ON)
```

## <a name="next-steps"></a>Volgende stappen
Zie [Overzicht van SQL Data Warehouse voor ontwikkelaars](sql-data-warehouse-overview-develop.md) voor meer tips voor ontwikkelaars.
