---
title: Prestatiediagnostiek in Hyperscale
description: In dit artikel wordt beschreven hoe u problemen met de prestaties van Hyperscale in Azure SQL Database oplossen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: 26bd6ddb9d8255b8e2510133fc4b6aa645f89f68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75615066"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>Diagnose van SQL Hyperscale-prestaties voor het oplossen van problemen

Om prestatieproblemen in een Hyperscale-database op te lossen, is [algemene prestatieafstemmingsmethoden](sql-database-monitor-tune-overview.md) op het compute-knooppunt van de Azure SQL-database het uitgangspunt van een prestatieonderzoek. Echter, gezien de [gedistribueerde architectuur](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) van Hyperscale, extra diagnostiek zijn toegevoegd om te helpen. In dit artikel worden hyperscale-specifieke diagnostische gegevens beschreven.

## <a name="log-rate-throttling-waits"></a>Logboektarief beperking wacht

Elk Azure SQL Database-serviceniveau heeft limieten voor het genereren van logboeken die worden afgedwongen via [beheer van logsnelheden.](sql-database-resource-limits-database-server.md#transaction-log-rate-governance) In Hyperscale is de limiet voor het genereren van logboeken momenteel ingesteld op 100 MB/sec, ongeacht het serviceniveau. Er zijn echter momenten waarop de loggeneratiesnelheid op de primaire compute-replica moet worden beperkt om herstelbaarheid SLA's te behouden. Deze beperking vindt plaats wanneer een [paginaserver of een andere compute-replica](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) aanzienlijk achter het toepassen van nieuwe logboekrecords van de logboekservice zit.

De volgende wachttypen (in [sys.dm_os_wait_stats)](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/)beschrijven de redenen waarom de logsnelheid kan worden beperkt op de primaire compute-replica:

|Wachttype    |Beschrijving                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | Treedt op wanneer een primaire rekenknooppuntgeneratiesnelheid van een Hyperscale-database wordt beperkt als gevolg van vertraagd logboekverbruik op de paginaserver(s).         |
|RBIO_RG_DESTAGE        | Treedt op wanneer een Hyperscale database compute node log generatie snelheid wordt beperkt als gevolg van vertraagde log verbruik door de lange termijn log opslag.         |
|RBIO_RG_REPLICA        | Treedt op wanneer een Hyperscale database compute node log generatie tarief wordt beperkt als gevolg van vertraagde log verbruik door de leesbare secundaire replica (s).         |
|RBIO_RG_LOCALDESTAGE   | Treedt op wanneer een hyperscale database compute node log generatie snelheid wordt beperkt als gevolg van vertraagde log verbruik door de log service.         |

## <a name="page-server-reads"></a>Paginaserver leest

De compute replica's bewaren geen volledige kopie van de database lokaal. De gegevens die lokaal zijn voor de compute-replica worden opgeslagen in de buffergroep (in het geheugen) en in de lokale RBPEX-cache (Resilient Buffer Pool Extension) die een gedeeltelijke (niet-dekkende) cache van gegevenspagina's is. Deze lokale RBPEX-cache is evenredig aan de rekengrootte en is drie keer het geheugen van de compute-laag. RBPEX is vergelijkbaar met de Buffer Pool in die zin dat het de meest gebruikte gegevens heeft. Elke pagina server, aan de andere kant, heeft een dekking RBPEX cache voor het gedeelte van de database onderhoudt.
 
Wanneer een read wordt uitgegeven op een compute replica, als de gegevens niet bestaan in de Buffer Pool of lokale RBPEX-cache, wordt een getPage(pageId, LSN) functieaanroep uitgegeven en wordt de pagina opgehaald van de bijbehorende paginaserver. Reads van pagina servers zijn remote reads en zijn dus langzamer dan leest van de lokale RBPEX. Bij het oplossen van IO-gerelateerde prestatieproblemen, moeten we kunnen zien hoeveel IOs zijn gedaan via relatief tragere externe paginaserver leest.

Verschillende DMVs en uitgebreide gebeurtenissen hebben kolommen en velden die het aantal externe reads van een paginaserver opgeven, die kunnen worden vergeleken met het totaal. Query store legt ook externe leest als onderdeel van de query run time stats.

- Kolommen om paginaserverreads te rapporteren, zijn beschikbaar in uitvoerings-DMVs en catalogusweergaven, zoals:
    - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
    - [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql/)
- Paginaserverreads worden toegevoegd aan de volgende uitgebreide gebeurtenissen:
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - query-store_execution_runtime_info
- ActualPageServerReads/ActualPageServerReadAheads worden toegevoegd aan het queryplan XML voor werkelijke abonnementen. Bijvoorbeeld:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> Als u deze kenmerken wilt weergeven in het venster Eigenschappen van queryplan, is SSMS 18.3 of hoger vereist.

## <a name="virtual-file-stats-and-io-accounting"></a>Virtuele bestandsstatistieken en IO-boekhouding

In Azure SQL Database is de [dmf sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) de primaire manier om SQL Server IO te controleren. IO-kenmerken in Hyperscale zijn verschillend vanwege de [gedistribueerde architectuur.](sql-database-service-tier-hyperscale.md#distributed-functions-architecture) In deze sectie richten we ons op IO (leest en schrijft) naar gegevensbestanden zoals te zien in deze DMF. In Hyperscale komt elk gegevensbestand dat in deze DMF zichtbaar is overeen met een externe paginaserver. De RBPEX cache hier vermeld is een lokale SSD-gebaseerde cache, dat is een niet-dekkende cache op de compute replica.

### <a name="local-rbpex-cache-usage"></a>Lokaal RBPEX-cachegebruik

Lokale RBPEX-cache bestaat op de compute-replica, op lokale SSD-opslag. Io tegen deze cache is dus sneller dan IO tegen externe paginaservers. Momenteel heeft [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) in een Hyperscale-database een speciale rij die de IO rapporteert tegen de lokale RBPEX-cache op de compute-replica. Deze rij heeft de waarde `database_id` `file_id` van 0 voor beide en kolommen. De onderstaande query retourneert bijvoorbeeld RBPEX-gebruiksstatistieken sinds het opstarten van de database.

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

Een verhouding van leest gedaan op RBPEX tot geaggregeerde leest gedaan op alle andere gegevensbestanden biedt RBPEX cache hit ratio.

### <a name="data-reads"></a>Gegevens worden gelezen

- Wanneer de SQL Server-engine leest op een compute replica, kunnen ze worden bediend door de lokale RBPEX-cache of door externe paginaservers, of door een combinatie van de twee als ze meerdere pagina's lezen.
- Wanneer de compute replica een aantal pagina's uit een specifiek bestand leest, bijvoorbeeld file_id 1, als deze gegevens zich uitsluitend op de lokale RBPEX-cache bevinden, wordt alle IO voor deze gelezen verwerkt tegen file_id 0 (RBPEX). Als een deel van die gegevens zich in de lokale RBPEX-cache bevindt en een deel zich op een externe paginaserver bevindt, wordt IO naar file_id 0 verwerkt voor het deel dat wordt bediend door RBPEX en wordt het deel dat vanaf de externe paginaserver wordt bediend, verwerkt naar file_id 1. 
- Wanneer een compute replica een pagina op een bepaalde [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) van een paginaserver opvraagt, als de paginaserver de gevraagde LSN niet heeft ingehaald, wacht de lezen op de compute-replica totdat de paginaserver wordt ingehaald voordat de pagina wordt teruggezet naar de compute replica. Voor elke lezing van een paginaserver op de compute replica, ziet u de PAGEIOLATCH_ * wachttijd type als het wacht op dat IO. In Hyperscale bevat deze wachttijd zowel de tijd om de gevraagde pagina op de paginaserver in te halen naar de vereiste LSN als de tijd die nodig is om de pagina van de paginaserver naar de compute-replica over te zetten.
- Grote leest zoals read-ahead worden vaak gedaan met behulp van ["Scatter-Gather" Leest](/sql/relational-databases/reading-pages/). Hierdoor kunnen er tot 4 MB pagina's tegelijk worden gelezen, beschouwd als één keer lezen in de SQL Server-engine. Echter, wanneer de gegevens worden gelezen is in RBPEX, deze leest worden geboekt als meerdere individuele 8 KB leest, omdat de buffer pool en RBPEX altijd gebruik maken van 8 KB pagina's. Als gevolg hiervan kan het aantal lees-IOs gezien tegen RBPEX groter zijn dan het werkelijke aantal IOs uitgevoerd door de motor.

### <a name="data-writes"></a>Gegevensschrijft

- De primaire compute-replica schrijft niet rechtstreeks naar paginaservers. In plaats daarvan worden logboekrecords van de logboekservice opnieuw afgespeeld op bijbehorende paginaservers. 
- Schrijft dat gebeuren op de compute replica zijn voornamelijk schrijft aan de lokale RBPEX (file_id 0). Voor schrijft op logische bestanden die groter zijn dan 8 KB, met andere woorden die gedaan met behulp van [Gather-write](/sql/relational-databases/writing-pages/), elke schrijfbewerking wordt vertaald in meerdere 8 KB individuele schrijft aan RBPEX, omdat de buffer pool en RBPEX altijd gebruik maken van 8 KB pagina's. Als gevolg hiervan kan het aantal schrijf-IOs gezien tegen RBPEX groter zijn dan het werkelijke aantal IOs uitgevoerd door de motor.
- Niet-RBPEX-bestanden, of andere gegevensbestanden dan file_id 0 die overeenkomen met paginaservers, tonen ook schrijfbewerkingen. In de servicelaag Hyperscale worden deze schrijfbewerkingen gesimuleerd, omdat de compute-replica's nooit rechtstreeks naar paginaservers schrijven. Schrijf IOPS en doorvoer worden verwerkt als ze zich voordoen op de compute replica, maar latentie voor andere gegevensbestanden dan file_id 0 weerspiegelt niet de werkelijke latentie van paginaserverschrijft.

### <a name="log-writes"></a>Logboekschrijft

- Op de primaire compute, een log schrijven is goed voor in file_id 2 van sys.dm_io_virtual_file_stats. Een logboekschrijven op primaire gegevensbediel is een schrijven naar de logboeklandingzone.
- Logboekrecords worden niet gehard op de secundaire replica op een commit. In Hyperscale wordt logboek door de logservice asynchroon toegepast op de secundaire replica's. Omdat logboekschrijft niet echt voorkomen op secundaire replica's, is elke boekhouding van Log IOs op de secundaire replica's alleen voor trackingdoeleinden.

## <a name="data-io-in-resource-utilization-statistics"></a>Gegevens IO in resourcegebruiksstatistieken

In een niet-Hyperscale-database worden gecombineerde lees- en schrijf-IOPS-gegevens ten opzichte van de IOPS-limiet voor `avg_data_io_percent` [resourcegovernancegegevens](/azure/sql-database/sql-database-resource-limits-database-server#resource-governance) gerapporteerd in [sys.dm_db_resource_stats-](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) en [sys.resource_stats-weergaven](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) in de kolom. Dezelfde waarde wordt gerapporteerd in de portal als _Data IO Percentage_. 

In een Hyperscale-database rapporteert deze kolom over het iOPS-gebruik van gegevens ten opzichte `tempdb`van de limiet voor lokale opslag op compute-replica, met name IO tegen RBPEX en . Een waarde van 100% in deze kolom geeft aan dat resourcegovernance de lokale opslag-IOPS beperkt. Als dit gecorreleerd is met een prestatieprobleem, stemt u de werkbelasting af om minder IO te genereren of verhoogt u de doelstelling voor databaseservice om de limiet voor _de maximale gegevensiOPS-limiet_ [limit](sql-database-vcore-resource-limits-single-databases.md)voor resourcegovernance te verhogen. Voor resource governance van RBPEX leest en schrijft, telt het systeem individuele 8 KB IOs, in plaats van grotere IOs die kunnen worden uitgegeven door de SQL Server-engine. 

Gegevens IO tegen externe paginaservers worden niet gerapporteerd in resourcegebruiksweergaven of in de portal, maar wordt gerapporteerd in de [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF, zoals eerder vermeld.


## <a name="additional-resources"></a>Aanvullende bronnen

- Voor vCore-resourcelimieten voor een hyperscale-database zie [Hyperscale-servicelaag vCore-limieten](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)
- Zie [Queryprestaties in Azure SQL Database](sql-database-performance-guidance.md) voor Azure SQL Database performance tuning
- Zie [Prestatiebewaking met queryarchief](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/) voor prestatieafstemming met queryopslag
- Zie [Prestaties azure SQL Database bewaken met dynamische beheerweergaven](sql-database-monitoring-with-dmvs.md) voor DMV-monitoringscripts
