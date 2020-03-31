---
title: Azure Stream Analytics-uitvoer naar Azure SQL-database
description: Meer informatie over het uitdelen van gegevens naar SQL Azure vanuit Azure Stream Analytics en het bereiken van hogere schrijfdoorvoersnelheden.
author: chetanmsft
ms.author: chetang
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2019
ms.openlocfilehash: f68f973882af28d80b3a27bc4591c5ee932404a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443606"
---
# <a name="azure-stream-analytics-output-to-azure-sql-database"></a>Azure Stream Analytics-uitvoer naar Azure SQL-database

In dit artikel worden tips besproken om betere schrijfdoorvoerprestaties te bereiken wanneer u gegevens laadt in SQL Azure Database met Azure Stream Analytics.

SQL-uitvoer in Azure Stream Analytics ondersteunt het parallel schrijven als een optie. Deze optie maakt [volledig parallelle](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) taaktopologieën mogelijk, waarbij meerdere uitvoerpartities parallel naar de doeltabel schrijven. Het inschakelen van deze optie in Azure Stream Analytics is echter mogelijk niet voldoende om hogere doorvoersnelheden te bereiken, omdat deze aanzienlijk afhankelijk is van uw SQL Azure-databaseconfiguratie en tabelschema. De keuze van indexen, clusteringsleutel, indexvulfactor en compressie hebben een impact op de tijd om tabellen te laden. Zie [SQL-databaseprestatierichtlijnen](../sql-database/sql-database-performance-guidance.md)voor meer informatie over het optimaliseren van uw SQL Azure-database om de prestaties van query's en taken te verbeteren op basis van interne benchmarks. Het bestellen van schrijfbewerkingen is niet gegarandeerd bij het schrijven parallel aan SQL Azure Database.

Hier volgen enkele configuraties binnen elke service die kunnen helpen bij het verbeteren van de algehele doorvoer van uw oplossing.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

