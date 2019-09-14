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
ms.openlocfilehash: 41fbebcf4b85f6e48babba30c2d05fedb3e7a5c7
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/13/2019
ms.locfileid: "70985296"
---
# <a name="performance-tuning-with-ordered-clustered-columnstore-index"></a>Prestaties afstemmen met een geordende geclusterde column store-index  

Wanneer gebruikers een query uitvoeren op een column Store-tabel in Azure SQL Data Warehouse, controleert de Optimizer de minimum-en maximum waarden die zijn opgeslagen in elk segment.  Segmenten die zich buiten de grenzen van het query-predikaat bevinden, worden niet van de schijf naar het geheugen gelezen.  Een query kan betere prestaties krijgen als het aantal segmenten dat moet worden gelezen en de totale grootte klein zijn.   

## <a name="ordered-vs-non-ordered-clustered-columnstore-index"></a>Besteld versus niet-bestelde geclusterde column store-index 
Standaard maakt een intern onderdeel (Indexing Builder) voor elke Azure Data Warehouse-tabel die is gemaakt zonder een index optie, een niet-bestelde geclusterde column store-index (CCI).  De gegevens in elke kolom worden gecomprimeerd in een afzonderlijk CCI Rijg roep-segment.  Er zijn meta gegevens van de waarden van elk segment, waardoor segmenten die zich buiten de grenzen van het query predicaat bevinden, niet kunnen worden gelezen van de schijf tijdens de uitvoering van de query.  CCI biedt het hoogste niveau van gegevens compressie en vermindert de grootte van segmenten om te lezen zodat query's sneller kunnen worden uitgevoerd. Omdat de opbouw functie voor indexen echter geen gegevens sorteert voordat deze in segmenten worden gecomprimeerd, kunnen segmenten met overlappende cellenbereiken optreden, waardoor query's meer segmenten van de schijf worden gelezen en langer duren.  

Wanneer u een besteld CCI maakt, worden de gegevens in het geheugen door de Azure SQL Data Warehouse Engine gesorteerd op basis van de volg orde van de sleutel (s) voordat de index Builder deze comprimeert in index segmenten.  Met gesorteerde gegevens is het segment overlappend, zodat query's een efficiëntere segment eliminatie kunnen hebben, waardoor de prestaties sneller worden, omdat het aantal segmenten dat moet worden gelezen van de schijf kleiner is.  Als alle gegevens in een keer in het geheugen kunnen worden gesorteerd, kan segment overlappend worden vermeden.  Gezien de grote omvang van gegevens in Data Warehouse-tabellen, gebeurt dit niet vaak.  

Als u de segment bereiken voor een kolom wilt controleren, voert u deze opdracht uit met de naam van de tabel en de naam van de kolom:

```sql
SELECT o.name, pnp.index_id, pnp.rows, pnp.data_compression_desc, pnp.pdw_node_id, 
pnp.distribution_id, cls.segment_id, cls.column_id, cls.min_data_id, cls.max_data_id, cls.max_data_id-cls.min_data_id as difference
FROM sys.pdw_nodes_partitions AS pnp
   JOIN sys.pdw_nodes_tables AS Ntables ON pnp.object_id = NTables.object_id AND pnp.pdw_node_id = NTables.pdw_node_id
   JOIN sys.pdw_table_mappings AS Tmap  ON NTables.name = TMap.physical_name AND substring(TMap.physical_name,40, 10) = pnp.distribution_id
   JOIN sys.objects AS o ON TMap.object_id = o.object_id
   JOIN sys.pdw_nodes_column_store_segments AS cls ON pnp.partition_id = cls.partition_id AND pnp.distribution_id  = cls.distribution_id
   JOIN sys.columns as cols ON o.object_id = cols.object_id AND cls.column_id = cols.column_id
WHERE o.name = '<table_name>' and c.name = '<column_name>'
ORDER BY o.name, pnp.distribution_id, cls.min_data_id
```

## <a name="data-loading-performance"></a>Prestaties van het laden van gegevens

De prestaties van het laden van gegevens in een geordende CCI-tabel zijn vergelijkbaar met het laden van gegevens in een gepartitioneerde tabel.  
Het laden van gegevens in een geordende CCI-tabel kan meer tijd in beslag nemen dan het laden van gegevens in een niet-bestelde CCI-tabel vanwege het sorteren van gegevens.  

Hier volgt een voor beeld van de prestatie vergelijking van het laden van gegevens in tabellen met verschillende schema's.
![Performance_comparison_data_loading](media/performance-tuning-ordered-cci/cci-data-loading-performance.png)
 
## <a name="reduce-segment-overlapping"></a>Segment overlappend verminderen
Hieronder vindt u opties voor het verder verminderen van segment overlap ping bij het maken van besteld CCI voor een nieuwe tabel via CTAS of voor een bestaande tabel met gegevens:

- Gebruik een grotere resource klasse om te voor komen dat er meer gegevens in het geheugen tegelijk worden gesorteerd voordat de opbouw functie voor indexen ze comprimeert in segmenten.  Eenmaal in een index segment kan de fysieke locatie van de gegevens niet worden gewijzigd.  Er wordt geen sortering van gegevens binnen een segment of tussen segmenten.  

- Gebruik een lagere mate van parallelle uitvoering (bijvoorbeeld DOP = 1).  Elke thread die wordt gebruikt voor het bewerkte CCI maakt, werkt met een subset van gegevens en sorteert deze lokaal.  Er is geen algemene sorteer volgorde voor gegevens die door verschillende threads worden gesorteerd.  Het gebruik van parallelle threads kan de tijd verminderen voor het maken van een geordend CCI, maar er worden meer overlappende segmenten gegenereerd dan met behulp van één thread. 
- Sorteer de gegevens vooraf op de sorteer sleutel (s) voordat u deze in Azure SQL Data Warehouse tabellen laadt.

## <a name="create-ordered-cci-on-large-tables"></a>Besteld CCI maken voor grote tabellen
Het maken van een bestelde CCI is een offline bewerking.  Voor tabellen zonder partities zijn de gegevens niet toegankelijk voor gebruikers totdat het bestelde CCI-proces is voltooid.   Voor gepartitioneerde tabellen, omdat de engine de bestelde CCI-partitie per partitie maakt, hebben gebruikers nog steeds toegang tot de gegevens in partities waar het maken van een CCI niet in behandeling is.   U kunt deze optie gebruiken om de downtime te minimaliseren tijdens het maken van een geordende CCI op grote tabellen: 

1.  Partities maken voor de grote doel tabel (tabel A genoemd).
2.  Maak een lege bestelde CCI-tabel (met de naam tabel B) met hetzelfde tabel-en partitie schema als tabel A.
3.  Een partitie van tabel A naar tabel B overschakelen.
4.  Voer ALTER INDEX < Ordered_CCI_Index > opnieuw samen te stellen in tabel B om de switch-partitie opnieuw op te bouwen.  
5.  Herhaal stap 3 en 4 voor elke partitie in tabel A.
6.  Als alle partities zijn overgeschakeld van tabel A naar tabel B en opnieuw zijn opgebouwd, verwijdert u tabel A en wijzigt u tabel B in tabel A. 

## <a name="examples"></a>Voorbeelden

**ÉÉN. Controleren op geordende kolommen en rang telwoord voor bestellingen:**
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
