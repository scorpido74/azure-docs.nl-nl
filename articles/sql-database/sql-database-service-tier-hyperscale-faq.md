---
title: Veelgestelde vragen over Hyperscale van Azure SQL Database
description: Antwoorden op veelgestelde vragen die klanten stellen over een Azure SQL-database in de Hyperscale-servicelaag - ook wel Hyperscale-database genoemd.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 03/03/2020
ms.openlocfilehash: 9f518df02b1923513fd014be53646a9a1be8465e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268624"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Veelgestelde vragen over Hyperscale van Azure SQL Database

In dit artikel vindt u antwoorden op veelgestelde vragen voor klanten die een database overwegen in de Azure SQL Database Hyperscale-servicelaag, die in de rest van deze faq alleen Hyperscale wordt genoemd. In dit artikel worden de scenario's beschreven die Hyperscale ondersteunt en de functies die compatibel zijn met Hyperscale.

- Deze FAQ is bedoeld voor lezers die een kort begrip van de Hyperscale service tier en zijn op zoek naar hun specifieke vragen en zorgen beantwoord.
- Deze veelgestelde vragen is niet bedoeld als een handleiding of antwoord op vragen over het gebruik van een Hyperscale-database. Voor een inleiding tot Hyperscale raden we u aan te verwijzen naar de [Azure SQL Database Hyperscale-documentatie.](sql-database-service-tier-hyperscale.md)

## <a name="general-questions"></a>Algemene vragen

### <a name="what-is-a-hyperscale-database"></a>Wat is een Hyperscale-database

Een Hyperscale-database is een Azure SQL-database in de hyperscale-servicelaag die wordt ondersteund door de Hyperscale scale-out-opslagtechnologie. Een Hyperscale-database ondersteunt tot 100 TB aan gegevens en biedt een hoge doorvoer en prestaties, evenals snelle schaling om zich aan te passen aan de workloadvereisten. Schalen is transparant voor de toepassing – connectiviteit, queryverwerking, enz.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Welke resourcetypen en inkoopmodellen ondersteunen Hyperscale

De hyperscale-servicelaag is alleen beschikbaar voor afzonderlijke databases met het op vCore gebaseerde inkoopmodel in Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Hoe verschilt de servicelaag Hyperscale van de serviceniveaus Algemeen Doel en Bedrijfskritieke

De op vCore gebaseerde servicelagen worden gedifferentieerd op basis van de beschikbaarheid en opslagtype van de database, de prestaties en de maximale grootte, zoals beschreven in de volgende tabel.

| | Resourcetype | Algemeen gebruik |  Hyperscale | Bedrijfskritisch |
|:---:|:---:|:---:|:---:|:---:|
| **Het beste voor** |Alle|Biedt budgetgeoriënteerde gebalanceerde reken- en opslagopties.|De meeste zakelijke workloads. Automatisch schalen van de opslaggrootte tot 100 TB, snelle verticale en horizontale compute scaling, snelle database herstel.|OLTP-toepassingen met een hoge transactiesnelheid en lage IO-latentie. Biedt de hoogste veerkracht voor storingen en snelle failovers met meerdere synchroon bijgewerkte replica's.|
|  **Resourcetype** ||Enkele database / elastische pool / beheerde instantie | Individuele database | Enkele database / elastische pool / beheerde instantie |
| **Rekengrootte**|Enkele database / elastische pool * | 1 tot 80 vCores | 1 tot 80 vCores* | 1 tot 80 vCores |
| |Beheerd exemplaar | 8, 16, 24, 32, 40, 64, 80 vCores | N.v.t. | 8, 16, 24, 32, 40, 64, 80 vCores |
| **Opslagtype** | Alle |Premium externe opslag (per exemplaar) | Gekoppelde opslag met lokale SSD-cache (per instantie) | Supersnelle lokale SSD-opslag (per exemplaar) |
| **Opslaggrootte** | Enkele database / elastische pool *| 5 GB – 4 TB | Tot 100 TB | 5 GB – 4 TB |
| | Beheerd exemplaar  | 32 GB – 8 TB | N.v.t. | 32 GB – 4 TB |
| **IOPS** | Individuele database | 500 IOPS per vCore met maximaal 7000 IOPS | Hyperscale is een multi-tiered architectuur met caching op meerdere niveaus. Effectieve IOPS is afhankelijk van de werkbelasting. | 5000 IOPS met 200.000 maximale IOPS|
| | Beheerd exemplaar | Afhankelijk van de bestandsgrootte | N.v.t. | 1375 IOPS/vCore |
|**Beschikbaarheid**|Alle|1 replica, geen uitgelezen uitschaing, geen lokale cache | Meerdere replica's, maximaal 4 uitgelezen, gedeeltelijke lokale cache | 3 replica's, 1 Uitgelezen, zoneredundante HA, volledige lokale opslag |
|**Back-ups**|Alle|RA-GRS, 7-35 dagen retentie (standaard 7 dagen)| RA-GRS, 7 dagen retentie, constant time point-in-time recovery (PITR) | RA-GRS, 7-35 dagen retentie (standaard 7 dagen) |

