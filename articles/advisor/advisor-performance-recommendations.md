---
title: Verbeter de prestaties van Azure-toepassingen met Azure Advisor
description: Gebruik Advisor om de prestaties van uw Azure-implementaties te optimaliseren.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: d0b309fd35fa0a78685017e25eea0caf3f97da03
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84658409"
---
# <a name="improve-performance-of-azure-applications-with-azure-advisor"></a>Verbeter de prestaties van Azure-toepassingen met Azure Advisor

Azure Advisor prestatie aanbevelingen helpen de snelheid en reactie tijd van uw bedrijfskritische toepassingen te verbeteren. U kunt de prestatie aanbevelingen van Advisor op het tabblad **prestaties** van het Advisor-dash board ontvangen.

## <a name="reduce-dns-time-to-live-on-your-traffic-manager-profile-to-fail-over-to-healthy-endpoints-faster"></a>U kunt de DNS-tijd op uw Traffic Manager-profiel verlagen zodat er sneller een failover wordt uitgevoerd naar goede eind punten

Met de [instellingen voor time to Live (TTL)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-performance-considerations) in uw Traffic Manager-profiel kunt u opgeven hoe snel eind punten moeten worden geswitcheerd als een bepaald eind punt niet meer reageert op query's. Het verminderen van de TTL-waarden betekent dat clients sneller naar werkende eind punten worden doorgestuurd.

