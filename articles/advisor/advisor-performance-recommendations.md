---
title: Prestaties van Azure-toepassingen verbeteren met Azure Advisor
description: Gebruik Advisor om de prestaties van uw Azure-implementaties te optimaliseren.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: 405ec395feeb33b8511b9b915151b2ed9503c371
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443056"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Prestaties van Azure-toepassingen verbeteren met Azure Advisor

Prestatieaanbevelingen van Azure Advisor helpen de snelheid en responsiviteit van uw bedrijfskritieke toepassingen te verbeteren. U prestatieaanbevelingen van Advisor krijgen op het tabblad **Prestaties** van het Dashboard Adviseur.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>Verminder de DNS-tijd om te leven op uw Traffic Manager-profiel om sneller naar gezonde eindpunten te gaan

[Met de Instellingen van Time to Live (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) in uw Traffic Manager-profiel u opgeven hoe snel u eindpunten wijzigen als een bepaald eindpunt niet meer reageert op query's. Het verlagen van de TTL-waarden betekent dat klanten sneller worden doorgestuurd naar goed functionerende eindpunten.

Azure Advisor identificeert Traffic Manager-profielen met een langere TTL-geconfigureerd en raadt aan de TTL te configureren voor 20 seconden of 60 seconden, afhankelijk van of het profiel is geconfigureerd voor [Fast Failover.](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/)

## <a name="improve-database-performance-with-sql-db-advisor"></a>Databaseprestaties verbeteren met SQL DB Advisor

Advisor biedt u een consistente, geconsolideerde weergave van aanbevelingen voor al uw Azure-resources. Het integreert met SQL Database Advisor om u aanbevelingen te geven voor het verbeteren van de prestaties van uw SQL Azure-database.SQL Database Advisor beoordeelt de prestaties van uw SQL Azure-databases door uw gebruiksgeschiedenis te analyseren. Het biedt vervolgens aanbevelingen die het meest geschikt zijn voor het uitvoeren van de typische werkbelasting van de database.

> [!NOTE]
> Om aanbevelingen te krijgen, moet een database ongeveer een week gebruik hebben, en binnen die week moet er een consistente activiteit zijn. SQL Database Advisor kan gemakkelijker optimaliseren voor consistente querypatronen dan voor willekeurige uitbarstingen van activiteit.

Zie SQL Database Advisor voor meer informatie over SQL Database [Advisor.](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)

## <a name="improve-app-service-performance-and-reliability"></a>Verbeter de prestaties en betrouwbaarheid van appservice

Azure Advisor integreert aanbevelingen voor best practices voor het verbeteren van uw App Services-ervaring en het ontdekken van relevante platformmogelijkheden. Voorbeelden van aanbevelingen voor App Services zijn:
* Detectie van instanties waarin geheugen- of CPU-resources worden uitgeput door app-runtimes met mitigatieopties.
* Detectie van instanties waarbij het verzamelen van bronnen zoals web-apps en databases de prestaties kan verbeteren en lagere kosten kunnen verlagen.

Zie Aanbevolen procedures voor Azure App Service voor meer informatie over [aanbevelingen voor](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)App Services.

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Beheerde schijven gebruiken om i/o-beperking van de schijf te voorkomen

Advisor identificeert virtuele machines die behoren tot een opslagaccount dat zijn schaalbaarheidsdoel bereikt. Deze voorwaarde maakt die VM's gevoelig voor I/O throttling. Advisor raadt aan managed disks te gebruiken om prestatiedegradatie te voorkomen.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Verbeter de prestaties en betrouwbaarheid van virtuele machineschijven met Premium Storage

Advisor identificeert virtuele machines met standaardschijven met een hoog transactievolume op uw opslagaccount en raadt aan om te upgraden naar premium schijven. 

Azure Premium Storage biedt krachtige schijfondersteuning met lage latentie voor virtuele machines die I/O-intensieve workloads uitvoeren. Virtuele machineschijven die gebruikmaken van premium opslagaccounts slaan gegevens op solid-state drives (SSD's). Voor de beste prestaties voor uw toepassing raden we u aan om virtuele machineschijven te migreren waarvoor een hoge IOPS nodig is voor premium opslag.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Gegevensscheefheid in uw SQL-gegevensmagazijntabel verwijderen om de queryprestaties te verhogen

Het scheeftrekken van gegevens kan leiden tot onnodige gegevensverplaatsing of knelpunten in de bron bij het uitvoeren van uw werkbelasting. Advisor detecteert de spreiding van distributiegegevens groter dan 15% en raadt u aan uw gegevens opnieuw te distribueren en uw tabeldistributiesleutelselecties opnieuw te bekijken. Zie [Probleemoplossing voor het](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice)oplossen van problemen voor meer informatie over het identificeren en verwijderen van scheeftrekken.

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Verouderde tabelstatistieken maken of bijwerken op uw SQL-gegevensmagazijntabel om de queryprestaties te verhogen

Advisor identificeert tabellen die geen up-to-date [tabelstatistieken](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) hebben en raadt aan tabelstatistieken te maken of bij te werken. De SQL-queryoptimizer voor gegevensmagazijn gebruikt up-to-date statica om de kardinaliteit of het aantal rijen in het queryresultaat te schatten waarmee de queryoptimizer een queryplan van hoge kwaliteit kan maken voor de snelste prestaties.

## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Opschalen om het cachegebruik op uw SQL Data Warehouse-tabellen te optimaliseren om de queryprestaties te verbeteren

Azure Advisor detecteert of uw SQL Data Warehouse een hoog cachegebruikt percentage en een laag hitpercentage heeft. Deze voorwaarde duidt op een hoge cacheuitzetting, wat van invloed kan zijn op de prestaties van uw SQL Data Warehouse. Advisor stelt voor dat u uw SQL Data Warehouse opschaalt om ervoor te zorgen dat u voldoende cachecapaciteit toewijst voor uw workload.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>SQL Data Warehouse-tabellen converteren naar gerepliceerde tabellen om de queryprestaties te verhogen

Advisor identificeert tabellen die geen gerepliceerde tabellen zijn, maar profiteren van het converteren en stelt voor dat u deze tabellen converteert. Aanbevelingen zijn gebaseerd op de gerepliceerde tabelgrootte, het aantal kolommen, het type tabeldistributie en het aantal partities van de SQL Data Warehouse-tabel. Aanvullende heuristiek kan worden verstrekt in de aanbeveling voor context. Zie [SQL Data Warehouse Recommendations](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables)voor meer informatie over de manier waarop deze aanbeveling wordt bepaald. 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migreer uw opslagaccount naar Azure Resource Manager om alle nieuwste Azure-functies te krijgen

Migreer uw implementatiemodel voor opslagaccount naar Azure Resource Manager (Resource Manager) om te profiteren van sjabloonimplementaties, extra beveiligingsopties en de mogelijkheid om te upgraden naar een GPv2-account voor het gebruik van de nieuwste functies van Azure Storage. Advisor identificeert alle zelfstandige opslagaccounts die het klassieke implementatiemodel gebruiken en raadt aan te migreren naar het implementatiemodel van Resource Manager.

> [!NOTE]
> Klassieke waarschuwingen in Azure Monitor zijn in augustus 2019 ingetrokken. We raden u aan uw klassieke opslagaccount te upgraden om Resource Manager te gebruiken om de waarschuwingsfunctionaliteit met het nieuwe platform te behouden. Zie [Classic Alerts Retirement voor](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform)meer informatie.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Ontwerp uw opslagaccounts om te voorkomen dat u de maximale abonnementslimiet bereikt

Een Azure-regio kan maximaal 250 opslagaccounts per abonnement ondersteunen. Zodra de limiet is bereikt, u geen opslagaccounts meer maken in die regio/abonnementscombinatie. Advisor controleert uw abonnementen en oppervlakteaanbevelingen voor u om te ontwerpen voor minder opslagaccounts voor een account die dicht bij het bereiken van de maximale limiet ligt.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>De prestaties van uw Azure MySQL-, Azure PostgreSQL- en Azure MariaDB-servers optimaliseren 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>De CPU-druk van uw Azure MySQL-, Azure PostgreSQL- en Azure MariaDB-servers oplossen met CPU-knelpunten
Een zeer hoog gebruik van de CPU over een langere periode kan leiden tot trage queryprestaties voor uw werkbelasting. Het vergroten van de CPU-grootte zal helpen bij het optimaliseren van de runtime van de databasequery's en het verbeteren van de algehele prestaties. Azure Advisor identificeert servers met een hoog CPU-gebruik waarmee waarschijnlijk cpu-beperkte workloads worden uitgevoerd en raadt aan om uw rekenkracht te schalen.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Verminder geheugenbeperkingen op uw Azure MySQL-, Azure PostgreSQL- en Azure MariaDB-servers of ga naar een geheugengeoptimaliseerde SKU
Een lage hitratio in de cache kan leiden tot tragere queryprestaties en een hogere IOPS. Dit kan te wijten zijn aan een slecht queryplan of het uitvoeren van een geheugenintensieve werkbelasting. Door het queryplan op te lossen of het geheugen van de Azure Database voor PostgreSQL-databaseserver, Azure MySQL-databaseserver of Azure MariaDB-server te [verhogen,](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) u de uitvoering van de databasewerkbelasting optimaliseren. Azure Advisor identificeert servers die zijn getroffen als gevolg van deze hoge bufferpoolchurn en raadt aan om het queryplan op te lossen, over te gaan naar een hogere SKU met meer geheugen of de opslaggrootte te vergroten om meer IOPS te krijgen.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Gebruik een Azure MySQL of Azure PostgreSQL Read Replica om reads uit te schalen voor leesintensieve workloads
Azure Advisor maakt gebruik van op workloads gebaseerde heuristiek, zoals de verhouding tussen reads en schrijft op de server in de afgelopen zeven dagen om leesintensieve workloads te identificeren. Uw Azure-database voor PostgreSQL-bron of Azure-database voor MySQL-bron met een zeer hoge lees-/schrijfverhouding kan leiden tot CPU- en/of geheugenconflicten die leiden tot trage queryprestaties. Het toevoegen van een [replica](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) helpt bij het uitschalen van reads naar de replicaserver, waardoor CPU- en/of geheugenbeperkingen op de primaire server worden voorkomen. Advisor identificeert servers met zulke hoge leesintensieve workloads en raadt aan een [leesreplica](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas) toe te voegen om een deel van de leesworkloads te ontladen.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Uw Azure MySQL-, Azure PostgreSQL- of Azure MariaDB-server schalen naar een hogere SKU om verbindingsbeperkingen te voorkomen
Elke nieuwe verbinding met uw databaseserver neemt wat geheugen in beslag. De prestaties van de databaseserver degradeeren als verbindingen met uw server mislukken vanwege een [bovengrens](https://docs.microsoft.com/azure/postgresql/concepts-limits) in het geheugen. Azure Advisor identificeert servers met veel verbindingsfouten en raadt aan de verbindingslimieten van uw server te upgraden om meer geheugen naar uw server te bieden door compute op te schalen of geheugengeoptimaliseerde SKU's te gebruiken, die meer rekenkracht per core hebben.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Uw cache schalen naar een andere grootte of SKU om de cache- en toepassingsprestaties te verbeteren

Cache-exemplaren presteren het best wanneer ze niet worden uitgevoerd onder hoge geheugendruk, hoge serverbelasting of hoge netwerkbandbreedte, waardoor ze niet meer reageren, gegevensverlies ervaren of niet meer beschikbaar zijn. Advisor identificeert cache-exemplaren in deze omstandigheden en raadt aan om best practices toe te passen om de geheugendruk, serverbelasting of netwerkbandbreedte te verminderen of te schalen naar een andere grootte of SKU met meer capaciteit.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Regio's met verkeer toevoegen aan uw Azure Cosmos DB-account

Advisor detecteert Azure Cosmos DB-accounts met verkeer uit een regio die momenteel niet is geconfigureerd en raadt aan dat gebied toe te voegen. Dit zal de latentie voor aanvragen uit die regio verbeteren en zorgen voor beschikbaarheid in geval van regio-uitval. [Meer informatie over wereldwijde gegevensdistributie met Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Uw Azure Cosmos DB-indexeringsbeleid configureren met door de klant opgenomen of uitgesloten paden

Azure Advisor identificeert Cosmos DB-containers die het standaard indexeringsbeleid gebruiken, maar kunnen profiteren van een aangepast indexeringsbeleid op basis van het werkbelastingspatroon. Het standaardindexeringsbeleid indexeert alle eigenschappen, maar het gebruik van een aangepast indexeringsbeleid met expliciete opgenomen of uitgesloten paden die worden gebruikt in queryfilters, kan de RU's en de opslag die wordt verbruikt voor indexering verminderen. [Meer informatie over het wijzigen van indexbeleid](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Azure Cosmos DB-querypaginaformaat (MaxItemCount) instellen op -1 

Azure Advisor identificeert Azure Cosmos DB-containers die de querypaginagrootte van 100 gebruiken en raadt aan een paginagrootte van -1 te gebruiken voor snellere scans. [Meer informatie over het aantal objecten voor maximaal objecten](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Toegang tot prestatieaanbevelingen in Adviseur

1. Meld u aan bij de [Azure-portal](https://portal.azure.com)en open [Vervolgens Advisor](https://aka.ms/azureadvisordashboard).

2.  Klik op het dashboard Van Advisor op het tabblad **Prestaties.**

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over aanbevelingen van Advisor:

* [Inleiding tot Adviseur](advisor-overview.md)
* [Aan de slag met Advisor](advisor-get-started.md)
* [Aanbevelingen voor kosten van adviseur](advisor-cost-recommendations.md)
* [Aanbevelingen voor hoge beschikbaarheid](advisor-high-availability-recommendations.md)
* [Aanbevelingen voor beveiliging van adviseur](advisor-security-recommendations.md)
* [Aanbevelingen adviseur Operational Excellence](advisor-operational-excellence-recommendations.md)
