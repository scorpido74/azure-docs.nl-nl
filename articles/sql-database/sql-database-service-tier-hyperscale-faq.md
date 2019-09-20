---
title: Veelgestelde vragen over Azure SQL Database grootschalige | Microsoft Docs
description: Antwoorden op veelgestelde vragen klanten vragen over een Azure-SQL database in de grootschalige-service laag, meestal een grootschalige-data base genoemd.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/06/2019
ms.openlocfilehash: 8c35877c7de2fa89a8fe7a94c11787814183df9e
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162258"
---
# <a name="faq-about-azure-sql-hyperscale-databases"></a>Veelgestelde vragen over Azure SQL grootschalige-data bases

In dit artikel vindt u antwoorden op veelgestelde vragen over klanten die overwegen om een data base in Azure SQL Database de grootschalige-servicelaag, ook wel een grootschalige-data base genoemd. In dit artikel worden de scenario's beschreven die door grootschalige worden ondersteund en de services voor meerdere functies in het algemeen compatibel zijn met SQL Database grootschalige.

- Deze veelgestelde vragen zijn bedoeld voor lezers die een korte uitleg hebben over de grootschalige-service tier en op zoek zijn naar hun specifieke vragen en antwoorden.
- Deze veelgestelde vragen zijn niet bedoeld als een Guidebook of antwoorden op vragen over het gebruik van een SQL Database grootschalige-data base. Daarom raden we u aan de [Azure SQL database grootschalige](sql-database-service-tier-hyperscale.md) -documentatie te raadplegen.

## <a name="general-questions"></a>Algemene vragen

### <a name="what-is-a-hyperscale-database"></a>Wat is een grootschalige-data base?

Een grootschalige-data base is een Azure-SQL database in de grootschalige-servicelaag die wordt ondersteund door de grootschalige scale-out-opslag technologie. Een grootschalige-data base ondersteunt Maxi maal 100 TB aan gegevens en biedt hoge door Voer en prestaties, evenals snelle schaling om aan de werk belasting vereisten toe te passen. Schalen is transparant voor de toepassing: connectiviteit, verwerking van query's, enzovoort, werken als andere SQL database.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Welke resource typen en aankoop modellen ondersteunen grootschalige

De grootschalige is alleen beschikbaar voor afzonderlijke data bases met behulp van het op vCore gebaseerde aankoop model in Azure SQL Database.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Hoe verschilt de grootschalige-servicelaag van de Algemeen-en Bedrijfskritiek-service lagen

De vCore-gebaseerde service lagen worden voornamelijk onderscheiden op basis van de beschik baarheid, het opslag type en IOPs.

- De laag Algemeen is geschikt voor de meeste bedrijfs werkbelastingen, met een evenwichtige set reken-en opslag opties waarbij IO-latentie of failover tijden niet de prioriteit zijn.
- De grootschalige is geoptimaliseerd voor zeer grote database werkbelastingen.
- De servicelaag Bedrijfskritiek is geschikt voor zakelijke workloads waarbij IO-latentie een prioriteit is.

