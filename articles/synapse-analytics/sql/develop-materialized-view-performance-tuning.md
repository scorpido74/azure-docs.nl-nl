---
title: Prestaties afstemmen met gerealiseerde weergaven
description: Aanbevelingen en overwegingen die u moet weten terwijl u gematerialiseerde weergaven gebruikt om uw queryprestaties te verbeteren.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.openlocfilehash: 30ca03633b9b0788235439204a3c1926fe6b6a6b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429978"
---
# <a name="performance-tuning-with-materialized-views"></a>Prestaties afstemmen met gerealiseerde weergaven

In synapse SQL-pool bieden de gematerialiseerde weergaven een onderhoudsarme methode voor complexe analytische query's om snelle prestaties te krijgen zonder dat query's worden gewijzigd. Dit artikel bespreekt de algemene richtlijnen voor het gebruik van gematerialiseerde weergaven.

## <a name="materialized-views-vs-standard-views"></a>Gematerialiseerde weergaven versus standaardweergaven

SQL-pool ondersteunt zowel standaard- als gematerialiseerde weergaven.  Beide zijn virtuele tabellen gemaakt met SELECT-expressies en gepresenteerd aan query's als logische tabellen.  Weergaven onthullen de complexiteit van algemene gegevensberekening en voegen een abstractielaag toe aan berekeningswijzigingen, zodat query's niet hoeven te worden herschreven.  

Een standaardweergave berekent de gegevens telkens wanneer de weergave wordt gebruikt.  Er zijn geen gegevens opgeslagen op schijf. Mensen gebruiken standaardweergaven meestal als een hulpmiddel waarmee de logische objecten en query's in een database kunnen worden georganiseerd.  Als u een standaardweergave wilt gebruiken, moet een query er rechtstreeks naar verwijzen.

Een gematerialiseerde weergave berekent, slaat en onderhoudt de gegevens in SQL-groep, net als een tabel.  Herberekening is niet nodig elke keer dat een gematerialiseerde weergave wordt gebruikt.  Daarom kunnen query's die alle of een subset van de gegevens in gematerialiseerde weergaven gebruiken, sneller presteren.  Nog beter, query's kunnen een gematerialiseerde weergave gebruiken zonder er direct naar te verwijzen, dus het is niet nodig om toepassingscode te wijzigen.  

De meeste standaardweergavevereisten zijn nog steeds van toepassing op een gematerialiseerde weergave. Zie [GEMATERIALISEERDE WEERGAVE MAKEN ALS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)voor meer informatie over de gematerialiseerde weergavesyntaxis en andere vereisten .

| Vergelijking                     | Weergave                                         | Gerealiseerde weergave
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Definitie weergeven                 | Opgeslagen in azure-gegevensmagazijn.              | Opgeslagen in azure-gegevensmagazijn.
|Inhoud weergeven                    | Elke keer gegenereerd wanneer de weergave wordt gebruikt.   | Voorverwerkt en opgeslagen in Azure-gegevensmagazijn tijdens het maken van de weergave. Bijgewerkt als gegevens worden toegevoegd aan de onderliggende tabellen.
|Gegevensvernieuwing                    | Altijd bijgewerkt                               | Altijd bijgewerkt
|Snelheid om weergavegegevens uit complexe query's op te halen     | Langzaam                                         | Snel  
|Extra opslag                   | Nee                                           | Ja
|Syntaxis                          | WEERGAVE MAKEN                                  | GEMATERIALISEERDE WEERGAVE MAKEN ALS SELECTEREN

## <a name="benefits-of-using-materialized-views"></a>Voordelen van het gebruik van gematerialiseerde weergaven

Een goed ontworpen gematerialiseerde weergave biedt de volgende voordelen:

- Minder uitvoeringstijd voor complexe query's met JOIN's en geaggregeerde functies. Hoe complexer de query, hoe groter het potentieel voor uitvoeringstijdbesparing. Het meeste voordeel wordt behaald wanneer de berekeningskosten van een query hoog zijn en de resulterende gegevensset klein is.  

