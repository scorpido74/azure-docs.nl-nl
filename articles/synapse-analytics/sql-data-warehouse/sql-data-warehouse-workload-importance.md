---
title: Workloadurgentie
description: Richt lijnen voor het instellen van de urgentie voor Synapse SQL-pool query's in azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 43ee14784b6049e9b5c1a78e733e72bbc45f915d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80744037"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Prioriteit van Azure Synapse Analytics-workload

In dit artikel wordt uitgelegd hoe de urgentie van het workload invloed kan hebben op de volg orde van de uitvoering van Synapse SQL pool-aanvragen in azure Synapse.

## <a name="importance"></a>Urgentie

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Bedrijfs behoeften kunnen vereisen dat werk belastingen voor gegevens opslag belang rijker zijn dan andere.  Denk na over een scenario waarin essentiële verkoop gegevens worden geladen voordat de fiscale periode wordt gesloten.  Gegevens die worden geladen voor andere bronnen, zoals weer gegevens, hebben geen strikte Sla's. Als u het hoge urgentie niveau instelt voor een aanvraag voor het laden van verkoop gegevens en een lage urgentie voor een aanvraag om weer gegevens te laden, zorgt u ervoor dat de belasting van de omzet eerst toegang krijgt tot bronnen en sneller kan worden uitgevoerd.

## <a name="importance-levels"></a>Urgentie niveaus

Er zijn vijf prioriteits niveaus: laag, below_normal, normaal, above_normal en hoog.  Aanvragen waarvoor geen urgentie is ingesteld, krijgen het standaard niveau normaal. Aanvragen met hetzelfde urgentie niveau hebben hetzelfde plannings gedrag als vandaag.

## <a name="importance-scenarios"></a>Urgentie scenario's

Naast het scenario met de basis prioriteit die hierboven wordt beschreven met verkoop-en weer gegevens, zijn er andere scenario's waarin de werk belasting van de workload aan gegevens verwerking en query behoeften voldoet.

### <a name="locking"></a>Vergrendelen

Toegang tot de vergren delingen voor lees-en schrijf activiteiten is één gebied met natuurlijke conflicten. Voor activiteiten zoals het overschakelen van een [partitie](sql-data-warehouse-tables-partition.md) of het [wijzigen van de naam van het object](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) is verhoogde vergren deling vereist  Zonder urgentie van de werk belasting, Synapse SQL-pool in azure Synapse optimaliseert voor door voer. Optimalisatie voor door Voer betekent dat wanneer in-en in de wachtrij geplaatste aanvragen dezelfde vergrendelings behoeften hebben en resources beschikbaar zijn, de aanvragen in de wachtrij kunnen verzoeken met een hogere vergrendelings behoefte die eerder in de wachtrij voor aanvragen is aangekomen, worden overgeslagen. Zodra de urgentie van de werk belasting wordt toegepast op aanvragen met hogere vergrendelings behoeften. Een aanvraag met een hogere urgentie wordt uitgevoerd vóór de aanvraag met een lagere urgentie.

Kijk een naar het volgende voorbeeld:

- Q1 wordt actief uitgevoerd en er worden gegevens uit SalesFact geselecteerd.
- Q2 is in de wachtrij gezet voordat Q1 is voltooid.  Het is verzonden op 9:00 en probeert de switch van nieuwe gegevens te partitioneren in SalesFact.
- Q3 wordt verzonden op 9:01am en wil gegevens selecteren uit SalesFact.

Als Q2 en Q3 hetzelfde urgentie hebben en Q1 nog steeds wordt uitgevoerd, wordt Q3 uitgevoerd. Q2 blijft wachten op een exclusieve vergren deling op SalesFact.  Als Q2 hoger is dan Q3, moet Q3 wachten tot Q2 is voltooid voordat de uitvoering kan worden gestart.

### <a name="non-uniform-requests"></a>Niet-uniforme aanvragen

Een ander scenario waarbij het belang rijk kan zijn om te voldoen aan query vereisten is wanneer aanvragen met verschillende resource klassen worden verzonden.  Zoals eerder vermeld, is in Synapse SQL-pool in azure Synapse geoptimaliseerd voor door voer. Wanneer aanvragen voor gemengde grootte (zoals smallrc of mediumrc) in de wachtrij worden geplaatst, kiest Synapse SQL-pool de vroegste aankomende aanvraag die binnen de beschik bare resources past. Als de urgentie van de werk belasting wordt toegepast, wordt de aanvraag voor de hoogste urgentie gepland op de volgende regel.
  
Bekijk het volgende voor beeld op DW500c:

- Q1, Q2, Q3 en Q4 voert smallrc-query's uit.
- Q5 wordt verzonden met de resource klasse mediumrc op 9:00.
- Q6 wordt verzonden met smallrc-resource klasse op 9:01am.

Omdat Q5 mediumrc is, zijn er twee gelijktijdigheids sleuven nodig. Q5 moet wachten tot twee van de actieve query's zijn voltooid.  Wanneer een van de query's (Q1-Q4) wordt voltooid, wordt Q6 echter onmiddellijk gepland, omdat de resources bestaan om de query uit te voeren.  Als Q5 een hoger belang heeft dan Q6, wacht Q6 totdat Q5 wordt uitgevoerd voordat het kan worden uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

- Zie de [classificatie werk belasting maken (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)voor meer informatie over het maken van een classificatie.  
- Zie [workload classificatie](sql-data-warehouse-workload-classification.md)voor meer informatie over de classificatie van werk belastingen.  
- Zie de Snelstartgids [werk belasting maken classificatie](quickstart-create-a-workload-classifier-tsql.md) voor het maken van een classificatie van werk belastingen.
- Zie de artikelen met procedures voor het [configureren van de urgentie van werk belastingen](sql-data-warehouse-how-to-configure-workload-importance.md) en het [beheren en bewaken van workload Management](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Zie [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) om query's en de prioriteit weer te geven.
