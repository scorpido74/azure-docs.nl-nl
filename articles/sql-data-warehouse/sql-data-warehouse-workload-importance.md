---
title: Workloadurgentie
description: Richt lijnen voor het instellen van de urgentie van query's in Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 28d239d47b46a5aafdf65c72ef826a0efb79f52b
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974630"
---
# <a name="azure-sql-data-warehouse-workload-importance"></a>Urgentie van Azure SQL Data Warehouse werk belasting

In dit artikel wordt uitgelegd hoe de urgentie van het werk belasting de volg orde van de uitvoering van SQL Data Warehouse aanvragen kan beïnvloeden.

## <a name="importance"></a>Urgentie

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Bedrijfs behoeften kunnen vereisen dat werk belastingen voor gegevens opslag belang rijker zijn dan andere.  Denk na over een scenario waarin essentiële verkoop gegevens worden geladen voordat de fiscale periode wordt gesloten.  Gegevens die worden geladen voor andere bronnen, zoals weer gegevens, hebben geen strikte Sla's. Het instellen van een hoge urgentie voor een aanvraag voor het laden van verkoop gegevens en een lage urgentie voor een aanvraag om te laden of gegevens ervoor zorgen dat de belasting van de omzet eerst toegang krijgt tot bronnen en sneller kan worden uitgevoerd.

## <a name="importance-levels"></a>Urgentie niveaus

Er zijn vijf prioriteits niveaus: laag, below_normal, normaal, above_normal en hoog.  Aanvragen waarvoor geen urgentie is ingesteld, krijgen het standaard niveau normaal. Aanvragen met hetzelfde urgentie niveau hebben hetzelfde plannings gedrag als vandaag.

## <a name="importance-scenarios"></a>Urgentie scenario's

Naast het scenario met de basis prioriteit die hierboven wordt beschreven met verkoop-en weer gegevens, zijn er andere scenario's waarin de werk belasting van de workload aan gegevens verwerking en query behoeften voldoet.

### <a name="locking"></a>Vergrendelen

Toegang tot de vergren delingen voor lees-en schrijf activiteiten is één gebied met natuurlijke conflicten. Voor activiteiten zoals het overschakelen van een [partitie](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) of het [wijzigen van de naam van het object](/sql/t-sql/statements/rename-transact-sql) is verhoogde vergren deling vereist  Zonder urgentie van de werk belasting, SQL Data Warehouse optimaliseert voor door voer.  Optimalisatie voor door Voer betekent dat wanneer in-en in de wachtrij geplaatste aanvragen dezelfde vergrendelings behoeften hebben en resources beschikbaar zijn, de aanvragen in de wachtrij kunnen verzoeken met een hogere vergrendelings behoefte die eerder in de wachtrij voor aanvragen is aangekomen, worden overgeslagen.  Zodra de urgentie van de werk belasting wordt toegepast op aanvragen met hogere vergrendelings behoeften, worden aanvragen met een hogere urgentie uitgevoerd vóór de aanvraag met een lagere urgentie.

Kijk een naar het volgende voorbeeld:

Q1 wordt actief uitgevoerd en er worden gegevens uit SalesFact geselecteerd.
Q2 is in de wachtrij gezet voordat Q1 is voltooid.  Het is verzonden op 9:00 en probeert de switch van nieuwe gegevens te partitioneren in SalesFact.
Q3 wordt verzonden op 9:01am en wil gegevens selecteren uit SalesFact.

Als Q2 en Q3 hetzelfde urgentie hebben en Q1 nog steeds wordt uitgevoerd, wordt Q3 uitgevoerd. Q2 blijft wachten op een exclusieve vergren deling op SalesFact.  Als Q2 hoger is dan Q3, moet Q3 wachten tot Q2 is voltooid voordat de uitvoering kan worden gestart.

### <a name="non-uniform-requests"></a>Niet-uniforme aanvragen

Een ander scenario waarbij het belang rijk kan zijn om te voldoen aan query vereisten is wanneer aanvragen met verschillende resource klassen worden verzonden.  Zoals eerder vermeld, onder hetzelfde belang, SQL Data Warehouse optimaliseert voor door voer. Wanneer aanvragen voor gemengde grootte (zoals smallrc of mediumrc) in de wachtrij worden geplaatst, kiest SQL Data Warehouse de eerste aankomende aanvraag die binnen de beschik bare resources past. Als de urgentie van de werk belasting wordt toegepast, wordt de aanvraag voor de hoogste urgentie gepland op de volgende regel.
  
Bekijk het volgende voor beeld op DW500c:

Q1, Q2, Q3 en Q4 voert smallrc-query's uit.
Q5 wordt verzonden met de resource klasse mediumrc op 9:00.
Q6 wordt verzonden met smallrc-resource klasse op 9:01am.

Omdat Q5 mediumrc is, zijn er twee gelijktijdigheids sleuven nodig. Q5 moet wachten tot twee van de actieve query's zijn voltooid.  Wanneer een van de query's (Q1-Q4) wordt voltooid, wordt Q6 echter onmiddellijk gepland, omdat de resources bestaan om de query uit te voeren.  Als Q5 een hoger belang heeft dan Q6, wacht Q6 totdat Q5 wordt uitgevoerd voordat het kan worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

- Zie de [classificatie werk belasting maken (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql)voor meer informatie over het maken van een classificatie.  
- Zie [workload classificatie](sql-data-warehouse-workload-classification.md)voor meer informatie over de classificatie van SQL Data Warehouse werk belasting.  
- Zie de Snelstartgids [werk belasting maken classificatie](quickstart-create-a-workload-classifier-tsql.md) voor het maken van een classificatie van werk belastingen.
- Zie de artikelen met procedures voor het [configureren van de urgentie van werk belastingen](sql-data-warehouse-how-to-configure-workload-importance.md) en het [beheren en bewaken van workload Management](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Zie [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) om query's en de prioriteit weer te geven.