- **Partitionering overnemen:** met deze SQL-uitvoerconfiguratieoptie u het partitieschema van uw vorige querystap of -invoer overnemen. Met deze ingeschakeld, schrijven naar een schijf-gebaseerde tabel en met een [volledig parallelle](stream-analytics-parallelization.md#embarrassingly-parallel-jobs) topologie voor uw taak, verwachten betere doorvoerte te zien. Deze partitionering gebeurt al automatisch voor vele andere [uitgangen.](stream-analytics-parallelization.md#partitions-in-sources-and-sinks) Tabelvergrendeling (TABLOCK) is ook uitgeschakeld voor bulkinserts die met deze optie zijn gemaakt.

> [!NOTE] 
> Wanneer er meer dan 8 invoerpartities zijn, is het overnemen van het invoerpartitieschema mogelijk geen geschikte keuze. Deze bovengrens werd waargenomen op een tabel met één identiteitskolom en een geclusterde index. In dit geval u overwegen [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count) 8 in uw query te gebruiken om het aantal uitvoerschrijvers expliciet op te geven. Op basis van uw schema en de keuze van de indexen kunnen uw waarnemingen variëren.

- **Batchgrootte** - SQL-uitvoerconfiguratie stelt u in staat om de maximale batchgrootte op te geven in een Azure Stream Analytics SQL-uitvoer op basis van de aard van uw doeltabel/werkbelasting. Batchgrootte is het maximum aantal records dat wordt verzonden bij elke transactie met bulkinvoeging. In geclusterde kolomarchiefindexen zorgen batchformaten rond [100K](https://docs.microsoft.com/sql/relational-databases/indexes/columnstore-indexes-data-loading-guidance) voor meer parallelisatie, minimale logboekregistratie en vergrendelingsoptimalisaties. In op schijven gebaseerde tabellen kan 10K (standaard) of lager optimaal zijn voor uw oplossing, omdat hogere batchgroottes leiden tot escalatie van het vergrendelen tijdens bulkwisselplaten.

- **Input Message Tuning** - Als u hebt geoptimaliseerd met behulp van inherit partitionering en batchgrootte, helpt het verhogen van het aantal invoergebeurtenissen per bericht per partitie om uw schrijfdoorvoer verder te verhogen. Met het afstemmen van invoerberichten kunnen batchformaten binnen Azure Stream Analytics worden bereikt tot de opgegeven batchgrootte, waardoor de doorvoer wordt verbeterd. Dit kan worden bereikt door [compressie](stream-analytics-define-inputs.md) te gebruiken of de grootte van invoerberichten te vergroten in EventHub of Blob.

## <a name="sql-azure"></a>SQL Azure

- **Partitietabel en indexen** – Het gebruik van een [partitie-SQL-tabel](https://docs.microsoft.com/sql/relational-databases/partitions/partitioned-tables-and-indexes?view=sql-server-2017) en partitieindexen op de tabel met dezelfde kolom als uw partitiesleutel (bijvoorbeeld PartitionId) kan de twisten tussen partities tijdens het schrijven aanzienlijk verminderen. Voor een partitietabel moet u een [partitiefunctie](https://docs.microsoft.com/sql/t-sql/statements/create-partition-function-transact-sql?view=sql-server-2017) en een [partitieschema](https://docs.microsoft.com/sql/t-sql/statements/create-partition-scheme-transact-sql?view=sql-server-2017) maken in de primaire bestandsgroep. Dit zal ook de beschikbaarheid van bestaande gegevens verhogen terwijl nieuwe gegevens worden geladen. Log IO-limiet kan worden geraakt op basis van het aantal partities, die kunnen worden verhoogd door het upgraden van de SKU.

- **Unieke belangrijke schendingen voorkomen:** als u [meerdere waarschuwingsberichten](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) voor belangrijke schendingen ontvangt in het Activiteitenlogboek azure Stream Analytics, moet u ervoor zorgen dat uw taak niet wordt beïnvloed door unieke schendingen van beperkingen die waarschijnlijk zullen plaatsvinden tijdens herstelaanvragen. Dit kan worden voorkomen door de optie [DUP\_\_KEY negeren](stream-analytics-troubleshoot-output.md#key-violation-warning-with-azure-sql-database-output) in te stellen op uw indexen.

## <a name="azure-data-factory-and-in-memory-tables"></a>Azure-gegevensfabriek en geheugentabellen

- **In-Memory Table as temp table** – [In-Memory tables](/sql/relational-databases/in-memory-oltp/in-memory-oltp-in-memory-optimization) allow for very high-speed data loads but data needs to fit in memory. Benchmarks tonen bulkladen van een in-memory tabel naar een schijftabel is ongeveer 10 keer sneller dan direct bulk invoegen met behulp van een enkele schrijver in de schijftabel met een identiteitskolom en een geclusterde index. Als u gebruik wilt maken van deze bulkinsertprestaties, stelt u een [kopieertaak](../data-factory/connector-azure-sql-database.md) in met Azure Data Factory die gegevens uit de tabel in het geheugen kopieert naar de tabel op basis van schijven.

## <a name="avoiding-performance-pitfalls"></a>Het vermijden van valkuilen voor prestaties
Het invoegen van bulkgegevens is veel sneller dan het laden van gegevens met afzonderlijke inserts, omdat de herhaalde overhead van het overbrengen van de gegevens, het ontzeggen van de invoeginstructie, het uitvoeren van de instructie en het uitgeven van een transactierecord wordt vermeden. In plaats daarvan wordt een efficiënter pad gebruikt in de opslagengine om de gegevens te streamen. De installatiekosten van dit pad zijn echter veel hoger dan een enkele invoeginstructie in een op schijven gebaseerde tabel. Het break-even punt ligt meestal rond de 100 rijen, waarboven bulkladen bijna altijd efficiënter is. 

Als de inkomende gebeurtenissensnelheid laag is, kan deze eenvoudig batchformaten maken die lager zijn dan 100 rijen, waardoor bulkinsert inefficiënt is en te veel schijfruimte wordt gebruikt. Als u deze beperking wilt omzeilen, u een van deze acties uitvoeren:
* Maak een IN PLAATS VAN [trigger](/sql/t-sql/statements/create-trigger-transact-sql) om eenvoudige insert voor elke rij te gebruiken.
* Gebruik een in-memory temp tabel zoals beschreven in de vorige sectie.

Een ander dergelijk scenario treedt op bij het schrijven in een niet-geclusterde columnstore-index (NCCI), waarbij kleinere bulkinserts te veel segmenten kunnen maken, waardoor de index kan worden vastgelopen. In dit geval is de aanbeveling om in plaats daarvan een clusterindex voor kolomarchief te gebruiken.

## <a name="summary"></a>Samenvatting

Samengevat moet met de partitieuitvoerfunctie in Azure Stream Analytics voor SQL-uitvoer, de parallelle parallelisatie van uw taak worden uitgelijnd met een partitietabel in SQL Azure, waardoor u aanzienlijke doorvoerverbeteringen moet krijgen. Door gebruik te maken van Azure Data Factory voor het orkestreren van gegevensverplaatsing van een in-memory tabel naar op schijf gebaseerde tabellen kan order of magnitude doorvoerwinsten worden gegenereerd. Indien haalbaar, kan het verbeteren van de berichtdichtheid ook een belangrijke factor zijn bij het verbeteren van de totale doorvoer.