\*Elastische pools worden niet ondersteund in de servicelaag Hyperscale

### <a name="who-should-use-the-hyperscale-service-tier"></a>Wie moet de hyperscale-servicelaag gebruiken

De Hyperscale-servicelaag is bedoeld voor klanten die grote on-premises SQL Server-databases hebben en hun toepassingen willen moderniseren door over te stappen naar de cloud of voor klanten die Azure SQL Database al gebruiken en de potentieel voor databasegroei. Hyperscale is ook bedoeld voor klanten die zowel hoge prestaties als hoge schaalbaarheid nastreven. Met Hyperscale krijg je:

- Databasegrootte tot 100 TB
- Snelle databaseback-ups, ongeacht de grootte van de database (back-ups zijn gebaseerd op opslagmomentopnamen)
- Snelle database herstelt, ongeacht de databasegrootte (herstelis afkomstig van opslagmomentopnamen)
- Hogere logdoorvoer, ongeacht de databasegrootte en het aantal vCores
- Lees Scale-out met behulp van een of meer alleen-lezen replica's, gebruikt voor het aflezen van offloading en als hot standbys.
- Snelle opschaing van compute, in constante tijd, om krachtiger te zijn om de zware werkbelasting tegemoet te komen en vervolgens af te schalen, in constante tijd. Dit is vergelijkbaar met het op- en afschalen tussen bijvoorbeeld een P6 en een P11, maar veel sneller omdat dit geen grootte van gegevensbewerking is.

### <a name="what-regions-currently-support-hyperscale"></a>Welke regio's ondersteunen hyperscale momenteel

De servicelaag Hyperscale is momenteel beschikbaar in de regio's die worden vermeld onder [Azure SQL Database Hyperscale Overview](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Kan ik meerdere Hyperscale-databases per logische server maken

Ja. Zie [SQL Database-bronlimieten voor enkele en samengevoegde databases op een logische server voor](sql-database-resource-limits-logical-server.md)meer informatie en beperkingen over het aantal Hyperscale-databases per logische server.

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Wat zijn de prestatiekenmerken van een Hyperscale-database

De Hyperscale-architectuur biedt hoge prestaties en doorvoer en ondersteunt tegelijkertijd grote databaseformaten. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Wat is de schaalbaarheid van een Hyperscale-database

Hyperscale biedt snelle schaalbaarheid op basis van uw workloadvraag.

- **Op/omlaag schalen**

  Met Hyperscale u de primaire rekengrootte opschalen in termen van resources zoals CPU en geheugen en vervolgens in constante tijd verkleinen. Omdat de opslag wordt gedeeld, is het opschalen en verkleinen niet een grootte van de gegevensbewerking.  