| | Resourcetype | Algemeen doel |  Grootschalig | Bedrijfskritiek |
|:---:|:---:|:---:|:---:|:---:|
| **Geschikt voor** |Alle|  De meeste zakelijke workloads. Biedt berekenings-en opslag opties voor budget gericht evenwicht. | Gegevens toepassingen met vereisten voor grote gegevens capaciteit en de mogelijkheid om opslag te schalen en de reken capaciteit vloeiender te schalen. | OLTP-toepassingen met een hoge transactie frequentie en een laagste latentie-IO. Biedt de hoogste flexibiliteit tot storingen met behulp van verschillende, geïsoleerde replica's.|
|  **Resourcetype** ||Eén data base/elastische pool/beheerd exemplaar | Individuele database | Eén data base/elastische pool/beheerd exemplaar |
| **Reken grootte**|Eén data base/elastische pool * | 1 tot 80 vCores | 1 tot 80 vCores * | 1 tot 80 vCores |
| |Beheerd exemplaar | 8, 16, 24, 32, 40, 64, 80 vCores | N/A | 8, 16, 24, 32, 40, 64, 80 vCores |
| **Opslagtype** | Alle |Premium externe opslag (per instantie) | Niet-gekoppelde opslag met lokale SSD-cache (per instantie) | Super snelle lokale SSD-opslag (per instantie) |
| **Opslag grootte** | Eén data base/elastische pool | 5 GB – 4 TB | Tot 100 TB | 5 GB – 4 TB |
| | Beheerd exemplaar  | 32 GB – 8 TB | N/A | 32 GB – 4 TB |
| **IO-door Voer** | Eén data base * * | 500 IOPS per vCore met 7000 maximum aantal IOPS | Grootschalige is een architectuur met meerdere lagen met caching op meerdere niveaus. Effectief IOPs is afhankelijk van de werk belasting. | 5000 IOPS met 200.000 maximum aantal IOPS|
| | Beheerd exemplaar | Is afhankelijk van de grootte van het bestand | N/A | Beheerd exemplaar: Is afhankelijk van de grootte van het bestand|
|**Beschikbaarheid**|Alle|1 replica, geen lees schaal, geen lokale cache | Meerdere replica's, Maxi maal 4 Lees bare, gedeeltelijke lokale cache | 3 replica's, 1 Lees bare, zone-redundante HA, volledige lokale cache |
|**Back-ups**|Alle|RA-GRS, 7-35 dagen (standaard 7 dagen)| RA-GRS, 7 dagen, constant tijdstip herstel (PITR) | RA-GRS, 7-35 dagen (standaard 7 dagen) |

\*Elastische Pools worden niet ondersteund in de grootschalige-servicelaag

### <a name="who-should-use-the-hyperscale-service-tier"></a>Wie moet de grootschalige-servicelaag gebruiken?

De grootschalige is voornamelijk bedoeld voor klanten die grote on-premises SQL Server data bases hebben en hun toepassingen willen moderniseren door over te stappen op de Cloud of voor klanten die al Azure SQL Database gebruiken en u aanzienlijk wilt uitbreiden de kans op database groei. Grootschalige is ook bedoeld voor klanten die zowel hoge prestaties als hoge schaal baarheid verzamelen. Met grootschalige krijgt u het volgende:

- Ondersteuning voor Maxi maal 100 TB aan database grootte
- Snelle database back-ups, ongeacht de grootte van de data base (back-ups zijn gebaseerd op moment opnamen van bestanden)
- Snel data base terugzetten, ongeacht de grootte van de data base (herstel bewerkingen zijn afkomstig van moment opnamen van bestanden)
- Een hogere doorvoer snelheid van het logboek leidt tot snelle doorvoer tijden voor trans acties, ongeacht de grootte van de data base
- Uitschalen naar een of meer knoop punten met het kenmerk alleen-lezen voor het offloaden van uw Lees werk belasting en voor hot standby.
- Snel omhoog schalen van reken kracht, in constant tijd, om de zware werk belasting krachtiger te maken en vervolgens omlaag te schalen, in constante tijd. Dit is vergelijkbaar met het omhoog en omlaag schalen van een P6 naar een P11, maar veel sneller, omdat dit geen grootte van de gegevens bewerking is.

### <a name="what-regions-currently-support-hyperscale"></a>Welke regio's ondersteunen momenteel grootschalige

De laag Azure SQL Database grootschalige is momenteel beschikbaar in de regio's onder [Azure SQL database overzicht van grootschalige](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Kan ik meerdere grootschalige-data bases per logische server maken

Ja. Zie [SQL database resource limieten voor één en gepoolde data bases op een logische server](sql-database-resource-limits-logical-server.md)voor meer informatie en limieten voor het aantal grootschalige-data bases per logische server.

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Wat zijn de prestatie kenmerken van een grootschalige-data base?

De architectuur van de SQL Database grootschalige biedt hoge prestaties en een maximale door Voer, terwijl grote database grootten worden ondersteund. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>Wat is de schaal baarheid van een grootschalige-data base

SQL Database grootschalige biedt een snelle schaal baarheid op basis van de vraag van uw werk belasting.

- **Omhoog/omlaag schalen**

  Met grootschalige kunt u de primaire Compute-grootte in termen van resources zoals CPU, geheugen en omlaag schalen, in constante tijd. Omdat de opslag wordt gedeeld, kunt u het formaat van de gegevens bewerking niet wijzigen.  