- De optimizer in SQL-groep kan geïmplementeerde gematerialiseerde weergaven automatisch gebruiken om de uitvoeringsplannen voor query's te verbeteren.  Dit proces is transparant voor gebruikers die snellere queryprestaties leveren en vereist geen query's om direct te verwijzen naar de gematerialiseerde weergaven.

- Vereist weinig onderhoud op de uitzichten.  Een gematerialiseerde weergave slaat gegevens op twee plaatsen op, een geclusterde kolomarchiefindex voor de oorspronkelijke gegevens op de weergavetijd en een deltaarchief voor de incrementele gegevenswijzigingen.  Alle gegevenswijzigingen uit de basistabellen worden op een synchrone manier aan het deltaarchief toegevoegd.  Een achtergrondproces (tuple mover) verplaatst de gegevens periodiek van het deltaarchief naar de kolomarchiefindex van de weergave.  Met dit ontwerp kunnen gematerialiseerde weergaven dezelfde gegevens retourneren als het rechtstreeks opvragen van de basistabellen.
- De gegevens in een gematerialiseerde weergave kunnen anders worden verdeeld dan de basistabellen.  
- Gegevens in gematerialiseerde weergaven krijgen dezelfde voordelen voor hoge beschikbaarheid en tolerantie als gegevens in reguliere tabellen.  

In vergelijking met andere leveranciers van gegevensmagazijnen bieden de gematerialiseerde weergaven die zijn geïmplementeerd in SQL-pool ook de volgende extra voordelen:

- Automatische en synchrone gegevens vernieuwen met gegevenswijzigingen in basistabellen. Er is geen actie van de gebruiker vereist.
- Brede geaggregeerde functieondersteuning. Zie [GEMATERIALISEERDE WEERGAVE MAKEN ALS SELECT (Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).
- De ondersteuning voor queryspecifieke gematerialiseerde weergaveaanbeveling.  Zie [EXPLAIN (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="common-scenarios"></a>Algemene scenario's  

Gematerialiseerde weergaven worden meestal gebruikt in volgende scenario's:

**Noodzaak om de prestaties van complexe analytische query's te verbeteren tegen grote gegevens in omvang**

Complexe analytische query's gebruiken doorgaans meer aggregatiefuncties en tabeljoins, waardoor meer rekenzware bewerkingen worden veroorzaakt, zoals shuffles en voegt ze samen in queryuitvoering.  Daarom duurt het langer voordat deze query's zijn voltooid, vooral op grote tabellen.  

Gebruikers kunnen gematerialiseerde weergaven maken voor de gegevens die worden geretourneerd uit de algemene berekeningen van query's, zodat er geen herberekening vereist is wanneer deze gegevens nodig zijn door query's, waardoor lagere rekenkosten en een snellere queryrespons mogelijk zijn.

**Snellere prestaties nodig zonder of minimale querywijzigingen**

Schema- en querywijzigingen in gegevensmagazijnen worden doorgaans tot een minimum beperkt om reguliere ETL-bewerkingen en rapportages te ondersteunen.  Mensen kunnen gematerialiseerde weergaven gebruiken voor het afstemmen van queryprestaties als de kosten die de weergaven maken, kunnen worden gecompenseerd door de winst in queryprestaties.

In vergelijking met andere tuningopties, zoals schaal- en statistiekbeheer, is het een veel minder impactvolle productiewijziging om een gematerialiseerde weergave te creëren en te behouden en de potentiële prestatiewinst is ook hoger.

- Het maken of onderhouden van gematerialiseerde weergaven heeft geen invloed op de query's die worden uitgevoerd ten opzichte van de basistabellen.
- De queryoptimizer kan de geïmplementeerde gematerialiseerde weergaven automatisch gebruiken zonder directe weergaveverwijzing in een query. Deze mogelijkheid vermindert de behoefte aan queryverandering in prestatieafstemming.

**Verschillende datadistributiestrategie nodig voor snellere queryprestaties**

Azure data warehouse is een gedistribueerd en massaal parallel processing (MPP) systeem.   Gegevens in een gegevensmagazijntabel worden verdeeld over 60 knooppunten met behulp van een van de drie [distributiestrategieën](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (hash, round_robin of gerepliceerd).  

De gegevensverdeling wordt opgegeven op de tijd voor het maken van de tabel en blijft ongewijzigd totdat de tabel is verwijderd. Gematerialiseerde weergave wordt een virtuele tabel op schijf ondersteunt hash en round_robin gegevensdistributies.  Gebruikers kunnen een gegevensdistributie kiezen die verschilt van de basistabellen, maar die optimaal is voor de prestaties van query's die vaak de weergaven gebruiken.  

## <a name="design-guidance"></a>Ontwerprichtlijnen

Hier vindt u de algemene richtlijnen voor het gebruik van gematerialiseerde weergaven om de queryprestaties te verbeteren:

**Ontwerp voor uw werklast**

Voordat u gematerialiseerde weergaven gaat maken, is het belangrijk om een diepgaand inzicht te hebben in uw werkbelasting in termen van querypatronen, belang, frequentie en de grootte van de resulterende gegevens.  

Gebruikers kunnen EXPLAIN WITH_RECOMMENDATIONS <SQL_statement> uitvoeren voor de gematerialiseerde weergaven die door de queryoptimizer worden aanbevolen.  Aangezien deze aanbevelingen queryspecifiek zijn, is een gematerialiseerde weergave die ten goede komt aan één query mogelijk niet optimaal voor andere query's in dezelfde werkbelasting.  

Evalueer deze aanbevelingen met uw werkbelastingbehoeften in gedachten.  De ideale gematerialiseerde weergaven zijn die welke de prestaties van de werkbelasting ten goede komen.  

**Wees je bewust van de afweging tussen snellere query's en de kosten**

Voor elke gematerialiseerde weergave zijn er kosten voor gegevensopslag en kosten voor het onderhouden van de weergave.  Naarmate gegevens in de basistabellen veranderen, neemt de grootte van de gematerialiseerde weergave toe en verandert ook de fysieke structuur.  

Om degradatie van queryprestaties te voorkomen, wordt elke gematerialiseerde weergave afzonderlijk onderhouden door de datawarehouse-engine, inclusief het verplaatsen van rijen van deltastore naar de indexsegmenten van het kolomarchief en het consolideren van gegevenswijzigingen.  

De onderhoudswerklast stijgt hoger wanneer het aantal gematerialiseerde weergaven en wijzigingen in de basistabel toeneemt.   Gebruikers moeten controleren of de kosten van alle gematerialiseerde weergaven kunnen worden gecompenseerd door de prestatiewinst van query's.  

U deze query uitvoeren voor de lijst met gematerialiseerde weergave in een database:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

Opties om het aantal gematerialiseerde weergaven te verminderen:

- Identificeer veelvoorkomende gegevenssets die vaak worden gebruikt door de complexe query's in uw werkbelasting.  Maak gematerialiseerde weergaven om deze gegevenssets op te slaan, zodat de optimizer ze kan gebruiken als bouwstenen bij het maken van uitvoeringsplannen.  

- Laat de gematerialiseerde weergaven vallen die weinig of niet meer nodig zijn.  Een uitgeschakelde gematerialiseerde weergave wordt niet onderhouden, maar brengt nog steeds opslagkosten met zich mee.  

- Combineer gematerialiseerde weergaven die zijn gemaakt op dezelfde of vergelijkbare basistabellen, zelfs als hun gegevens elkaar niet overlappen.  Het combineren van gematerialiseerde weergaven kan resulteren in een grotere weergave in grootte dan de som van de afzonderlijke weergaven, maar de onderhoudskosten van de weergave moeten worden verminderd.  Bijvoorbeeld:

```sql
-- Query 1 would benefit from having a materialized view created with this SELECT statement
SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement
SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form
SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**Niet alle prestatieafstemming vereist querywijziging**

De optimizer van het gegevensmagazijn kan geïmplementeerde gematerialiseerde weergaven automatisch gebruiken om de queryprestaties te verbeteren.  Deze ondersteuning wordt transparant toegepast op query's die niet verwijzen naar de weergaven en op query's die niet-ondersteunde aggregaten gebruiken bij het maken van gematerialiseerde weergaven.  Er is geen querywijziging nodig. U het geschatte uitvoeringsplan van een query controleren om te bevestigen of een gematerialiseerde weergave wordt gebruikt.  

**Gematerialiseerde weergaven controleren**

Een gematerialiseerde weergave wordt opgeslagen in het gegevensmagazijn, net als een tabel met geclusterde kolomarchiefindex (CCI).  Het lezen van gegevens uit een gematerialiseerde weergave omvat het scannen van de index en het toepassen van wijzigingen vanuit het deltaarchief.  Wanneer het aantal rijen in het deltaarchief te hoog is, kan het oplossen van een query vanuit een gematerialiseerde weergave langer duren dan het rechtstreeks opvragen van de basistabellen.  Om degradatie van queryprestaties te voorkomen, is het een goede gewoonte om [DBCC-PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) uit te voeren om de overhead_ratio van de weergave (total_rows / base_view_row) te controleren.  Als de overhead_ratio te hoog is, u overwegen de gematerialiseerde weergave opnieuw te herstellen, zodat alle rijen in het deltaarchief worden verplaatst naar de kolomarchiefindex.  

**Gematerialiseerde weergave en resultaatset caching**

Deze twee functies worden geïntroduceerd in SQL-pool rond dezelfde tijd voor queryprestaties afstemmen. Resultaatset caching wordt gebruikt voor het bereiken van hoge gelijktijdigheid en snelle responstijden van repetitieve query's tegen statische gegevens.  

Als u het resultaat in de cache wilt gebruiken, moet de vorm van de query voor cacheaanvragen overeenkomen met de query die de cache heeft geproduceerd.  Bovendien moet het in de cache opgeslagen resultaat van toepassing zijn op de hele query.  Met gematerialiseerde weergaven kunnen gegevenswijzigingen in de basistabellen worden aangebracht.  Gegevens in gematerialiseerde weergaven kunnen worden toegepast op een stuk van een query.  Met deze ondersteuning kunnen dezelfde gematerialiseerde weergaven worden gebruikt door verschillende query's die een bepaalde berekening delen voor snellere prestaties.

## <a name="example"></a>Voorbeeld

In dit voorbeeld wordt een TPCDS-achtige query gebruikt waarin klanten worden gevonden die meer geld uitgeven via de catalogus dan in winkels. Het identificeert ook de voorkeursklanten en hun land van herkomst.   De query omvat het selecteren van TOP 100-records uit de UNIE van drie sub-SELECT-instructies met som() en GROEP DOOR.

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

Controleer het geschatte uitvoeringsplan van de query.  Er zijn 18 shuffles en 17 joins operaties, die meer tijd in beslag nemen om uit te voeren. Laten we nu één gematerialiseerde weergave maken voor elk van de drie sub-SELECT-instructies.

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

Controleer het uitvoeringsplan van de oorspronkelijke query opnieuw.  Nu verandert het aantal joins van 17 naar 5 en is er geen shuffle meer.  Klik op het pictogram Filterbewerking in het plan. De uitvoerlijst geeft aan dat de gegevens worden gelezen uit de gematerialiseerde weergaven in plaats van basistabellen.  

 ![Plan_Output_List_with_Materialized_Views](./media/develop-materialized-view-performance-tuning/output-list.png)

Bij gematerialiseerde weergaven wordt dezelfde query veel sneller uitgevoerd zonder dat de code wordt gewijzigd.  

## <a name="next-steps"></a>Volgende stappen

Zie [Synapse SQL-ontwikkelingsoverzicht](develop-overview.md)voor meer ontwikkelingstips.
 