- **In/uit schalen**

  Met Hyperscale krijgt u ook de mogelijkheid om een of meer extra compute replica's in te richten die u gebruiken om uw leesverzoeken te serveren. Dit betekent dat u deze extra compute replica's gebruiken als alleen-lezen replica's om uw leeswerkbelasting te ontladen van de primaire compute. Naast alleen-lezen, deze replica's dienen ook als hot-standbys in het geval van een failover van de primaire.

  Het inrichten van elk van deze aanvullende compute replica's kan worden gedaan in constante tijd en is een online operatie. U verbinding maken met deze extra alleen-lezen-gegevensreplica's door het `ApplicationIntent` argument op uw verbindingstekenreeks in te stellen op `ReadOnly`. Alle verbindingen `ReadOnly` met de toepassingsintentie worden automatisch doorgestuurd naar een van de extra alleen-lezen compute-replica's.

## <a name="deep-dive-questions"></a>Deep Dive vragen

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Kan ik Hyperscale en enkele databases mengen in één logische server

Ja, dat is mogelijk.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Heeft Hyperscale vereist dat mijn applicatie programmering model te veranderen

Nee, uw applicatie programmering model blijft zoals het is. U gebruikt uw verbindingstekenreeks zoals gewoonlijk en de andere reguliere manieren om te communiceren met uw Hyperscale-database.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Welk transactieisolatieniveau is de standaardinstelling in een Hyperscale-database

Op de primaire replica is het standaardniveau voor transactieisolatie RCSI (Lees vastgelegde momentopnameisolatie). Op de secundaire replica's voor uitlezen is het standaardisolatieniveau Momentopname.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Kan ik mijn on-premises of IaaS SQL Server-licentie naar Hyperscale brengen

Ja, [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) is beschikbaar voor Hyperscale. Elke SQL Server Standard-kern kan worden toegewezen aan 1 Hyperscale vCores. Elke SQL Server Enterprise-kern kan worden toegewezen aan 4 Hyperscale-vCores. U hebt geen SQL-licentie nodig voor secundaire replica's. De Azure Hybrid Benefit-prijs wordt automatisch toegepast op Leesschaal-out (secundaire) replica's.

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Voor welke workloads is Hyperscale ontworpen

Hyperscale ondersteunt alle SQL Server-workloads, maar is voornamelijk geoptimaliseerd voor OLTP. U ook hybride (HTAP) en Analytische (datamart)workloads meenemen.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-azure-sql-database-hyperscale"></a>Hoe kan ik kiezen tussen Azure SQL Data Warehouse en Azure SQL Database Hyperscale

Als u momenteel interactieve analysequery's uitvoert met SQL Server als gegevensmagazijn, is Hyperscale een geweldige optie omdat u kleine en middelgrote gegevensmagazijnen (zoals een paar TB tot 100 TB) tegen lagere kosten hosten en u uw SQL Server-gegevens migreren magazijnworkloads naar Hyperscale met minimale T-SQL-codewijzigingen.

Als u op grote schaal gegevensanalyses uitvoert met complexe query's en aanhoudende opnamepercentages hoger dan 100 MB/s, of als u Parallel Data Warehouse (PDW), Teradata of andere MPP-gegevensmagazijnen (Massively Parallel Processing) gebruikt, is SQL Data Warehouse mogelijk de beste keuze.
  
## <a name="hyperscale-compute-questions"></a>Hyperscale Compute-vragen

### <a name="can-i-pause-my-compute-at-any-time"></a>Kan ik mijn rekenkracht op elk gewenst moment pauzeren

Op dit moment u echter uw rekenkracht en aantal replica's schalen om de kosten tijdens niet-piekuren te verlagen.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>Kan ik een compute replica inrichten met extra RAM voor mijn geheugenintensieve workload

Nee. Om meer RAM te krijgen, moet u upgraden naar een hogere rekengrootte. Zie [Hyperscale-opslag- en rekenformaten voor](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)meer informatie.

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>Kan ik meerdere compute replica's van verschillende grootte sprodatatuur inrichten

Nee.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Hoeveel uitgelezen replica's worden ondersteund