- **In-en uitschalen**

  Met grootschalige krijgt u ook de mogelijkheid om een of meer extra reken knooppunten in te richten die u kunt gebruiken om uw Lees aanvragen te verwerken. Dit betekent dat u deze extra reken knooppunten als alleen-lezen knoop punten kunt gebruiken om uw Lees werk belasting van de primaire Compute te offloaden. Naast alleen-lezen, fungeren deze knoop punten ook als hot stand-by in het geval van een failover van de primaire.

  Het inrichten van elk van deze extra reken knooppunten kan worden uitgevoerd in een constante tijd en is een online bewerking. U kunt verbinding maken met deze extra Compute-knoop punten met het `ApplicationIntent` kenmerk alleen-lezen door `readonly`het argument op uw Connection String in te stellen op. Alle verbindingen die zijn `readonly` gemarkeerd met, worden automatisch doorgestuurd naar een van de extra Compute-knoop punten met het kenmerk alleen-lezen.

## <a name="deep-dive-questions"></a>Diep gaande vragen

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Kan ik grootschalige en afzonderlijke data bases op één logische server combi neren

Ja, dat is mogelijk.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>Moet het programmeer model van de toepassing worden gewijzigd door grootschalige

Nee, het programmeer model van de toepassing blijft ongewijzigd. U kunt uw connection string zoals gebruikelijk en de andere normale modus gebruiken om te communiceren met uw Azure-SQL database.

### <a name="what-transaction-isolation-levels-are-going-to-be-default-on-sql-database-hyperscale-database"></a>Welke trans actie-isolatie niveaus standaard worden ingesteld voor SQL Database grootschalige-data base

Op het primaire knoop punt is het isolatie niveau van de trans actie RCSI (Lees doorgevoerde momentopname isolatie). Op de secundaire knoop punten Lees schaal is het isolatie niveau moment opname.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-sql-database-hyperscale"></a>Kan ik mijn on-premises of IaaS SQL Server licentie voor SQL Database grootschalige

Ja, [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) is beschikbaar voor grootschalige. Elke SQL Server Standard kern kan worden toegewezen aan 1 grootschalige vCores. Elke SQL Server Enterprise kern kan worden toegewezen aan 4 grootschalige vCores. U hebt geen SQL-licentie nodig voor secundaire replica's. De prijs van de Azure Hybrid Benefit wordt automatisch toegepast op de replica's met een lees bare (secundaire) grootte.

### <a name="what-kind-of-workloads-is-sql-database-hyperscale-designed-for"></a>Wat voor soort werk belastingen is SQL Database grootschalige ontworpen voor

SQL Database grootschalige ondersteunt alle SQL Server workloads, maar is voornamelijk geoptimaliseerd voor OLTP. U kunt ook Hybrid (HTAP) en analytische werk belastingen (Data Mart) opnemen.

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-sql-database-hyperscale"></a>Hoe kan ik kiezen tussen Azure SQL Data Warehouse en SQL Database grootschalige

Als u op dit moment interactieve Analytics-query's uitvoert met SQL Server als Data Warehouse, is SQL Database grootschalige een fantastische optie omdat u relatief kleine data warehouses kunt hosten (zoals enkele TB tot 10 aan TB) tegen lagere kosten en u uw gegevens kunt migreren arehouse-workload voor het SQL Database van grootschalige zonder T-SQL-code wijzigingen.

Als u gegevens analyse uitvoert op een grote schaal met complexe query's en gebruikmaakt van PDW (parallel data warehouse), Teradata of andere gegevens-data warehouses met een enorme parallelle processor (MPP), is SQL Data Warehouse mogelijk de beste keuze.
  
## <a name="sql-database-hyperscale-compute-questions"></a>Vragen over SQL Database grootschalige

### <a name="can-i-pause-my-compute-at-any-time"></a>Kan ik mijn Compute op elk gewenst moment onderbreken

Op dit moment kunt u uw reken kracht en aantal replica's echter omlaag schalen om de kosten te verlagen tijdens niet-piek tijden.

### <a name="can-i-provision-a-compute-with-extra-ram-for-my-memory-intensive-workload"></a>Kan ik een reken kracht met extra RAM inrichten voor mijn geheugenintensieve werk belasting