Azure Advisor identificeert Traffic Manager profielen met een meer TTL-waarde die is geconfigureerd en raadt u aan de TTL te configureren op 20 seconden of 60 seconden, afhankelijk van het feit of het profiel is geconfigureerd voor [snelle failover](https://azure.microsoft.com/roadmap/fast-failover-and-tcp-probing-in-azure-traffic-manager/).

## <a name="improve-database-performance-with-sql-db-advisor"></a>Databaseprestaties verbeteren met SQL DB Advisor

Advisor biedt een consistente, geconsolideerde weer gave van aanbevelingen voor al uw Azure-resources. Het integreert met SQL Database Advisor om u aanbevelingen te doen voor het verbeteren van de prestaties van uw data base.SQL Database Advisor evalueert de prestaties van uw data bases door de gebruiks geschiedenis te analyseren. Vervolgens worden aanbevelingen geboden die het meest geschikt zijn voor het uitvoeren van de normale werk belasting van de data base.

> [!NOTE]
> Voor het verkrijgen van aanbevelingen moet een Data Base over een week gebruik beschikken en binnen die week een consistente activiteit moeten zijn. SQL Database Advisor kan gemakkelijker worden geoptimaliseerd voor consistente query patronen dan voor wille keurige bursts van de activiteit.

Zie [SQL database Advisor](https://azure.microsoft.com/documentation/articles/sql-database-advisor/)voor meer informatie over SQL database Advisor.

## <a name="upgrade-your-storage-client-library-to-the-latest-version-for-better-reliability-and-performance"></a>Upgrade uw Storage-clientbibliotheek naar de nieuwste versie voor betere betrouwbaarheid en prestaties

De nieuwste versie van Storage-clientbibliotheek/SDK bevat oplossingen voor fouten die door klanten zijn gemeld en proactief zijn geïdentificeerd via onze QA-procedure. De nieuwste versie is ook geoptimaliseerd voor betrouwbaarheid en prestaties en bevat nieuwe functies die uw algemene ervaring met Azure Storage verbeteren. Advisor biedt u aanbevelingen en stappen om een upgrade uit te voeren naar de meest recente versie van SDK als u een verouderde versie hebt. De aanbevelingen zijn voor ondersteunde talen: C++ en .net.

## <a name="improve-app-service-performance-and-reliability"></a>De prestaties en betrouw baarheid van App Service verbeteren

Azure Advisor integreert aanbevelingen voor aanbevolen procedures voor het verbeteren van uw App Services ervaring en het detecteren van relevante platform mogelijkheden. Voor beelden van App Services aanbevelingen zijn:
* Detectie van exemplaren waarbij geheugen-of CPU-bronnen worden uitgeput door app-Runtimes met beperkende opties.
* Detectie van exemplaren waarbij collocating resources, zoals web-apps en data bases, de prestaties en lagere kosten kunnen verbeteren.

Zie [Aanbevolen procedures voor Azure app service](https://azure.microsoft.com/documentation/articles/app-service-best-practices/)voor meer informatie over app Services aanbevelingen.

## <a name="use-managed-disks-to-prevent-disk-io-throttling"></a>Managed Disks gebruiken om schijf-I/O-beperking te voor komen

Advisor identificeert virtuele machines die deel uitmaken van een opslag account dat het schaalbaarheids doel bereikt. Deze voor waarde zorgt ervoor dat de Vm's vatbaar zijn voor I/O-beperking. Advisor wordt aangeraden Managed Disks te gebruiken om prestaties te verslechteren.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks-by-using-premium-storage"></a>Verbeter de prestaties en betrouw baarheid van virtuele-machine schijven met behulp van Premium Storage

Advisor identificeert virtuele machines met standaard schijven met een groot aantal trans acties op uw opslag account en raadt u aan om een upgrade uit te brengen naar Premium-schijven. 

Azure Premium Storage biedt schijf ondersteuning met hoge prestaties en lage latentie voor virtuele machines die I/O-intensieve workloads uitvoeren. Voor virtuele-machine schijven die gebruikmaken van Premium Storage-accounts, worden gegevens opgeslagen op Solid-state drives (Ssd's). Voor de beste prestaties van uw toepassing raden wij aan dat u alle schijven voor virtuele machines die hoge IOPS vereisen voor Premium-opslag hebt gemigreerd.

## <a name="remove-data-skew-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Gegevens scheefheid van uw SQL Data Warehouse-tabel verwijderen om de query prestaties te verbeteren

Gegevens scheefheid kan leiden tot onnodige gegevens verplaatsing of bron knelpunten wanneer uw werk belasting wordt uitgevoerd. Advisor detecteert distributie gegevens die groter zijn dan 15% en u wordt aangeraden uw gegevens opnieuw te distribueren en uw tabel distributie sleutel te bekijken. Zie [problemen met scheef trekken oplossen](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice)voor meer informatie over het identificeren en verwijderen van scheefheid.

## <a name="create-or-update-outdated-table-statistics-on-your-sql-data-warehouse-table-to-increase-query-performance"></a>Verouderde tabel statistieken in uw SQL Data Warehouse-tabel maken of bijwerken om de query prestaties te verbeteren

Advisor identificeert tabellen die geen actuele [tabel statistieken](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics) hebben en raadt u aan om tabel statistieken te maken of bij te werken. De SQL Data Warehouse-query optimalisatie maakt gebruik van up-to-date statische gegevens om de kardinaliteit of het aantal rijen in het query resultaat te schatten waarmee de query Optimizer een query plan van hoge kwaliteit kan maken voor de snelste prestaties.

## <a name="improve-mysql-connection-management"></a>MySQL-verbindingsbeheer verbeteren

Met Advisor-analyse wordt aangegeven dat de toepassing die verbinding maakt met MySQL-server mogelijk niet efficiënt beheert. Dit kan leiden tot onnodig resourceverbruik en een algeheel hogere toepassingsvertraging. We raden u aan om het aantal kortdurende verbindingen te beperken en onnodige niet-actieve verbindingen te elimineren om verbindingsbeheer te verbeteren. U kunt dit doen door aan de serverzijde een verbindingspooler, zoals ProxySQL, te configureren.


## <a name="scale-up-to-optimize-cache-utilization-on-your-sql-data-warehouse-tables-to-increase-query-performance"></a>Schaal omhoog om het cache gebruik op uw SQL Data Warehouse tabellen te optimaliseren om de query prestaties te verbeteren

Azure Advisor detecteert of uw SQL Data Warehouse een hoog percentage voor cache gebruik heeft en een lage treffer percentage. Deze voor waarde duidt op hoge cache verwijdering, wat van invloed kan zijn op de prestaties van uw SQL Data Warehouse. Advisor raadt u aan om uw SQL Data Warehouse te verg Roten om ervoor te zorgen dat u voldoende cache capaciteit toewijst voor uw werk belasting.

## <a name="convert-sql-data-warehouse-tables-to-replicated-tables-to-increase-query-performance"></a>SQL Data Warehouse tabellen converteren naar gerepliceerde tabellen om de query prestaties te verbeteren

Advisor identificeert tabellen die geen gerepliceerde tabellen zijn, maar die wel van nut zouden zijn voor conversie en suggesties voor het converteren van deze tabellen. Aanbevelingen zijn gebaseerd op de grootte van de gerepliceerde tabel, het aantal kolommen, het distributie type van de tabel en het aantal partities van de SQL Data Warehouse tabel. Aanvullende heuristiek kan worden gegeven in de aanbeveling voor context. Zie [SQL Data Warehouse aanbevelingen](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-concept-recommendations#replicate-tables)voor meer informatie over hoe deze aanbeveling wordt bepaald. 

## <a name="migrate-your-storage-account-to-azure-resource-manager-to-get-all-of-the-latest-azure-features"></a>Migreer uw opslag account naar Azure Resource Manager om alle nieuwste functies van Azure te verkrijgen

Migreer het implementatie model van uw opslag account naar Azure Resource Manager (Resource Manager) om te profiteren van sjabloon implementaties, extra beveiligings opties en de mogelijkheid om een upgrade uit te voeren naar een GPv2-account voor gebruik van de nieuwste functies van Azure Storage. Advisor identificeert zelfstandige opslag accounts die gebruikmaken van het klassieke implementatie model en raadt u aan om te migreren naar het Resource Manager-implementatie model.

> [!NOTE]
> Klassieke waarschuwingen in Azure Monitor zijn in augustus 2019 buiten gebruik gesteld. We raden u aan uw klassieke opslag account bij te werken om Resource Manager te gebruiken om de functionaliteit van waarschuwingen met het nieuwe platform te behouden. Zie [klassieke waarschuwingen buiten](https://docs.microsoft.com/azure/azure-monitor/platform/monitoring-classic-retirement#retirement-of-classic-monitoring-and-alerting-platform)gebruik stellen voor meer informatie.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Uw opslag accounts ontwerpen om te voor komen dat de maximale abonnements limiet wordt bereikt

Een Azure-regio kan Maxi maal 250 opslag accounts per abonnement ondersteunen. Zodra de limiet is bereikt, kunt u geen opslag accounts meer maken in deze combi natie van regio en abonnement. Advisor controleert uw abonnementen en aanbevelingen voor het Opper vlak zodat u kunt ontwerpen voor minder opslag accounts voor elk wat bijna de maximale limiet bereikt.

## <a name="consider-increasing-the-size-of-your-vnet-gateway-sku-to-adress-high-p2s-use"></a>Overweeg de omvang van uw VNet-gateway-SKU te verg Roten naar het gebruik van een hoog P2S

Elke gateway-SKU kan slechts een beperkt aantal gelijktijdige P2S-verbindingen ondersteunen. Als uw verbinding tot stand is gebracht, wordt de limiet voor het aantal verbindingen mogelijk verbroken. Door de grootte van uw gateway te verg Roten, kunt u meer gelijktijdige P2S-gebruikers ondersteunen. Advisor biedt aanbevelingen en stappen om dit te doen.

## <a name="consider-increasing-the-size-of-your-vnet-gateway-sku-to-address-high-cpu"></a>Overweeg om de gateway-SKU van uw virtuele netwerk te vergroten om het hoge CPU-gebruik te verhelpen

Bij een hoge verkeersbelasting kan de VPN-gateway pakketten verwijderen vanwege een hoge CPU. U kunt overwegen om uw VPN Gateway-SKU bij te werken, omdat uw VPN op consistente wijze actief is geweest. Door de grootte van uw VPN-gateway te verg Roten, zorgt u ervoor dat de verbindingen niet worden verbroken vanwege een hoge CPU. Advisor provdes-aanbeveling om dit probleem proactief op te lossen. 

## <a name="increase-batch-size-when-loading-to-maximize-load-throughput-data-compression-and-query-performance"></a>Batchgrootte vergroten tijdens het laden om de laaddoorvoer, gegevenscompressie en queryprestaties te maximaliseren

Met Advisor kan worden gedetecteerd dat u de belasting prestaties en door Voer kunt verhogen door de Batch grootte te verhogen bij het laden in uw data base. U kunt overwegen de instructie COPY te gebruiken. Als u de instructie COPY niet kunt gebruiken, kunt u overwegen de batchgrootte te vergroten bij het gebruik van laadhulpprogramma's zoals de SQLBulkCopy-API of BCP. Een goede vuistregel is een batchgrootte tussen 100.000 en 1 miljoen rijen. Dit neemt de belasting door Voer, gegevens compressie en query prestaties.

## <a name="co-locate-the-storage-account-within-the-same-region-to-minimize-latency-when-loading"></a>Plaats het opslagaccount in dezelfde regio om de latentie tijdens het laden te minimaliseren

Met Advisor kan worden gedetecteerd dat u vanuit een andere regio dan uw SQL-groep wordt geladen. Overweeg om een opslagaccount te gebruiken dat zich in dezelfde regio bevindt als uw SQL-pool om de latentie tijdens het laden van gegevens te minimaliseren. Dit helpt latentie te minimaliseren en de belasting prestaties te verg Roten.

## <a name="unsupported-kubernetes-version-is-detected"></a>Er is een niet-ondersteunde Kubernetes-versie gedetecteerd

Advisor kan detecteren of er een niet-ondersteunde Kubernetes-versie is gedetecteerd. De aanbeveling helpt ervoor te zorgen dat Kubernetes-cluster wordt uitgevoerd met een ondersteunde versie.

## <a name="optimize-the-performance-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers"></a>Optimaliseer de prestaties van uw Azure MySQL-, Azure PostgreSQL-en Azure MariaDB-servers 

### <a name="fix-the-cpu-pressure-of-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-with-cpu-bottlenecks"></a>De CPU-druk van uw Azure MySQL-, Azure PostgreSQL-en Azure MariaDB-servers met CPU-knel punten oplossen
Een zeer hoog gebruik van de CPU gedurende een langere periode kan de prestaties van uw werk belasting vertragen. Het verg Roten van de CPU-grootte helpt de runtime van de database query's te optimaliseren en de algehele prestaties te verbeteren. Azure Advisor herkent servers met een hoog CPU-gebruik waarvoor waarschijnlijk CPU-beperkte workloads worden uitgevoerd en wordt u aangeraden om uw reken kracht te schalen.

### <a name="reduce-memory-constraints-on-your-azure-mysql-azure-postgresql-and-azure-mariadb-servers-or-move-to-a-memory-optimized-sku"></a>Verminder de geheugen beperkingen op uw Azure MySQL-, Azure PostgreSQL-en Azure MariaDB-servers of ga naar een voor geheugen geoptimaliseerde SKU
Een lage cache verhouding kan leiden tot tragere query prestaties en meer IOPS. Dit kan worden veroorzaakt door een beschadigd query plan of door een geheugenintensieve werk belasting uit te voeren. Het herstellen van het query plan of [het verg Roten](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers) van het geheugen van de Azure database for PostgreSQL database server, Azure MySQL-database server of Azure MariaDB-server helpt de uitvoering van de werk belasting van de data base te optimaliseren. Azure Advisor identificeert servers die worden beïnvloed door deze hoge verloop tijd van de buffer groep en raadt u aan om het query plan te corrigeren, over te stappen op een hogere SKU met meer geheugen of de opslag grootte te verg Roten om meer IOPS te verkrijgen.

### <a name="use-a-azure-mysql-or-azure-postgresql-read-replica-to-scale-out-reads-for-read-intensive-workloads"></a>Een Azure MySQL-of Azure PostgreSQL-Lees replica gebruiken om Lees bewerkingen uit te schalen voor intensieve werk belastingen voor lezen
Azure Advisor maakt gebruik van heuristiek op basis van werk belastingen, zoals de verhouding van Lees bewerkingen op de server gedurende de afgelopen zeven dagen om Lees-intensieve workloads te identificeren. Uw Azure data base for PostgreSQL-resource of Azure data base for MySQL-resource met een zeer hoge lees-en schrijf bewerkingen kan resulteren in CPU-en/of geheugen conflicten die leiden tot trage query prestaties. Het toevoegen van een [replica](https://docs.microsoft.com/azure/postgresql/howto-read-replicas-portal) helpt bij het uitschalen van Lees bewerkingen naar de replica server, waardoor er geen CPU-en/of geheugen beperkingen zijn op de primaire server. Advisor identificeert servers met zulke hoge lees-intensieve workloads en raadt aan een [Lees replica](https://docs.microsoft.com/azure/postgresql/concepts-read-replicas)toe te voegen   om een aantal lees workloads te offloaden.


### <a name="scale-your-azure-mysql-azure-postgresql-or-azure-mariadb-server-to-a-higher-sku-to-prevent-connection-constraints"></a>Schaal uw Azure MySQL-, Azure PostgreSQL-of Azure MariaDB-server naar een hogere SKU om verbindings beperkingen te voor komen
Elke nieuwe verbinding met uw database server neemt geheugen in beslag. De prestaties van de database server verslechteren als verbindingen met uw server mislukken vanwege een [bovenlimiet](https://docs.microsoft.com/azure/postgresql/concepts-limits) in het geheugen. Azure Advisor identificeert servers met veel verbindings fouten en raadt u aan om de verbindings limieten van uw server te upgraden om meer geheugen beschikbaar te maken voor uw server door Compute te schalen of door geheugen geoptimaliseerde Sku's te gebruiken, die meer compute per kern hebben.

## <a name="scale-your-cache-to-a-different-size-or-sku-to-improve-cache-and-application-performance"></a>Schaal uw cache op een andere grootte of SKU om de cache-en toepassings prestaties te verbeteren

Cache-instanties worden het beste uitgevoerd wanneer ze niet worden uitgevoerd onder hoge geheugen belasting, hoge belasting van de server of hoge netwerk bandbreedte, waardoor ze niet meer reageren, verlies van gegevens opleveren of niet meer beschikbaar zijn. Advisor identificeert cache-instanties in deze omstandigheden en raden u aan om aanbevolen procedures toe te passen om de geheugen belasting, server belasting of netwerk bandbreedte te verminderen of te schalen naar een andere grootte of SKU met meer capaciteit.

## <a name="add-regions-with-traffic-to-your-azure-cosmos-db-account"></a>Regio's met verkeer toevoegen aan uw Azure Cosmos DB-account

Advisor detecteert Azure Cosmos DB accounts die verkeer hebben van een regio die momenteel niet is geconfigureerd en aanbeveling die regio toe te voegen. Hierdoor wordt de latentie verbeterd van aanvragen die afkomstig zijn uit deze regio en wordt de beschik baarheid gewaarborgd in het geval van regionale storingen. [Meer informatie over de distributie van globale gegevens met Azure Cosmos DB](https://aka.ms/cosmos/globaldistribution)

## <a name="configure-your-azure-cosmos-db-indexing-policy-with-customer-included-or-excluded-paths"></a>Uw Azure Cosmos DB-indexerings beleid configureren met door de klant opgenomen of uitgesloten paden

Azure Advisor herkent Cosmos DB containers die gebruikmaken van het standaard indexerings beleid, maar kunnen wel profiteren van een aangepast indexerings beleid op basis van het werkbelasting patroon. Het standaard indexerings beleid indexeert alle eigenschappen, maar met behulp van een aangepast indexerings beleid met expliciet opgenomen of uitgesloten paden die worden gebruikt in query filters, kunnen het RUs en de opslag voor indexering worden verminderd. [Meer informatie over het wijzigen van index beleid](https://aka.ms/cosmosdb/modify-index-policy)

## <a name="configure-your-azure-cosmos-db-query-page-size-maxitemcount-to--1"></a>Azure Cosmos DB-querypaginaformaat (MaxItemCount) instellen op -1 

Azure Advisor herkent Azure Cosmos DB containers die gebruikmaken van het pagina formaat van de query van 100 en het is raadzaam een pagina grootte van-1 te gebruiken voor snellere scans. [Meer informatie over maximum aantal items](https://aka.ms/cosmosdb/sql-api-query-metrics-max-item-count)

## <a name="how-to-access-performance-recommendations-in-advisor"></a>Aanbevelingen voor toegang tot prestatie verbeteringen in Advisor

1. Meld u aan bij de [Azure Portal](https://portal.azure.com)en open vervolgens [Advisor](https://aka.ms/azureadvisordashboard).

2.  Op het Advisor-dash board klikt u op het tabblad **prestaties** .

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Advisor-aanbevelingen:

* [Inleiding tot Advisor](advisor-overview.md)
* [Aan de slag met Advisor](advisor-get-started.md)
* [Aanbevelingen van Advisor met betrekking tot kosten](advisor-cost-recommendations.md)
* [Aanbevelingen voor hoge Beschik baarheid van Advisor](advisor-high-availability-recommendations.md)
* [Aanbevelingen voor de beveiliging van Advisor](advisor-security-recommendations.md)
* [Aanbevelingen voor operationele uitmuntendheid van Advisor](advisor-operational-excellence-recommendations.md)