De Hyperscale-databases worden standaard gemaakt met één Leesschaal-outreplica (twee replica's inclusief primair). U het aantal alleen-lezen replica's schalen tussen 0 en 4 met Azure [portal](https://portal.azure.com) of [REST API.](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>Voor hoge beschikbaarheid moet ik aanvullende compute-replica's inrichten

In Hyperscale-databases wordt gegevenstolerantie geleverd op opslagniveau. Je hebt maar één replica nodig om veerkracht te bieden. Wanneer de compute replica is uitgeschakeld, wordt er automatisch een nieuwe replica gemaakt zonder gegevensverlies.

Als er echter maar één replica is, kan het enige tijd duren voordat de lokale cache in de nieuwe replica na een failover is gebouwd. Tijdens de fase van het herbouwen van de cache haalt de database gegevens rechtstreeks van de paginaservers op, wat resulteert in een hogere opslaglatentie en verminderde queryprestaties.

Voor bedrijfskritieke apps waarvoor een hoge beschikbaarheid nodig is met minimale gevolgen voor failover, moet u ten minste 2 compute replica's inrichten, waaronder de primaire compute replica. Dit is de standaard configuratie. Op die manier is er een hot-standby replica beschikbaar die dient als een failover doel.

## <a name="data-size-and-storage-questions"></a>Vragen over gegevensgrootte en opslag

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Wat is de maximale databasegrootte ondersteund met Hyperscale

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Wat is de grootte van het transactielogboek met Hyperscale

Het transactielogboek met Hyperscale is vrijwel oneindig. U hoeft zich geen zorgen te maken over het opraken van de logboekruimte op een systeem met een hoge logdoorvoer. Echter, log generatie tarief kan worden beperkt voor continue agressief schrijven workloads. De piek aanhoudende log generatie tarief is 100 MB /s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>Is `tempdb` mijn schaal als mijn database groeit

Uw `tempdb` database bevindt zich op lokale SSD-opslag en is evenredig aangepast aan de rekengrootte die u indient. Uw `tempdb` is geoptimaliseerd om maximale prestatievoordelen te bieden. `tempdb`formaat is niet configureerbaar en wordt voor u beheerd.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>Wordt mijn databasegrootte automatisch groter of moet ik de grootte van gegevensbestanden beheren

Uw databasegrootte wordt automatisch groter naarmate u meer gegevens invoegt/inneemt.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Wat is de kleinste databasegrootte die Hyperscale ondersteunt of begint met

40 GB. Er wordt een Hyperscale-database gemaakt met een startgrootte van 10 GB. Vervolgens begint het te groeien met 10 GB elke 10 minuten, totdat het de grootte van 40 GB bereikt. Elk van deze 10 GB chucks is toegewezen in een andere pagina server om meer IOPS en hogere I / O parallellisme te bieden. Door deze optimalisatie wordt de database automatisch gegroeid tot minimaal 40 GB, zelfs als u kiest voor een initiële databasegrootte kleiner dan 40 GB.

### <a name="in-what-increments-does-my-database-size-grow"></a>In welke stappen groeit mijn databasegrootte

Elk gegevensbestand groeit met 10 GB. Meerdere gegevensbestanden kunnen tegelijkertijd groeien.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>Is de opslag in Hyperscale lokaal of extern

In Hyperscale worden gegevensbestanden opgeslagen in standaardopslag van Azure. Gegevens worden volledig opgeslagen op lokale SSD-opslag, op paginaservers die dicht bij de compute-replica's liggen. Bovendien hebben compute replica's gegevenscaches op lokale SSD en in het geheugen, om de frequentie van het ophalen van gegevens van externe paginaservers te verminderen.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Kan ik bestanden of bestandsgroepen beheren of definiëren met Hyperscale

Nee. Gegevensbestanden worden automatisch toegevoegd. De veelvoorkomende redenen voor het maken van extra bestandsgroepen zijn niet van toepassing in de Hyperscale-opslagarchitectuur.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Kan ik een hard cap voor de gegevensgroei voor mijn database

Nee.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Hoe worden gegevensbestanden ingedeeld met Hyperscale

De gegevensbestanden worden beheerd door paginaservers, met één paginaserver per gegevensbestand. Naarmate de gegevensgrootte toeneemt, worden gegevensbestanden en bijbehorende paginaservers toegevoegd.

### <a name="is-database-shrink-supported"></a>Wordt databasekrimp ondersteund

Nee.

### <a name="is-data-compression-supported"></a>Wordt gegevenscompressie ondersteund

Ja, inclusief compressie van rij-, pagina- en kolomarchief.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Als ik een enorme tabel heb, worden mijn tabelgegevens verspreid over meerdere gegevensbestanden

Ja. De gegevenspagina's die aan een bepaalde tabel zijn gekoppeld, kunnen in meerdere gegevensbestanden terechtkomen, die allemaal deel uitmaken van dezelfde bestandsgroep. SQL Server gebruikt [proportionele vulstrategie](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) om gegevens over gegevensbestanden te distribueren.

## <a name="data-migration-questions"></a>Vragen over gegevensmigratie

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Kan ik mijn bestaande Azure SQL-databases verplaatsen naar de hyperscale-servicelaag

Ja. U uw bestaande Azure SQL-databases verplaatsen naar Hyperscale. Dit is een eenrichtingsmigratie. U databases niet verplaatsen van Hyperscale naar een andere servicelaag. Voor proofs of concept (POC's) raden we u aan een kopie van uw database te maken en de kopie te migreren naar Hyperscale.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Kan ik mijn Hyperscale-databases verplaatsen naar andere servicelagen

Nee. Op dit moment u een Hyperscale-database niet verplaatsen naar een andere servicelaag.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Verlies ik functionaliteit of mogelijkheden na migratie naar de hyperscale-servicelaag

Ja. Sommige azure SQL-databasefuncties worden nog niet ondersteund in Hyperscale, inclusief maar niet beperkt tot het bewaren van back-ups op lange termijn. Nadat u uw databases naar Hyperscale hebt gemigreerd, werken deze functies niet meer.  We verwachten dat deze beperkingen tijdelijk zijn.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>Kan ik mijn on-premises SQL Server-database of mijn SQL Server-database in een virtuele cloudmachine verplaatsen naar Hyperscale

Ja. U alle bestaande migratietechnologieën gebruiken om te migreren naar Hyperscale, inclusief transactionele replicatie, en andere technologieën voor gegevensverplaatsing (Bulk Copy, Azure Data Factory, Azure Databricks, SSIS). Zie ook de [Azure Database Migration Service](../dms/dms-overview.md), die veel migratiescenario's ondersteunt.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Wat is mijn downtime tijdens de migratie van een on-premises of virtuele machine omgeving naar Hyperscale, en hoe kan ik minimaliseren

Downtime voor migratie naar Hyperscale is hetzelfde als de downtime wanneer u uw databases migreert naar andere Azure SQL Database-servicelagen. U [transactionele replicatie](replication-to-sql-database.md#data-migration-scenario
) gebruiken om downtimemigratie voor databases tot weinig TB-grootte te minimaliseren. Voor zeer grote databases (10+ TB) u overwegen om gegevens te migreren met BEHULP van ADF- of Spark- of andere technologieën voor gegevensverplaatsing.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Hoeveel tijd zou het kosten om X-hoeveelheid gegevens naar Hyperscale te brengen

Hyperscale kan 100 MB/s aan nieuwe/gewijzigde gegevens verbruiken, maar de tijd die nodig is om gegevens naar Azure SQL-databases te verplaatsen, wordt ook beïnvloed door de beschikbare netwerkdoorvoer, leessnelheid van de bron en de doelstelling voor doeldatabaseserviceniveau.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-sql-data-warehouse"></a>Kan ik gegevens uit blob-opslag lezen en snel laden (zoals Polybase in SQL Data Warehouse)

U een clienttoepassing gegevens uit Azure Storage laten lezen en gegevens laden in een Hyperscale-database (net zoals u dat met elke andere Azure SQL-database). Polybase wordt momenteel niet ondersteund in Azure SQL Database. Als alternatief voor snelle belasting u [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/)gebruiken of een Spark-taak gebruiken in Azure [Databricks](https://docs.microsoft.com/azure/azure-databricks/) met de [Spark-connector voor SQL.](sql-database-spark-connector.md) De Spark-connector naar SQL ondersteunt bulkinsert.

Het is ook mogelijk om bulkgegevens uit Azure Blob store te lezen met BULK INSERT of OPENROWSET: [Voorbeelden van bulktoegang tot gegevens in Azure Blob Storage.](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location)

Eenvoudig herstel- of bulkregistratiemodel wordt niet ondersteund in Hyperscale. Volledig herstelmodel is vereist om een hoge beschikbaarheid en point-in-time recovery te bieden. Hyperscale-logboekarchitectuur biedt echter een betere gegevensinnamesnelheid in vergelijking met andere Azure SQL Database-servicelagen.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>Maakt Hyperscale het mogelijk om meerdere knooppunten in te richten voor het parallel opnemen van grote hoeveelheden gegevens

Nee. Hyperscale is een symmetrische multi-processing (SMP) architectuur en is geen massaal parallelle processing (MPP) of een multi-master architectuur. U slechts meerdere replica's maken om alleen-lezen workloads uit te schalen.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Wat is de oudste SQL Server-versie die wordt ondersteund voor migratie naar Hyperscale

SQL Server 2005. Zie [Migreren naar één database of een samengevoegde database voor](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database)meer informatie. Zie [Compatibiliteitsproblemen met databasemigratie oplossen](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues)voor compatibiliteitsproblemen met compatibiliteitsproblemen met database .

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>Hyperscale ondersteunt migratie van andere gegevensbronnen zoals Amazon Aurora, MySQL, PostgreSQL, Oracle, DB2 en andere databaseplatforms

Ja. [Azure Database Migration Service](../dms/dms-overview.md) ondersteunt veel migratiescenario's.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Vragen over bedrijfscontinuïteit en noodherstel

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Welke SLA's zijn beschikbaar voor een Hyperscale-database

Zie [SLA voor Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/). Extra secundaire compute replica's verhogen de beschikbaarheid, tot 99,99% voor een database met twee of meer secundaire compute replica's.

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Worden de databaseback-ups voor mij beheerd door de Azure SQL Database-service

Ja.

### <a name="how-often-are-the-database-backups-taken"></a>Hoe vaak worden de databaseback-ups genomen

Er zijn geen traditionele volledige, differentiële en logboekback-ups voor Hyperscale-databases. In plaats daarvan zijn er regelmatig opslagmomentopnamen van gegevensbestanden. Logboek dat wordt gegenereerd, wordt gewoon behouden zoals deze is voor de geconfigureerde bewaarperiode, waardoor het herstel naar elk moment binnen de bewaartermijn mogelijk is.

### <a name="does-hyperscale-support-point-in-time-restore"></a>Is Hyperscale support point in time restore

Ja.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>Wat is de Recovery Point Objective (RPO)/Recovery Time Objective (RTO) voor databaserestore in Hyperscale

De RPO is 0 min. Het RTO-doel is minder dan 10 minuten, ongeacht de databasegrootte. 

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>Heeft databaseback-up invloed op de rekenprestaties op mijn primaire of secundaire replica's

Nee. Back-ups worden beheerd door het subsysteem opslag en maken gebruik van opslagmomentopnamen. Ze hebben geen invloed op de workloads van gebruikers.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>Kan ik geo-restore uitvoeren met een Hyperscale-database

Ja. Geo-restore wordt volledig ondersteund. In tegenstelling tot point-in-time restore vereist geo-restore een grootte van de gegevensbewerking. Gegevensbestanden worden parallel gekopieerd, dus de duur van deze bewerking is voornamelijk afhankelijk van de grootte van het grootste bestand in de database, in plaats van van de totale databasegrootte. De geohersteltijd is aanzienlijk korter als de database wordt hersteld in het Azure-gebied dat is [gekoppeld aan](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) het gebied van de brondatabase.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Kan ik geo-replicatie instellen met Hyperscale-database

Momenteel niet.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>Kan ik een Hyperscale-databaseback-up maken en deze herstellen naar mijn on-premises server of op SQL Server in een VM

Nee. De opslagindeling voor Hyperscale-databases verschilt van elke vrijgegeven versie van SQL Server en u hebt geen back-ups of hebt er geen toegang toe. Als u uw gegevens uit een Hyperscale-database wilt halen, u gegevens extraheren met behulp van technologieën voor gegevensverplaatsing, d.w.z. Azure Data Factory, Azure Databricks, SSIS, enz.

## <a name="cross-feature-questions"></a>Vragen over functies

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Verlies ik functionaliteit of mogelijkheden na migratie naar de hyperscale-servicelaag

Ja. Sommige azure SQL-databasefuncties worden niet ondersteund in Hyperscale, inclusief maar niet beperkt tot het bewaren van back-ups op lange termijn. Nadat u uw databases naar Hyperscale hebt gemigreerd, werken deze functies niet meer.

### <a name="will-polybase-work-with-hyperscale"></a>Werkt Polybase met Hyperscale

Nee. Polybase wordt niet ondersteund in Azure SQL Database.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>Heeft Hyperscale ondersteuning voor R en Python

Momenteel niet.

### <a name="are-compute-nodes-containerized"></a>Worden compute nodes gecontaineriseerd

Nee. Hyperscale-processen worden uitgevoerd op een [Service Fabric-knooppunten](https://azure.microsoft.com/services/service-fabric/) (VM's), niet in containers.

## <a name="performance-questions"></a>Prestatievragen

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Hoeveel schrijfdoorvoer kan ik pushen in een Hyperscale-database

De doorvoerlimiet voor transactielogboeken is ingesteld op 100 MB/s voor elke hyperscale-compute-grootte. De mogelijkheid om dit percentage te bereiken is afhankelijk van meerdere factoren, waaronder maar niet beperkt tot werkbelastingtype, clientconfiguratie en voldoende rekencapaciteit op de primaire compute replica om logboek te produceren met deze snelheid.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Hoeveel IOPS krijg ik op de grootste compute

IOPS en IO latency variëren afhankelijk van de werkbelastingpatronen. Als de gegevens die worden geopend in de cache worden opgeslagen op de compute-replica, ziet u vergelijkbare IO-prestaties als bij lokale SSD.If the data being accessed is cached on the compute replica, you will see similar IO performance as with local SSD.

### <a name="does-my-throughput-get-affected-by-backups"></a>Is mijn doorvoer te worden beïnvloed door back-ups

Nee. Compute is losgekoppeld van de opslaglaag. Dit elimineert de impact van de prestaties van back-up.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>Wordt mijn doorvoer beïnvloed als ik extra compute replica's indplaats

Omdat de opslag wordt gedeeld en er geen directe fysieke replicatie plaatsvindt tussen primaire en secundaire compute-replica's, wordt de doorvoer op primaire replica niet rechtstreeks beïnvloed door het toevoegen van secundaire replica's. We kunnen echter gas geven aan de continue agressief schrijven de werkbelasting op de primaire om logboek toe te passen op secundaire replica's en paginaservers in te halen, om slechte leesprestaties op secundaire replica's te voorkomen.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Hoe kan ik prestatieproblemen in een Hyperscale-database diagnosticeren en oplossen

Voor de meeste prestatieproblemen, met name die welke niet zijn geworteld in de opslagprestaties, zijn algemene sql-serverdiagnostische en probleemoplossingsstappen van toepassing. Zie [SQL Hyperscale-prestatieprobleemoplossingsdiagnostiek](sql-database-hyperscale-performance-diagnostics.md)voor hyperscale-specifieke opslagdiagnostiek.

## <a name="scalability-questions"></a>Schaalbaarheidsvragen

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Hoe lang zou het duren om een compute replica op en neer te schalen

Het op- of afschalen van compute duurt 5-10 minuten, ongeacht de grootte van de gegevens.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Is mijn database offline terwijl de op/neer schalen de bewerking aan de gang is

Nee. Het op- en afschalen zal online zijn.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Moet ik de verbindingsdaling verwachten wanneer de schaalbewerkingen aan de gang zijn

Als u deze schaal of neerzet, worden bestaande verbindingen verwijderd wanneer er een failover plaatsvindt aan het einde van de schalingbewerking. Het toevoegen van secundaire replica's leidt niet tot verbindingsdalingen.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>Is het op- en afschalen van compute replica's automatisch of de bewerking van de eindgebruiker geactiveerd

Eindgebruiker. Niet automatisch.  

### <a name="does-the-size-of-my-tempdb-database-also-grow-as-the-compute-is-scaled-up"></a>Is de grootte `tempdb` van mijn database ook groeien als de compute wordt opgeschaald

Ja. De `tempdb` database wordt automatisch opschalen naarmate de compute groeit.  

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Kan ik meerdere primaire compute replica's inrichten, zoals een multi-master systeem, waarbij meerdere primaire rekenkoppen een hoger niveau van gelijktijdigheid kunnen aansturen

Nee. Alleen de primaire compute replica accepteert lees-/schrijfverzoeken. Secundaire compute replica's accepteren alleen-lezen aanvragen.

## <a name="read-scale-out-questions"></a>Vragen voor uitschalen lezen

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Hoeveel secundaire compute replica's kan ik inrichten

We maken standaard één secundaire replica voor Hyperscale-databases. Als u het aantal replica's wilt aanpassen, u dit doen met [Azure-portal](https://portal.azure.com) of [REST API.](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Hoe maak ik verbinding met deze secundaire compute replica's

U verbinding maken met deze extra alleen-lezen-gegevensreplica's door het `ApplicationIntent` argument op uw verbindingstekenreeks in te stellen op `ReadOnly`. Alle verbindingen `ReadOnly` die zijn gemarkeerd met automatisch worden doorgestuurd naar een van de extra alleen-lezen compute replica's.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Hoe kan ik valideren of ik met succes verbinding heb gemaakt met secundaire compute replica met Behulp van SSMS of andere clienttools?

U de volgende T-SQL-query uitvoeren: `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')`.
Het resultaat `READ_ONLY` is als u bent verbonden met `READ_WRITE` een alleen-lezen secundaire replica, en als u bent aangesloten op de primaire replica. Houd er rekening mee dat de databasecontext moet worden ingesteld `master` op de naam van de Hyperscale-database, niet op de database.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>Kan ik een speciaal eindpunt maken voor een uitgelezen replica

Nee. U alleen verbinding maken met Uitgelezen `ApplicationIntent=ReadOnly`replica's door op te geven.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Doet het systeem intelligente taakverdeling van de leeswerkbelasting

Nee. Een nieuwe verbinding met alleen-lezen intentie wordt doorgestuurd naar een willekeurige Read Scale-out replica.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>Kan ik de secundaire compute replica's opschalen/omlaag onafhankelijk van de primaire replica

Nee. De secundaire compute replica worden ook gebruikt als failoverdoelen met hoge beschikbaarheid, dus ze moeten dezelfde configuratie hebben als de primaire om verwachte prestaties te leveren na failover.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Krijg ik `tempdb` verschillende maten voor mijn primaire compute en mijn extra secundaire compute replica's

Nee. Uw `tempdb` database is geconfigureerd op basis van de compute size provisioning, uw secundaire compute replica's zijn even groot als de primaire compute.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>Kan ik indexen en weergaven toevoegen aan mijn secundaire compute replica's

Nee. Hyperscale databases hebben gedeelde opslag, wat betekent dat alle compute replica's dezelfde tabellen, indexen en weergaven te zien krijgen. Als u extra indexen wilt die zijn geoptimaliseerd voor leest op secundaire, moet u deze toevoegen aan de primaire.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Hoeveel vertraging er zal zijn tussen de primaire en secundaire compute replica's

De latentie van gegevens vanaf het moment dat een transactie wordt vastgelegd op het primaire tot het tijdstip waarop deze zichtbaar is op een secundaire, is afhankelijk van de huidige mate van logboekgeneratie. Typische gegevenslatentie is in lage milliseconden.

## <a name="next-steps"></a>Volgende stappen

Zie [Hyperscale-servicelaag](sql-database-service-tier-hyperscale.md)voor meer informatie over de servicelaag Hyperscale.