Nee. Als u meer RAM-geheugen wilt ontvangen, moet u een upgrade uitvoeren naar een hogere reken grootte. Zie [grootschalige Storage and Compute sizes](sql-database-vcore-resource-limits-single-databases.md#hyperscale-service-tier-for-provisioned-compute)(Engelstalig) voor meer informatie.

### <a name="can-i-provision-multiple-compute-nodes-of-different-sizes"></a>Kan ik meerdere reken knooppunten van verschillende grootten inrichten

Nee.

### <a name="how-many-read-scale-replicas-are-supported"></a>Hoeveel replica's van de Lees grootte worden ondersteund

De grootschalige-data bases worden standaard gemaakt met één Lees bare replica (twee replica's in totaal). U kunt het aantal alleen-lezen replica's tussen 0 en 4 schalen met behulp van de [Azure Portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [Power shell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) of [cli](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-nodes"></a>Voor hoge Beschik baarheid moet ik extra reken knooppunten inrichten

In grootschalige-data bases wordt de tolerantie op opslag niveau bepaald. U hebt slechts één replica nodig om tolerantie te bieden. Wanneer de compute replica niet actief is, wordt automatisch een nieuwe replica gemaakt zonder verlies van gegevens.

Als er echter maar één replica is, kan het enige tijd duren voordat de lokale cache in de nieuwe replica is gemaakt na een failover. Tijdens de fase van het opnieuw opbouwen in de cache worden gegevens rechtstreeks van de pagina servers opgehaald, wat resulteert in gedegradeerde IOPS en query prestaties.

Voor bedrijfskritische apps waarvoor hoge Beschik baarheid is vereist, moet u ten minste twee reken knooppunten inrichten, inclusief het primaire Compute-knoop punt (standaard). Op die manier is er een hot standby-stand beschikbaar in het geval van een failover.

## <a name="data-size-and-storage-questions"></a>Vragen over gegevens grootte en opslag

### <a name="what-is-the-max-db-size-supported-with-sql-database-hyperscale"></a>Wat is de maximale grootte van de data base die wordt ondersteund met SQL Database grootschalige

100 TB

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Wat is de grootte van het transactie logboek met grootschalige

Het transactie logboek met grootschalige is nagenoeg oneindig. U hoeft zich geen zorgen te maken over het uitvoeren van de logboek ruimte op een systeem met een hoge door Voer van het logboek. De frequentie van het genereren van het logboek kan echter worden beperkt voor continue agressieve workloads. De piek waarde voor het genereren van het aantal Logboeken is ongeveer 100 MB/sec.

### <a name="does-my-temp-db-scale-as-my-database-grows"></a>Wordt mijn data base geschaald naarmate mijn database groeit

Uw `tempdb` Data Base bevindt zich op lokale SSD-opslag en wordt geconfigureerd op basis van de reken grootte die u inricht. Uw `tempdb` is geoptimaliseerd en ingedeeld om maximale prestaties te bieden. De `tempdb` grootte kan niet worden geconfigureerd en wordt voor u beheerd door een opslag subsysteem.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-the-data-files"></a>Neemt de grootte van de data base automatisch toe of moet ik de grootte van de gegevens bestanden beheren

De grootte van de data base neemt automatisch toe tijdens het invoegen/opnemen van meer gegevens.

### <a name="what-is-the-smallest-database-size-that-sql-database-hyperscale-supports-or-starts-with"></a>Wat is de kleinste grootte van de data base die SQL Database grootschalige ondersteunt of begint met

10 GB

### <a name="in-what-increments-does-my-database-size-grow"></a>In welke stappen groeit de grootte van mijn data base

1 GB

### <a name="is-the-storage-in-sql-database-hyperscale-local-or-remote"></a>Is de opslag in SQL Database grootschalige lokaal of extern

In grootschalige worden gegevens bestanden opgeslagen in azure Standard-opslag. Gegevens worden intensief in de cache opgeslagen op lokale SSD-opslag, op computers die op de reken knooppunten worden gesloten. Daarnaast hebben reken knooppunten een cache op lokale SSD en in-Memory (buffer groep, enzovoort), om de frequentie van het ophalen van gegevens van externe knoop punten te verminderen.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Kan ik bestanden of bestands groepen beheren of definiëren met grootschalige

Nee
  
### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Kan ik een vaste limiet voor de gegevens groei van mijn data base inrichten

Nee

### <a name="how-are-data-files-laid-out-with-sql-database-hyperscale"></a>Hoe worden gegevens bestanden ingedeeld met SQL Database grootschalige

De gegevens bestanden worden beheerd door pagina servers. Wanneer de gegevens grootte groeit, worden gegevens bestanden en gekoppelde pagina-Server knooppunten toegevoegd.

### <a name="is-database-shrink-supported"></a>Wordt database verkleinen ondersteund

Nee

### <a name="is-database-compression-supported"></a>Wordt database compressie ondersteund

Ja

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Als ik een enorme tabel heb, worden mijn tabel gegevens verdeeld over meerdere gegevens bestanden

Ja. De gegevens pagina's die aan een bepaalde tabel zijn gekoppeld, kunnen eindigen op meerdere gegevens bestanden, die allemaal deel uitmaken van dezelfde bestands groep. SQL Server maakt gebruik van een [proportionele strategie](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) voor het distribueren van gegevens over gegevens bestanden.

## <a name="data-migration-questions"></a>Vragen over gegevens migratie

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Kan ik mijn bestaande Azure SQL-data bases verplaatsen naar de grootschalige-servicelaag

Ja. U kunt uw bestaande Azure SQL-data bases verplaatsen naar grootschalige. Dit is een eenrichtings migratie. U kunt geen data bases van grootschalige naar een andere servicelaag verplaatsen. U wordt aangeraden een kopie te maken van uw productie databases en te migreren naar grootschalige voor het controleren van concepten (POCs).
  
### <a name="can-i-move-my-hyperscale-databases-to-other-editions"></a>Kan ik mijn grootschalige-data bases verplaatsen naar andere versies

Nee. Op dit moment kunt u een grootschalige-data base niet naar een andere servicelaag verplaatsen.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Gaan er na de migratie naar de grootschalige-servicelaag enige functionaliteit of mogelijkheden verloren

Ja. Sommige Azure SQL Database-functies worden nog niet ondersteund in grootschalige, met inbegrip van maar niet beperktere back-ups van de lange termijn retentie. Nadat u uw data bases naar grootschalige hebt gemigreerd, werken deze functies niet meer.  We verwachten dat deze beperkingen tijdelijk zijn.

### <a name="can-i-move-my--on-premises-sql-server-database-or-my-sql-server-virtual-machine-database-to-hyperscale"></a>Kan ik mijn on-premises SQL Server Data Base of mijn SQL Server Virtual Machine-data base verplaatsen naar grootschalige

Ja. U kunt alle bestaande migratie technologieën gebruiken om te migreren naar grootschalige, met inbegrip van BACPAC, transactionele replicatie, het laden van logische gegevens. Zie ook de [Azure database Migration service](../dms/dms-overview.md).

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Wat is mijn downtime tijdens de migratie van een on-premises of virtuele-machine omgeving naar grootschalige en hoe kan ik deze minimaliseren

Uitval tijd is hetzelfde als de uitval tijd wanneer u uw data bases naar een enkele data base in Azure SQL Database migreert. U kunt transactionele replicatie [](replication-to-sql-database.md#data-migration-scenario
) gebruiken om de downtime te migreren voor data bases met een grootte van Maxi maal enkele TB. Voor zeer grote data bases (10 + TB) kunt u overwegen om gegevens te migreren met behulp van ADF, Spark of andere technologieën voor het verplaatsen van gegevens.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-sql-database-hyperscale"></a>Hoe lang het duurt om de X-hoeveelheid gegevens te SQL Database grootschalige

Grootschalige kan 100 MB/sec van nieuwe/gewijzigde gegevens gebruiken.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-and-sql-data-warehouse"></a>Kan ik gegevens lezen uit Blob Storage en snel laden (zoals poly base en SQL Data Warehouse)

U kunt gegevens lezen van Azure Storage en laden van gegevens in een grootschalige-data base (net zoals u kunt doen met een reguliere afzonderlijke data base). Poly Base wordt momenteel niet ondersteund op Azure SQL Database. U kunt poly Base uitvoeren met behulp van [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/) of een Spark-taak in [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) met de [Spark-connector voor SQL](sql-database-spark-connector.md). De Spark-connector naar SQL ondersteunt bulksgewijze invoeging.

Het eenvoudige herstel-of bulk logboek model wordt niet ondersteund in grootschalige. Een volledig herstel model is vereist voor een hoge Beschik baarheid. Grootschalige biedt echter een betere gegevens opname frequentie vergeleken met één Azure SQL database vanwege de nieuwe logboek architectuur.

### <a name="does-sql-database-hyperscale-allow-provisioning-multiple-nodes-for-ingesting-large-amounts-of-data"></a>Kan SQL Database grootschalige meerdere knoop punten inrichten voor het opnemen van grote hoeveel heden gegevens

Nee. SQL Database grootschalige is een SMP-architectuur en is geen asymmetrische Multi verwerking of een architectuur met meerdere masters. U kunt slechts meerdere replica's maken om alleen-lezen workloads uit te schalen.

### <a name="what-is-the-oldest-sql-server-version-will-sql-database-hyperscale-support-migration-from"></a>Wat is de oudste SQL Server versie SQL Database grootschalige ondersteuning voor migratie van

SQL Server 2005. Zie [migreren naar een enkele data base of een gegroepeerde Data Base](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database)voor meer informatie. Zie [compatibiliteits problemen met database migratie oplossen](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues)voor compatibiliteits problemen.

### <a name="does-sql-database-hyperscale-support-migration-from-other-data-sources-such-as-aurora-mysql-oracle-db2-and-other-database-platforms"></a>Biedt SQL Database grootschalige ondersteuning voor migratie van andere gegevens bronnen, zoals Aurora, MySQL, Oracle, DB2 en andere database platforms

Ja. Die afkomstig zijn van andere gegevens bronnen dan SQL Server vereist een logische migratie. U kunt de [Azure database Migration service](../dms/dms-overview.md) voor een logische migratie gebruiken.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Vragen over bedrijfs continuïteit en herstel na nood gevallen

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>De beschik bare SLA voor een grootschalige-data base

Met de standaard primaire plus 1 Lees bare secundaire, is de SLA 99,95% Beschik baarheid.  Met meer replica's, loopt de SLA Maxi maal 99,99%.  

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Worden de database back-ups die voor mij worden beheerd door de Azure SQL Database-Service

Ja

### <a name="how-often-are-the-database-backups-taken"></a>Hoe vaak de back-ups van de Data Base zijn gemaakt

Er zijn geen traditionele volledige, differentiële en logboek back-ups voor SQL Database grootschalige-data bases. In plaats daarvan worden er reguliere moment opnamen van de gegevens bestanden en het logboek dat wordt gegenereerd, bewaard als voor de retentie tijd die voor u is geconfigureerd of beschikbaar is.

### <a name="does-sql-database-hyperscale-support-point-in-time-restore"></a>SQL Database grootschalige-ondersteunings punt in tijd herstellen

Ja

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-with-backuprestore-in-sql-database-hyperscale"></a>Wat is het beoogde herstel punt (RPO)/Recovery Time doelstelling (RTO) met back-up/herstel in SQL Database grootschalige

De RPO is 0 min. Het doel van de RTO is minder dan 10 minuten, ongeacht de grootte van de data base. 

### <a name="do-backups-of-large-databases-affect-compute-performance-on-my-primary"></a>Back-ups van grote data bases invloed hebben op de reken prestaties op mijn primaire

Nee. Back-ups worden beheerd door het opslag subsysteem en maken gebruik van moment opnamen van bestanden. Ze hebben geen invloed op de werk belasting van de gebruiker op de primaire.

### <a name="can-i-perform-geo-restore-with-a-sql-database-hyperscale-database"></a>Kan ik geo-herstel uitvoeren met een SQL Database grootschalige-data base

Ja.  Geo-Restore wordt volledig ondersteund.

### <a name="can-i-setup-geo-replication-with-sql-database-hyperscale-database"></a>Kan ik geo-replicatie instellen met SQL Database grootschalige-data base

Momenteel niet.

### <a name="do-my-secondary-compute-nodes-get-geo-replicated-with-sql-database-hyperscale"></a>Krijgen mijn secundaire reken knooppunten geo-replicatie met SQL Database grootschalige

Momenteel niet.

### <a name="can-i-take-a-sql-database-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-sql-server-in-vm"></a>Kan ik een back-up van SQL Database grootschalige-data base maken en deze herstellen op mijn on-premises server of SQL Server in VM

Nee. De opslag indeling voor grootschalige-data bases wijkt af van de traditionele SQL Server en u kunt geen back-ups beheren of er toegang toe hebben. Als u uw gegevens uit een SQL Database grootschalige-Data Base wilt halen, gebruikt u de export service of gebruikt u scripting plus BCP.

## <a name="cross-feature-questions"></a>Vragen over meerdere functies

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Gaan er na de migratie naar de grootschalige-servicelaag enige functionaliteit of mogelijkheden verloren

Ja. Sommige Azure SQL Database-functies worden niet ondersteund in grootschalige, waaronder maar niet beperktere back-ups van de lange termijn retentie. Nadat u uw data bases naar grootschalige hebt gemigreerd, werken deze functies niet meer.

### <a name="will-polybase-work-with-sql-database-hyperscale"></a>Werkt poly Base met SQL Database grootschalige

Nee. Poly Base wordt niet ondersteund op Azure SQL Database.

### <a name="does-the-compute-have-support-for-r-and-python"></a>Biedt de compute ondersteuning voor R en python

Nee. R en python worden niet ondersteund in Azure SQL Database.

### <a name="are-the-compute-nodes-containerized"></a>Zijn de reken knooppunten container

Nee. Uw Data Base bevindt zich op een compute-VM en is geen container.

## <a name="performance-questions"></a>Vragen over prestaties

### <a name="how-much-throughput-can-i-push-on-the-largest-sql-database-hyperscale-compute"></a>Hoeveel door Voer kan ik pushen op de grootste SQL Database grootschalige compute

We hebben een consistent 100 MB/sec. wijzigings gegevens (transactielog gegevens genereren) gezien

### <a name="how-many-iops-do-i-get-on-the-largest-sql-database-hyperscale-compute"></a>Hoeveel IOPS krijg ik op de grootste SQL Database grootschalige compute

IOPS en IO-latentie variëren afhankelijk van de werkbelasting patronen.  Als de gegevens waartoe toegang moet worden verkregen, lokaal zijn voor de cache van de compute, zijn deze dezelfde i/o-patronen als de lokale SSD.   

### <a name="does-my-throughput-get-affected-by-backups"></a>Wordt mijn door Voer beïnvloed door back-ups

Nee. De berekening wordt losgekoppeld van de opslaglaag om te voor komen dat de berekening wordt beïnvloed.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-nodes"></a>Wordt de door Voer beïnvloed als ik extra reken knooppunten inricht

Omdat de opslag wordt gedeeld en er geen directe fysieke replicatie plaatsvindt tussen de primaire en secundaire reken knooppunten, wordt de door Voer op het primaire knoop punt technisch niet beïnvloed door het toevoegen van knoop punten met een lees grootte. We kunnen de continue agressieve werk belasting echter beperken om het aanmelden op secundaire knoop punten en pagina servers toe te staan, en om te voor komen dat er slechte Lees prestaties op secundaire knoop punten zijn.

## <a name="scalability-questions"></a>Vragen over schaal baarheid

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-node"></a>Hoe lang duurt het om een reken knooppunt omhoog en omlaag te schalen

Het schalen van de reken kracht naar boven of beneden duurt 5-10 minuten, ongeacht de grootte van de gegevens.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Is mijn data base offline terwijl de bewerking omhoog/omlaag schalen wordt uitgevoerd

Nee. U kunt omhoog en omlaag schalen om online te gaan.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Moet ik verwachten dat er verbinding wordt gemaakt wanneer de schaal bewerkingen worden uitgevoerd

Als u omhoog of omlaag schaalt, worden de bestaande verbindingen verwijderd wanneer er een failover wordt uitgevoerd naar het reken knooppunt met de doel grootte. Het toevoegen van Lees replica's leidt er niet toe dat de verbinding wordt verbroken.

### <a name="is-the-scaling-up-and-down-of-compute-nodes-automatic-or-end-user-triggered-operation"></a>Wordt het omhoog en omlaag schalen van reken knooppunten automatisch of door eind gebruikers geactiveerde bewerking

Eind gebruiker. Niet automatisch.  

### <a name="does-my-tempb-also-grow-as-the-compute-is-scaled-up"></a>Kan mijn `tempb` werk ook groeien terwijl de reken kracht omhoog wordt geschaald

Ja. Temp DB wordt automatisch geschaald als de berekening groeit.  

### <a name="can-i-provision-multiple-primary-compute-nodes-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Kan ik meerdere primaire reken knooppunten inrichten, zoals een systeem voor meerdere masters, waarbij meerdere primaire Compute Heads een hoger niveau van gelijktijdigheid kunnen hebben

Nee. Alleen het primaire Compute-knoop punt accepteert Lees-en schrijf aanvragen. Secundaire reken knooppunten accepteren alleen alleen-lezen aanvragen.

## <a name="read-scale-questions"></a>Vragen over schaal lezen

### <a name="how-many-secondary-compute-nodes-can-i-provision"></a>Hoeveel secundaire reken knooppunten kan ik inrichten

We maken standaard 2 replica's voor grootschalige-data bases. Als u het aantal replica's wilt aanpassen, kunt u dit doen met behulp van [Azure Portal](https://portal.azure.com).

### <a name="how-do-i-connect-to-these-secondary-compute-nodes"></a>Hoe kan ik verbinding maken met deze secundaire reken knooppunten

U kunt verbinding maken met deze extra Compute-knoop punten met het `ApplicationIntent` kenmerk alleen-lezen door `readonly`het argument op uw Connection String in te stellen op. Alle verbindingen die zijn `readonly` gemarkeerd met, worden automatisch doorgestuurd naar een van de extra Compute-knoop punten met het kenmerk alleen-lezen.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-node-using-ssms--other-client-tools"></a>Hoe kan ik valideren of ik verbinding heb gemaakt met een secundair reken knooppunt met behulp van SSMS/andere client hulpprogramma's?

U kunt de volgende T-SQL-query uitvoeren met SSMS/andere client hulpprogramma's `SELECT DATABASEPROPERTYEX ( '<database_name>' , 'updateability' )`:.
Het resultaat is `READ_ONLY` dat als u de verbinding naar het secundaire knoop punt met het kenmerk alleen `READ_WRITE` -lezen wijst of als uw verbinding naar het primaire knoop punt verwijst.

### <a name="can-i-create-a-dedicated-endpoint-for-the-read-scale-replica"></a>Kan ik een toegewezen eind punt voor de Lees bare replica maken

Nee. U kunt alleen verbinding maken met een replica met een Lees `ApplicationIntent=ReadOnly`grootte door op te geven.

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>Voert het systeem intelligente taak verdeling van de werk belasting lezen uit

Nee. De alleen-lezen workload wordt omgeleid naar een wille keurige, lees bare replica.

### <a name="can-i-scale-updown-the-secondary-compute-nodes-independently-of-the-primary-compute"></a>Kan ik de secundaire reken knooppunten onafhankelijk van de primaire Compute omhoog/omlaag schalen

Nee. De secundaire reken knooppunten worden ook gebruikt voor HA, zodat ze dezelfde configuratie moeten hebben als de primaire, in het geval van een failover.

### <a name="do-i-get-different-temp-db-sizing-for-my-primary-compute-and-my-additional-secondary-compute-nodes"></a>Krijg ik verschillende Temp-DB-grootte voor mijn primaire Compute en mijn extra secundaire reken knooppunten

Nee. Uw `tempdb` is geconfigureerd op basis van de inrichting van de berekenings grootte, uw secundaire reken knooppunten hebben dezelfde grootte als de primaire compute.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-nodes"></a>Kan ik indexen en weer gaven toevoegen op mijn secundaire reken knooppunten

Nee. Grootschalige-data bases hebben gedeelde opslag, wat betekent dat alle reken knooppunten dezelfde tabellen, indexen en weer gaven zien. Als u aanvullende indexen wilt optimaliseren voor lees bewerkingen, moet u ze eerst toevoegen op de primaire.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-node"></a>Hoeveel vertraging er tussen het primaire en secundaire reken knooppunt ligt

Vanaf het moment dat een trans actie wordt doorgevoerd op de primaire,, afhankelijk van de frequentie van het genereren van het logboek, kan deze onmiddellijk of in een laag aantal milliseconden zijn.

## <a name="next-steps"></a>Volgende stappen

Zie [grootschalige service tier](sql-database-service-tier-hyperscale.md)(Engelstalig) voor meer informatie over de grootschalige.
