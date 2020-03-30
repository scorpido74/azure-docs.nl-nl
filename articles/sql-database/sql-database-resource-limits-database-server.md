---
title: Azure SQL Database-bronlimieten | Microsoft Documenten
description: In dit artikel vindt u een overzicht van de azure SQL Database-bronlimieten voor afzonderlijke databases en elastische pools. Het biedt ook informatie over wat er gebeurt wanneer deze resourcelimieten worden bereikt of overschreden.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/19/2019
ms.openlocfilehash: 550c315023c0ae907c369778c81b16e137004bec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067251"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>SQL Database-resourcelimieten en resourcegovernance

In dit artikel vindt u een overzicht van de SQL Database-bronlimieten voor een SQL Database-server die afzonderlijke databases en elastische pools beheert. Het geeft informatie over wat er gebeurt wanneer deze resourcelimieten worden bereikt of overschreden, en beschrijft de mechanismen voor resourcegovernance die worden gebruikt om deze limieten af te dwingen.

> [!NOTE]
> Zie [SQL Database-bronlimieten voor beheerde exemplaren voor](sql-database-managed-instance-resource-limits.md)limieten voor beheerde exemplaren voor beheerde instanties.

## <a name="maximum-resource-limits"></a>Maximale resourcelimieten

| Resource | Limiet |
| :--- | :--- |
| Databases per server | 5000 |
| Standaardaantal servers per abonnement in elke regio | 20 |
| Maximaal aantal servers per abonnement in elke regio | 200 |  
| DTU/ eDTU-quotum per server | 54,000 |  
| vCore-quotum per server/instantie | 540 |
| Maximale groepen per server | Beperkt door het aantal DTUs of vCores. Als elke groep bijvoorbeeld 1000 DTU's is, kan een server 54 groepen ondersteunen.|
|||

> [!IMPORTANT]
> Als het aantal databases de limiet per SQL Database-server benadert, kan het volgende optreden:
>
> - Toenemende latentie bij het uitvoeren van query's ten opzichte van de hoofddatabase.  Dit omvat weergaven van resourcegebruiksstatistieken zoals sys.resource_stats.
> - Toenemende latentie in beheerbewerkingen en rendering portal standpunten die betrekking hebben op het opsommen van databases in de server.

> [!NOTE]
> Als u meer DTU/eDTU-quota, vCore-quota of meer servers dan het standaardbedrag wilt verkrijgen, dient u een nieuwe ondersteuningsaanvraag in de Azure-portal in. Zie [Quotaverhogingen voor Azure SQL Database aanvragen voor](quota-increase-request.md)meer informatie.

### <a name="storage-size"></a>Opslaggrootte

Voor de grootte van resources in afzonderlijke databases raadpleegt u [dtu-gebaseerde resourcelimieten](sql-database-dtu-resource-limits-single-databases.md) of [vCore-gebaseerde resourcelimieten](sql-database-vcore-resource-limits-single-databases.md) voor de opslaggroottelimieten per prijscategorie.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>Wat gebeurt er als databaseresourcelimieten worden bereikt

### <a name="compute-dtus-and-edtus--vcores"></a>Compute (DTU's en eDTUs / vCores)

Wanneer het gebruik van databasegegevens (gemeten door DTU's en eDTU's, of vCores) hoog wordt, neemt de latentie van query's toe en kunnen query's zelfs een time-out krijgen. Onder deze voorwaarden kunnen query's in de wachtrij worden geplaatst door de service en worden resources voor uitvoering geleverd naarmate resources gratis worden.
Bij een hoog computergebruik zijn mitigatieopties:

- De rekengrootte van de database of elastische groep verhogen om de database te voorzien van meer rekenbronnen. Zie [Afzonderlijke databaseresources schalen](sql-database-single-database-scale.md) en [Elastische poolbronnen schalen](sql-database-elastic-pool-scale.md).
- Query's optimaliseren om het resourcegebruik van elke query te verminderen. Zie [Queryafstemming/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting)voor meer informatie.

### <a name="storage"></a>Storage

Wanneer de gebruikte databaseruimte de maximale groottelimiet bereikt, mislukken database-inserts en updates die de gegevensgrootte vergroten en ontvangen clients een [foutmelding.](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md) SELECT- en VERWIJDER-instructies blijven slagen.

Bij het tegenkomen van een hoge ruimte gebruik, mitigatie opties omvatten:

- De maximale grootte van de database of elastische pool vergroten of meer opslagruimte toevoegen. Zie [Afzonderlijke databaseresources schalen](sql-database-single-database-scale.md) en [Elastische poolbronnen schalen](sql-database-elastic-pool-scale.md).
- Als de database zich in een elastische groep bevindt, kan de database als alternatief buiten de groep worden verplaatst, zodat de opslagruimte niet wordt gedeeld met andere databases.
- Een database verkleinen om ongebruikte ruimte terug te winnen. Zie [Bestandsruimte beheren in Azure SQL Database](sql-database-file-space-management.md) voor meer informatie

### <a name="sessions-and-workers-requests"></a>Sessies en werknemers (aanvragen)

Het maximum aantal sessies en werknemers wordt bepaald door de servicelaag en de rekengrootte (DTU/eDTU's of vCores). Nieuwe aanvragen worden afgewezen wanneer sessie- of werklimieten worden bereikt en clients een foutbericht ontvangen. Hoewel het aantal beschikbare verbindingen door de toepassing kan worden gecontroleerd, is het aantal gelijktijdige werknemers vaak moeilijker in te schatten en te controleren. Dit geldt vooral tijdens piekbelastingsperioden wanneer databaseresourcelimieten worden bereikt en werknemers zich opstapelen als gevolg van langere query's, grote blokkeringsketens of overmatig queryparallellisme.

Bij het tegenkomen van een hoge sessie of werknemer gebruik, mitigatie opties omvatten:

- De servicelaag of rekengrootte van de database of elastische groep verhogen. Zie [Afzonderlijke databaseresources schalen](sql-database-single-database-scale.md) en [Elastische poolbronnen schalen](sql-database-elastic-pool-scale.md).
- Query's optimaliseren om het resourcegebruik van elke query te verminderen als de oorzaak van een groter gebruik van werknemers te wijten is aan onenigheid voor rekenresources. Zie [Queryafstemming/Hinting](sql-database-performance-guidance.md#query-tuning-and-hinting)voor meer informatie.
- Het verminderen van de [MAXDOP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option#Guidelines) (maximale mate van parallellisme) instelling.
- Querywerkbelasting optimaliseren om het aantal voorvallen en de duur van queryblokkering te verminderen.

### <a name="resource-consumption-by-user-workloads-and-internal-processes"></a>Resourceverbruik door gebruikersworkloads en interne processen

CPU- en geheugenverbruik door gebruikersworkloads in elke database wordt gerapporteerd in `avg_cpu_percent` `avg_memory_usage_percent` de [sys.dm_db_resource_stats-](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) en [sys.resource_stats-weergaven,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) in en in kolommen. Voor elastische pools wordt resourceverbruik op poolsniveau gerapporteerd in de weergave [sys.elastic_pool_resource_stats.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) Cpu-verbruik voor gebruikersworkloads `cpu_percent` wordt ook gerapporteerd via de Azure Monitor-statistiek, voor [afzonderlijke databases](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) en [elastische pools](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) op poolniveau.

Azure SQL Database vereist compute resources voor het implementeren van kernservicefuncties zoals hoge beschikbaarheid en disaster recovery, database back-up en herstel, monitoring, Query Store, Automatische tuning, enz. Het systeem zet een bepaald beperkt deel van de totale resources voor deze interne processen opzij met behulp van [mechanismen voor resourcegovernance,](#resource-governance) waardoor de rest van de resources beschikbaar is voor gebruikersworkloads. Op momenten dat interne processen geen compute resources gebruiken, maakt het systeem ze beschikbaar voor workloads van gebruikers.

Het totale CPU- en geheugenverbruik door gebruikersworkloads en interne processen op het SQL Server-exemplaar dat een enkele `avg_instance_cpu_percent` `avg_instance_memory_percent` database of een elastische groep host, wordt gerapporteerd in de [sys.dm_db_resource_stats-](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database?view=azuresqldb-current) en [sys.resource_stats-weergaven](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database?view=azuresqldb-current) in en in kolommen. Deze gegevens worden ook `sqlserver_process_core_percent` `sqlserver_process_memory_percent` gerapporteerd via de statistieken en Azure Monitor, voor [afzonderlijke databases](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserversdatabases) en [elastische pools](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftsqlserverselasticpools) op poolniveau.

Een meer gedetailleerde uitsplitsing van het recente resourceverbruik naar gebruikersworkloads en interne processen wordt gerapporteerd in de weergaven [sys.dm_resource_governor_resource_pools_history_ex](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-resource-pools-history-ex-azure-sql-database) en [sys.dm_resource_governor_workload_groups_history_ex.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-resource-governor-workload-groups-history-ex-azure-sql-database) Zie [Resourcegovernance](#resource-governance)voor meer informatie over resourcegroepen en werkbelastinggroepen waarnaar in deze weergaven wordt verwezen. Deze weergaven rapporteren over het gebruik van resources door gebruikersworkloads en specifieke interne processen in de bijbehorende resourcegroepen en werkworkloadgroepen.

In het kader van prestatiebewaking en probleemoplossing is het`avg_cpu_percent` `cpu_percent`belangrijk om zowel het **cpu-verbruik** van gebruikers ( , ) als **het totale CPU-verbruik** door gebruikersworkloads en interne processen in overweging te nemen ( ,`avg_instance_cpu_percent``sqlserver_process_core_percent`).

**Het CPU-verbruik** van gebruikers wordt berekend als een percentage van de gebruikersworkloadlimieten in elke servicedoelstelling. **Het gebruik van de cpu-gebruiker** op 100% geeft aan dat de gebruikersworkload de limiet van de servicedoelstelling heeft bereikt. Wanneer het **totale CPU-verbruik** echter het bereik van 70-100% bereikt, is het mogelijk om de doorvoer van de gebruikersworkload af te vlakken en de latentie van query's te verhogen, zelfs als het gerapporteerde **CPU-verbruik** van de gebruiker aanzienlijk onder de 100% blijft. Dit is waarschijnlijker bij het gebruik van kleinere servicedoelstellingen met een matige toewijzing van rekenresources, maar relatief intense gebruikersworkloads, zoals in [dichte elastische pools.](sql-database-elastic-pool-resource-management.md) Dit kan ook gebeuren met kleinere servicedoelstellingen wanneer interne processen tijdelijk extra resources vereisen, bijvoorbeeld bij het maken van een nieuwe replica van de database.

Wanneer **het totale CPU-verbruik** hoog is, zijn mitigatieopties hetzelfde als eerder vermeld, en omvatten servicedoelstelling te verhogen en/of user workload optimalisatie.

## <a name="resource-governance"></a>Resourcebeheer

Om resourcelimieten af te dwingen, gebruikt Azure SQL Database een implementatie voor resourcebeheer die is gebaseerd op SQL Server [Resource Governor,](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor)gewijzigd en uitgebreid om een SQL Server-databaseservice in Azure uit te voeren. Op elke SQL Server-instantie in de service zijn er meerdere [resourcegroepen](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) en [werkworkloadgroepen,](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group)met resourcelimieten die zijn ingesteld op zowel pool- als groepsniveaus om een [gebalanceerde Database-as-a-Service](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/)te bieden. Gebruikersworkloads en interne workloads worden ingedeeld in afzonderlijke resourcegroepen en workloadgroepen. Gebruikerswerkbelasting op de primaire en leesbare secundaire replica's, `SloSharedPool1` waaronder `UserPrimaryGroup.DBId[N]` georeplica's, wordt ingedeeld in de resourcegroep en werkbelastinggroep, waar `N` staat voor de waarde van de database-id. Daarnaast zijn er meerdere resourcepools en workloadgroepen voor verschillende interne workloads.

Azure SQL Database gebruikt niet alleen Resource Governor om resources binnen het SQL Server-proces te beheren, maar gebruikt ook Windows [Job Objects](https://docs.microsoft.com/windows/win32/procthread/job-objects) voor resourcegovernance op procesniveau en Windows File Server Resource [Manager (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) voor opslagquotumbeheer.

Azure SQL Database resource governance is hiërarchisch van aard. Van boven naar beneden worden limieten afgedwongen op os-niveau en op het niveau van het opslagvolume met behulp van beheermechanismen voor besturingssysteemresources en Resource Governor, vervolgens op resourcepoolniveau met Resource Governor en vervolgens op het niveau van de werkbelastinggroep met behulp van Resource Gouverneur. Resourcegovernancelimieten die van kracht zijn voor de huidige database of elastische groep, worden weergegeven in de [sys.dm_user_db_resource_governance-weergave.](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) 

### <a name="data-io-governance"></a>Data IO-governance

Data IO-governance is een proces in Azure SQL Database dat wordt gebruikt om zowel lezen als schrijven van fysieke IO te beperken tot gegevensbestanden van een database. IOPS-limieten zijn ingesteld voor elk serviceniveau om het effect 'luidruchtige buren' te minimaliseren, de toewijzing van resources in de multi-tenantservice te bieden en binnen de mogelijkheden van de onderliggende hardware en opslag te blijven.

Voor afzonderlijke databases worden limieten voor workloadgroepen toegepast op alle opslag-IO's tegen de database, terwijl resourcepoollimieten van toepassing zijn op alle opslag-IO voor alle databases in dezelfde SQL Server-instantie, inclusief de `tempdb` database. Voor elastische groepen zijn limieten voor werkbelastinggroepen van toepassing op elke database in `tempdb` de groep, terwijl de limiet voor de resourcegroep van toepassing is op de volledige elastische groep, inclusief de database, die wordt gedeeld tussen alle databases in de groep. In het algemeen zijn resourcepoollimieten mogelijk niet haalbaar door de werkbelasting ten opzichte van een database (één of gepoold), omdat de limieten voor werkbelastinggroepen lager zijn dan de limieten voor resourcegroepen en de IOPS/doorvoer eerder beperken. De gecombineerde workload kan echter worden bereikt door de gecombineerde workload ten opzichte van meerdere databases op dezelfde SQL Server-instantie.

Als een query bijvoorbeeld 1000 IOPS genereert zonder io-resourcebeheer, maar de maximale IOPS-limiet voor workloadgroep is ingesteld op 900 IOPS, kan de query niet meer dan 900 IOPS genereren. Als de maximale IOPS-limiet voor resourcesgroep echter is ingesteld op 1500 IOPS en het totale IO van alle workloadgroepen die zijn gekoppeld aan de resourcegroep meer dan 1500 IOPS, kan de IO van dezelfde query worden verlaagd tot onder de werkgroeplimiet van 900 IOPS.

De IOPS- en doorvoermin/max-waarden die worden geretourneerd door de [sys.dm_user_db_resource_governance-weergave](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) fungeren als limieten/caps, niet als garanties. Bovendien garandeert resourcegovernance geen specifieke opslaglatentie. De best haalbare latentie, IOPS en doorvoer voor een bepaalde gebruikersworkload zijn niet alleen afhankelijk van io-resourcegovernancelimieten, maar ook van de mix van io-formaten die worden gebruikt en van de mogelijkheden van de onderliggende opslag. SQL Server maakt gebruik van IOs die variëren in grootte tussen 512 KB en 4 MB. Voor de handhaving van IOPS-limieten wordt elke IO geboekt, ongeacht de grootte, met uitzondering van databases met gegevensbestanden in Azure Storage. In dat geval worden IOs groter dan 256 KB geboekt als meerdere 256 KB-IOs, om af te stemmen op azure storage IO-boekhouding.

Voor basisdatabases, standaard- en algemene beheerdatabases, die `primary_group_max_io` gegevensbestanden in Azure Storage gebruiken, is de waarde mogelijk niet haalbaar als een database niet genoeg gegevensbestanden heeft om dit aantal IOPS cumulatief op te geven, of als gegevens niet gelijkmatig over bestanden worden verdeeld, of als de prestatielaag van onderliggende blobs IOPS/doorvoer beperkt onder de limiet voor resourcegovernance. Met kleine log-IOs die worden gegenereerd `primary_max_log_rate` door frequente transactiecommit, is de waarde mogelijk niet haalbaar door een werkbelasting vanwege de IOPS-limiet voor de onderliggende Azure-opslagblob.

Waarden voor resourcegebruik, `avg_data_io_percent` zoals en `avg_log_write_percent`, gerapporteerd in de weergaven [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)en [sys.elastic_pool_resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) worden berekend als percentages van de maximale resourcegovernancelimieten. Wanneer andere factoren dan resource governance IOPS/throughput beperken, is het daarom mogelijk om IOPS/throughput afte vlakken en latencies te zien toenemen naarmate de werkbelasting toeneemt, ook al blijft het gerapporteerde resourcegebruik onder de 100%. 

Als u IOPS, doorvoer en latentie per databasebestand wilt lezen en schrijven, gebruikt u de functie [sys.dm_io_virtual_file_stats()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) om iops, doorvoer en latentie per databasebestand te bekijken. Deze functie oppervlak alle IO tegen de database, met `avg_data_io_percent`inbegrip van achtergrond IO die niet wordt verwerkt naar , maar maakt gebruik van IOPS en doorvoer van de onderliggende opslag, en kan van invloed zijn waargenomen opslag latentie. De functie geeft ook extra latentie weer die kan worden geïntroduceerd door `io_stall_queued_read_ms` `io_stall_queued_write_ms` IO resource governance voor respectievelijk reads en writes.

### <a name="transaction-log-rate-governance"></a>Beheer van transactielogboekratio's

Beheer van transactielogboeksnelheden is een proces in Azure SQL Database dat wordt gebruikt om hoge opnamepercentages voor workloads zoals bulkinserts, SELECT INTO en indexbuilds te beperken. Deze limieten worden bijgehouden en afgedwongen op het tweede niveau tot de snelheid van het genereren van logboekrecorden, waardoor de doorvoer wordt beperkt, ongeacht hoeveel IOs kunnen worden uitgegeven tegen gegevensbestanden.  Transactieloggeneratietarieven schalen momenteel lineair naar een punt dat hardwareafhankelijk is, waarbij de maximaal toegestane logsnelheid 96 MB/s is met het vCore-inkoopmodel. 

> [!NOTE]
> De werkelijke fysieke IOs naar transactie log bestanden worden niet geregeld of beperkt.

Logtarieven zijn zo ingesteld dat ze kunnen worden bereikt en ondersteund in een verscheidenheid van scenario's, terwijl het algemene systeem kan zijn functionaliteit te behouden met een minimale impact op de belasting van de gebruiker. Log rate governance zorgt ervoor dat transactielogback-ups binnen gepubliceerde herstelbaarheids-SLA's blijven.  Dit bestuur voorkomt ook een buitensporige achterstand op secundaire replica's.

Als logboekrecords worden gegenereerd, wordt elke bewerking geëvalueerd en beoordeeld op de vraag of deze moet worden vertraagd om een maximale gewenste logsnelheid (MB/s per seconde) te behouden. De vertragingen worden niet toegevoegd wanneer de logboekrecords naar opslag worden gespoeld, in plaats van dat beheer van logboekfrequentie wordt toegepast tijdens het genereren van logkoersen zelf.

De werkelijke log generatie tarieven opgelegd tijdens de looptijd kan ook worden beïnvloed door feedback mechanismen, tijdelijk verminderen van de toegestane log tarieven, zodat het systeem kan stabiliseren. Log bestandsruimtebeheer, voorkomen dat u uit de instellingen van de logboekruimte loopt en replicatiemechanismen voor beschikbaarheidsgroepen de algemene systeemlimieten tijdelijk kunnen verlagen.

Log rate governor traffic shaping is opgedoken via de volgende wachttypen (blootgesteld in de [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database) DMV):

| Wachttype | Opmerkingen |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Databasebeperkend |
| POOL_LOG_RATE_GOVERNOR | Beperking van de pool |
| INSTANCE_LOG_RATE_GOVERNOR | Beperking van het instantieniveau |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Feedbackbesturingselement, fysieke replicatie van beschikbaarheidsgroepen in Premium/Business Critical houdt het niet bij |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Feedbackcontrole, beperking van de tarieven om een voorwaarde voor een niet-logboekruimte te voorkomen |
|||

Wanneer u een limiet voor de logsnelheid tegenkomt die de gewenste schaalbaarheid belemmert, moet u rekening houden met de volgende opties:
- Schaal op naar een hoger serviceniveau om de maximale logsnelheid van 96 MB/s te krijgen of schakel over naar een andere servicelaag. De [hyperscale-servicelaag](sql-database-service-tier-hyperscale.md) biedt een logsnelheid van 100 MB/s, ongeacht het gekozen serviceniveau.
- Als gegevens die worden geladen van voorbijgaande aard zijn, zoals faseringsgegevens in een ETL-proces, kan deze worden geladen in tempdb (die minimaal is vastgelegd). 
- Voor analytische scenario's wordt u in een gedekte tabel met geclusterde kolomarchief geladen. Dit vermindert de vereiste logsnelheid als gevolg van compressie. Deze techniek verhoogt het CPU-gebruik en is alleen van toepassing op gegevenssets die profiteren van geclusterde kolomarchiefindexen. 

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor informatie over algemene Azure-limieten.
- Zie [DTU's en eDTU's voor informatie over DTU's en eDTU's.](sql-database-purchase-models.md#dtu-based-purchasing-model)
- Zie [TempDB in Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database)voor informatie over tempdb-groottelimieten.
