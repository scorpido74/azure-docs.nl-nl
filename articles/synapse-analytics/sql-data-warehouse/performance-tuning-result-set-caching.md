---
title: Prestaties afstemmen door resultatensets op te slaan in de cache
description: Overzicht van caching-functies voor Synapse SQL-pool in Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: eadbe13269ce1259b4560af117f5b15b3b294151
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730588"
---
# <a name="performance-tuning-with-result-set-caching"></a>Prestaties afstemmen door resultatensets op te slaan in de cache

Wanneer de cache van de resultatenset is ingeschakeld, cachet SQL Analytics automatisch queryresultaten in de gebruikersdatabase voor herhaald gebruik.  Hierdoor kunnen volgende query-uitvoeringen rechtstreeks resultaten uit de volgehouden cache krijgen, zodat herberekening niet nodig is.   Het incachezetten van resultaten verbetert de queryprestaties en vermindert het gebruik van rekenbronnen.  Bovendien gebruiken query's met in de cache ingestelde resultaten geen gelijktijdigheidssleuven en tellen ze dus niet mee voor bestaande gelijktijdigheidslimieten. Voor beveiliging hebben gebruikers alleen toegang tot de resultaten in de cache als ze dezelfde machtigingen voor gegevenstoegang hebben als de gebruikers die de resultaten in de cache maken.  

## <a name="key-commands"></a>Toetsopdrachten

[Aan/UIT-resultaatset instellen voor een gebruikersdatabase inschakelen](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Aan/UIT-resultaatset instellen voor een gebruikersdatabase inschakelen](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[In/UIT-resultaatset instellen voor een sessie](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[De grootte van de in de cache opgeslagen resultatenset controleren](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[De cache opschonen](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Wat is niet in de cache opgeslagen  

Zodra de resulterenset cache is ingeschakeld voor een database, worden de resultaten in de cache opgeslagen voor alle query's totdat de cache vol is, met uitzondering van deze query's:

- Query's met niet-deterministische functies zoals DateTime.Now()
- Query's met door de gebruiker gedefinieerde functies
- Query's met tabellen met beveiliging op rijniveau of beveiliging op kolomniveau ingeschakeld
- Query's die gegevens retourneren met een rijgrootte groter dan 64 KB
- Query's die grote gegevens retourneren (>10 GB) 

> [!IMPORTANT]
> De bewerkingen voor het maken van resultatensetcache en het ophalen van gegevens uit de cache vinden plaats op het controleknooppunt van een Synapse SQL-poolinstantie.
> Wanneer het incachezetten van het resultaat is ingeschakeld, kunnen query's die grote resultaatset retourneren (bijvoorbeeld >1 GB) leiden tot hoge beperking op het besturingselementknooppunt en het algemene queryantwoord op de instantie vertragen.  Deze query's worden vaak gebruikt tijdens gegevensverkenning of ETL-bewerkingen. Om te voorkomen dat het controleknooppunt wordt benadrukt en prestatieproblemen ontstaan, moeten gebruikers de cache van de resultatenset in de database uitschakelen voordat deze typen query's worden uitgevoerd.  

Voer deze query uit voor de tijd die wordt genomen door de cachingbewerkingen van de resultatenset voor een query:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

Hier is een voorbeelduitvoer voor een query die is uitgevoerd met de caching van de resultaatset uitgeschakeld.

![Query-stappen-met-rsc-uitgeschakeld](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Hier is een voorbeelduitvoer voor een query die is uitgevoerd met in cache van de resultaatset ingeschakeld.

![Query-stappen-met-rsc-ingeschakeld](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Wanneer in de cache opgeslagen resultaten worden gebruikt

Resultatenset in cache wordt opnieuw gebruikt voor een query als aan alle volgende vereisten is voldaan:

- De gebruiker die de query uitvoert, heeft toegang tot alle tabellen waarnaar in de query wordt verwezen.
- Er is een exacte overeenkomst tussen de nieuwe query en de vorige query die de cache van de resultaatset heeft gegenereerd.
- Er zijn geen gegevens- of schemawijzigingen in de tabellen waaruit de resultaatset in de cache is gegenereerd.

Voer deze opdracht uit om te controleren of een query is uitgevoerd met een hit or miss-hit of misser van de resultaatcache. De result_cache_hit kolom retourneert 1 voor cachehit, 0 voor cachemissen en negatieve waarden om redenen waarom resultaatsetcache niet is gebruikt. Kijk [op sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) voor meer informatie.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Resultaten in de cache beheren

De maximale grootte van de cache van de resultaatset is 1 TB per database.  De in de cache opgeslagen resultaten worden automatisch ongeldig gemaakt wanneer de onderliggende querygegevens worden gewijzigd.  

De cacheuitzetting wordt beheerd door SQL Analytics die automatisch volgens dit schema volgt:

- Elke 48 uur als de resultaatset niet is gebruikt of ongeldig is gemaakt.
- Wanneer de cache met resultaatset de maximale grootte nadert.

Gebruikers kunnen handmatig de volledige cache van de resultaatset legen met behulp van een van de volgende opties:

- De cachefunctie voor de resultatenset voor de database uitschakelen
- DBCC DROPRESULTSETCACHE uitvoeren terwijl deze is verbonden met de database

Als u een database pauzeert, wordt de set resultaten in de cache niet leeg.  

## <a name="next-steps"></a>Volgende stappen

Zie voor meer ontwikkelingstips [het ontwikkelingsoverzicht.](sql-data-warehouse-overview-develop.md)
