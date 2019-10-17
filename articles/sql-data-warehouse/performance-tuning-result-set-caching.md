---
title: Prestaties afstemmen met caches voor resultaat sets | Microsoft Docs
description: Overzicht van de functies
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.openlocfilehash: 2fa3da028e6e897921786889579c6de9a048c3e8
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392642"
---
# <a name="performance-tuning-with-result-set-caching"></a>Prestaties afstemmen met de cache van resultaten sets  
Wanneer het in cache plaatsen van de resultatenset is ingeschakeld, slaat Azure SQL Data Warehouse query resultaten in de gebruikers database automatisch op voor herhaaldelijk gebruik.  Op deze manier kunnen volgende query's worden uitgevoerd om resultaten rechtstreeks uit de persistente cache te halen, zodat herberekening niet nodig is.   Caching van resultaten sets verbetert de query prestaties en vermindert het gebruik van reken resources.  Daarnaast gebruiken query's die in de cache opgeslagen resultaten zijn ingesteld geen gelijktijdigheids sleuven en worden dus niet met bestaande gelijktijdigheids limieten geteld. Voor de beveiliging hebben gebruikers alleen toegang tot de resultaten in de cache als ze dezelfde machtigingen voor gegevens toegang hebben als de gebruikers die de in de cache opgeslagen resultaten maken.  

## <a name="key-commands"></a>Belangrijkste opdrachten
[IN-en uitschakelen van de cache voor de resultatenset voor een gebruikers database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[IN-en uitschakelen van de cache voor de resultatenset voor een sessie](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[De grootte van de resultatenset in de cache controleren](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[De cache opschonen](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Wat wordt niet in cache opgeslagen  

Zodra de caching van de resultatenset voor een Data Base is ingeschakeld, worden de resultaten in de cache opgeslagen voor alle query's totdat de cache vol is, met uitzonde ring van de volgende query's:
- Query's die gebruikmaken van niet-deterministische functies, zoals DateTime. Now ()
- Query's met door de gebruiker gedefinieerde functies
- Query's die gegevens retour neren met een Rijgrootte groter dan 64 KB

Query's met grote resultaten sets (bijvoorbeeld > 1.000.000 rijen) kunnen de prestaties vertragen tijdens de eerste uitvoering wanneer de resultaten cache wordt gemaakt.

## <a name="when-cached-results-are-used"></a>Wanneer de resultaten in de cache worden gebruikt

De resultatenset in de cache wordt opnieuw gebruikt voor een query als aan alle volgende vereisten wordt voldaan:
- De gebruiker die de query uitvoert, heeft toegang tot alle tabellen waarnaar wordt verwezen in de query.
- Er is een exacte overeenkomst tussen de nieuwe query en de vorige query die de cache met resultaten sets heeft gegenereerd.
- Er zijn geen gegevens of schema wijzigingen in de tabellen waarin de resultatenset in de cache is gegenereerd.

Voer deze opdracht uit om te controleren of een query is uitgevoerd met een treffer voor de resultaten cache of ontbreekt. Als er een cache-treffer is, retourneert de result_cache_hit 1.

```sql
SELECT request_id, command, result_cache_hit FROM sys.pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>In cache opgeslagen resultaten beheren 

De maximale grootte van de cache voor de resultatenset is 1 TB per data base.  De resultaten in de cache worden automatisch ongeldig gemaakt wanneer de onderliggende query gegevens veranderen.  

De verwijdering van de cache wordt beheerd door Azure SQL Data Warehouse automatisch de volgende planning te volgen: 
- Elke 48 uur als de resultatenset niet is gebruikt of ongeldig is gemaakt. 
- Wanneer de cache voor de resultatenset de maximum grootte nadert.

Gebruikers kunnen de volledige cache voor de resultatenset hand matig legen door een van de volgende opties te gebruiken: 
- De cache functie voor het instellen van resultaten voor de data base uitschakelen 
- DBCC DROPRESULTSETCACHE uitvoeren terwijl er verbinding is gemaakt met de data base

Wanneer een Data Base wordt onderbroken, wordt de resultatenset in de cache niet leeg gelaten.  

## <a name="next-steps"></a>Volgende stappen
Zie [Overzicht van SQL Data Warehouse voor ontwikkelaars](sql-data-warehouse-overview-develop.md) voor meer tips voor ontwikkelaars. 
