---
title: Referentiearchitecturen voor Oracle-databases op Azure | Microsoft Documenten
description: Verwijzingen naar architecturen voor het uitvoeren van Oracle Database Enterprise Edition-databases op Microsoft Azure Virtual Machines.
services: virtual-machines-linux
author: romitgirdhar
manager: gwallace
tags: ''
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/13/2019
ms.author: rogirdh
ms.custom: ''
ms.openlocfilehash: 235482f5d44877e5c4e47aed4d7eaf2baea5c3fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75560331"
---
# <a name="reference-architectures-for-oracle-database-enterprise-edition-on-azure"></a>Referentiearchitecturen voor Oracle Database Enterprise Edition op Azure

In deze handleiding vindt u informatie over het implementeren van een zeer beschikbare Oracle-database op Azure. Bovendien duikt deze gids in overwegingen voor noodherstel. Deze architecturen zijn gemaakt op basis van klantimplementaties. Deze handleiding is alleen van toepassing op Oracle Database Enterprise Edition.

Zie [Architect an Oracle DB](oracle-design.md)als u meer wilt weten over het maximaliseren van de prestaties van uw Oracle-database.

## <a name="assumptions"></a>Veronderstellingen

- U hebt inzicht in de verschillende concepten van Azure, zoals [beschikbaarheidszones](../../../availability-zones/az-overview.md)
- U draait Oracle Database Enterprise Edition 12c of hoger
- U bent zich bewust van en erkent de licentieimplicaties bij het gebruik van de oplossingen in dit artikel

## <a name="high-availability-for-oracle-databases"></a>Hoge beschikbaarheid voor Oracle-databases

Het bereiken van een hoge beschikbaarheid in de cloud is een belangrijk onderdeel van de planning en het ontwerp van elke organisatie. Microsoft Azure biedt [beschikbaarheidszones](../../../availability-zones/az-overview.md) en beschikbaarheidssets (te gebruiken in regio's waar beschikbaarheidszones niet beschikbaar zijn). Lees meer over [het beheren van de beschikbaarheid van uw virtuele machines](../../../virtual-machines/linux/manage-availability.md) om te ontwerpen voor de cloud.

Naast cloud-native tools en aanbiedingen biedt Oracle oplossingen voor hoge beschikbaarheid, zoals [Oracle Data Guard,](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7) [Data Guard met FSFO,](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html) [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html)en [GoldenGate](https://www.oracle.com/middleware/technologies/goldengate.html) die op Azure kunnen worden ingesteld. Deze gids behandelt referentiearchitecturen voor elk van deze oplossingen.

Ten slotte is het belangrijk om bij het migreren of maken van applicaties voor de cloud uw toepassingscode aan te passen om cloud-native patronen toe te voegen, zoals [het patroon opnieuw proberen](https://docs.microsoft.com/azure/architecture/patterns/retry) en het patroon van [stroomonderbrekers.](https://docs.microsoft.com/azure/architecture/patterns/circuit-breaker) Aanvullende patronen die zijn gedefinieerd in de [gids Voor cloudontwerppatronen](https://docs.microsoft.com/azure/architecture/patterns/) kunnen uw toepassing veerkrachtiger maken.

### <a name="oracle-rac-in-the-cloud"></a>Oracle RAC in de cloud

Oracle Real Application Cluster (RAC) is een oplossing van Oracle om klanten te helpen hoge doorvoersnelheden te bereiken door veel instanties toegang te hebben tot één databaseopslag (Gedeeld architectuurpatroon). Hoewel Oracle RAC ook kan worden gebruikt voor on-premises hoge beschikbaarheid, kan Oracle RAC alleen niet worden gebruikt voor hoge beschikbaarheid in de cloud, omdat het alleen beschermt tegen storingen op instantieniveau en niet tegen storingen op rack- of datacenterniveau. Daarom raadt Oracle aan oracle Data Guard te gebruiken met uw database (of het nu één instantie of RAC is) voor hoge beschikbaarheid. Klanten hebben over het algemeen een hoge SLA nodig voor het uitvoeren van hun bedrijfskritieke toepassingen. Oracle RAC is momenteel niet gecertificeerd of ondersteund door Oracle op Azure. Azure biedt echter functies zoals Azure-beschikbaarheidszones en geplande onderhoudsvensters om te beschermen tegen storingen op instantieniveau. Daarnaast kunnen klanten technologieën zoals Oracle Data Guard, Oracle GoldenGate en Oracle Sharding gebruiken voor hoge prestaties en veerkracht door hun databases te beschermen tegen rack-level en datacenter-niveau en geo-politieke mislukkingen.

Wanneer u Oracle Databases uitvoert in meerdere [beschikbaarheidszones](https://docs.microsoft.com/azure/availability-zones/az-overview) in combinatie met Oracle Data Guard of GoldenGate, kunnen klanten een uptime SLA van 99,99% krijgen. In Azure-regio's waar beschikbaarheidszones nog niet aanwezig zijn, kunnen klanten [beschikbaarheidssets](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) gebruiken en een uptime SLA van 99,95% behalen.

>OPMERKING: U een uptime-doel hebben dat veel hoger is dan de uptime SLA van Microsoft.

## <a name="disaster-recovery-for-oracle-databases"></a>Disaster recovery voor Oracle-databases

Wanneer u uw bedrijfskritieke toepassingen in de cloud host, is het belangrijk om te ontwerpen voor hoge beschikbaarheid en herstel na noodgevallen.

Voor Oracle Database Enterprise Edition is Oracle Data Guard een handige functie voor herstel na noodgevallen. U een stand-by database-exemplaar instellen in een [gekoppeldAzure-gebied](/azure/best-practices-availability-paired-regions) en een failover voor gegevensbewaking instellen voor herstel na noodgevallen. Voor nul gegevensverlies wordt aanbevolen om naast Active Data Guard ook een Oracle Data Guard Far Sync-exemplaar te implementeren. 

Overweeg het instellen van de Instantie Far Sync van Data Guard in een andere beschikbaarheidszone dan uw oracle primaire database als uw aanvraag de latentie toestaat (grondig testen is vereist). Gebruik een **modus Maximale beschikbaarheid** om synchroon transport van uw redo-bestanden naar de instantie Far Sync in te stellen. Deze bestanden worden vervolgens asynchroon overgebracht naar de stand-by database. 

Als uw toepassing het prestatieverlies niet toestaat bij het instellen van een Far Sync-instantie in een andere beschikbaarheidszone in **de modus Maximale beschikbaarheid** (synchroon), u een Far Sync-instantie instellen in dezelfde beschikbaarheidszone als uw primaire database. Voor extra beschikbaarheid u overwegen meerdere Far Sync-exemplaren in de buurt van uw primaire database en ten minste één instantie in de buurt van uw stand-bydatabase in te stellen (als de rol wordt overgezet). Lees meer over Oracle Data Guard Far Sync in deze [Oracle Active Data Guard Far Sync whitepaper.](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)

Bij het gebruik van Oracle Standard Edition-databases zijn er ISV-oplossingen zoals DBVisit Standby waarmee u hoge beschikbaarheid en noodherstel instellen.

## <a name="reference-architectures"></a>Referentiearchitecturen

### <a name="oracle-data-guard"></a>Oracle Data Guard

Oracle Data Guard zorgt voor een hoge beschikbaarheid, gegevensbescherming en noodherstel voor bedrijfsgegevens. Data Guard onderhoudt stand-by databases als transactioneel consistente kopieën van de primaire database. Afhankelijk van de afstand tussen de primaire en secundaire databases en de toepassingstolerantie voor latentie, u synchrone of asynchrone replicatie instellen. Als de primaire database niet beschikbaar is vanwege een geplande of ongeplande uitval, kan Data Guard elke stand-bydatabase overschakelen naar de primaire rol, waardoor downtime wordt geminimaliseerd. 

Wanneer u Oracle Data Guard gebruikt, u ook uw secundaire database openen voor alleen-lezen doeleinden. Deze configuratie heet Active Data Guard. Oracle Database 12c introduceerde een functie genaamd Data Guard Far Sync Instance. Met deze instantie u een configuratie met nul gegevensverlies van uw Oracle-database instellen zonder dat u compromissen hoeft te sluiten op het punt van prestaties.

> [!NOTE]
> Active Data Guard vereist extra licenties. Deze licentie is ook vereist om de Far Sync-functie te gebruiken. Neem contact op met uw Oracle-vertegenwoordiger om de licentieimplicaties te bespreken.

#### <a name="oracle-data-guard-with-fsfo"></a>Oracle Data Guard met FSFO
Oracle Data Guard met Fast-Start Failover (FSFO) kan extra veerkracht bieden door de broker op een aparte machine in te stellen. De Data Guard broker en de secundaire database draaien zowel de waarnemer als observeren de primaire database voor downtime. Dit zorgt voor redundantie in uw Data Guard waarnemer setup ook. 

Met Oracle Database versie 12.2 en hoger is het ook mogelijk om meerdere waarnemers te configureren met één Oracle Data Guard broker-configuratie. Deze instelling biedt extra beschikbaarheid, voor het geval één waarnemer en de secundaire database downtime ervaren. Data Guard Broker is lichtgewicht en kan worden gehost op een relatief kleine virtuele machine. Ga voor meer informatie over Data Guard Broker en de voordelen ervan naar de [Oracle-documentatie](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html) over dit onderwerp.

Het volgende diagram is een aanbevolen architectuur voor het gebruik van Oracle Data Guard op Azure met beschikbaarheidszones. Met deze architectuur u een VM-uptime SLA van 99,99% krijgen.

![Oracle Database met beschikbaarheidszones met Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_az.png)

In het voorgaande diagram heeft het clientsysteem toegang tot een aangepaste toepassing met Oracle backend via het web. De webfrontend is geconfigureerd in een load balancer. De webfrontend belt naar de juiste toepassingsserver om het werk af te handelen. De toepassingsserver vraagt de primaire Oracle-database op. De Oracle-database is geconfigureerd met behulp van een hyperthreaded [geheugen geoptimaliseerde virtuele machine](../../../virtual-machines/windows/sizes-memory.md) met beperkte core [vCPU's](../../../virtual-machines/windows/constrained-vcpu.md) om te besparen op licentiekosten en de prestaties te maximaliseren. Meerdere premium- of ultraschijven (Managed Disks) worden gebruikt voor prestaties en hoge beschikbaarheid.

De Oracle-databases worden in meerdere beschikbaarheidszones geplaatst voor hoge beschikbaarheid. Elke zone bestaat uit een of meer datacenters die zijn uitgerust met onafhankelijke stroom-, koelings- en netwerknetwerken. Om veerkracht te garanderen, worden in alle ingeschakelde regio's ten minste drie afzonderlijke zones ingesteld. De fysieke scheiding van beschikbaarheidszones binnen een regio beschermt de gegevens tegen fouten in datacenters. Daarnaast zijn twee FSFO-waarnemers ingesteld in twee beschikbaarheidszones om de database te starten en te mislukken naar het secundaire wanneer er een storing optreedt. 

U extra waarnemers en/of stand-bydatabases instellen in een andere beschikbaarheidszone (AZ 1, in dit geval) dan de zone die in de voorgaande architectuur wordt weergegeven. Ten slotte worden Oracle-databases gecontroleerd op uptime en prestaties door Oracle Enterprise Manager (OEM). OEM u ook verschillende prestatie- en gebruiksrapporten genereren.

In regio's waar beschikbaarheidszones niet worden ondersteund, u beschikbaarheidssets gebruiken om uw Oracle Database op een zeer beschikbare manier te implementeren. Met beschikbaarheidssets u een VM-uptime van 99,95% realiseren. Het volgende diagram is een referentiearchitectuur van dit gebruik:

![Oracle Database met beschikbaarheidssets met Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fsfo_as.png)

> [!NOTE]
> * De VM van Oracle Enterprise Manager hoeft niet in een beschikbaarheidsset te worden geplaatst, omdat er slechts één oem-exemplaren worden geïmplementeerd.
> * Ultra-schijven worden momenteel niet ondersteund in een configuratie met beschikbaarheidsset.

#### <a name="oracle-data-guard-far-sync"></a>Oracle Data Guard Far Sync

Oracle Data Guard Far Sync biedt nul mogelijkheden voor gegevensverliesbescherming voor Oracle Databases. Met deze mogelijkheid u zich beschermen tegen gegevensverlies als uw databasemachine uitvalt. Oracle Data Guard Far Sync moet op een aparte vm worden geïnstalleerd. Far Sync is een lichtgewicht Oracle-exemplaar dat alleen een besturingselementbestand, wachtwoordbestand, spfile en stand-bylogboeken heeft. Er zijn geen gegevensbestanden of rego-logbestanden. 

Voor nul gegevensverliesbescherming moet er synchrone communicatie zijn tussen uw primaire database en de instantie Far Sync. De instantie Far Sync ontvangt opnieuw van de primaire op een synchrone manier en stuurt deze onmiddellijk door naar alle stand-bydatabases op een asynchrone manier. Deze instelling vermindert ook de overhead op de primaire database, omdat het alleen de redo hoeft te verzenden naar de Far Sync-instantie in plaats van naar alle stand-bydatabases. Als een Far Sync-instantie mislukt, gebruikt Data Guard automatisch asynchrone transport naar de secundaire database vanuit de primaire database om bijna nul gegevensverliesbescherming te behouden. Voor extra tolerantie kunnen klanten meerdere Far Sync-exemplaren per database-instantie (primaire en secondaries) implementeren.

Het volgende diagram is een architectuur met hoge beschikbaarheid met Oracle Data Guard Far Sync:

![Oracle-database met beschikbaarheidszones met Data Guard Far Sync & Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az.png)

In de voorgaande architectuur is er een Far Sync-instantie geïmplementeerd in dezelfde beschikbaarheidszone als de database-instantie om de latentie tussen de twee te verminderen. In gevallen waarin de toepassing latentiegevoelig is, u overwegen uw database en Far Sync-instantie of -instanties in een [groep voor nabijheidsplaatsing](../../../virtual-machines/linux/proximity-placement-groups.md)te implementeren.

Het volgende diagram is een architectuur die gebruikmaakt van Oracle Data Guard FSFO en Far Sync om een hoge beschikbaarheid en disaster recovery te bereiken:

![Oracle Database gebruikt beschikbaarheidszones voor noodherstel met Data Guard Far Sync & Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_fs_az_dr.png)

### <a name="oracle-goldengate"></a>Oracle GoldenGate

GoldenGate maakt de uitwisseling en manipulatie van gegevens op transactieniveau mogelijk tussen meerdere heterogene platforms in de hele onderneming. Het verplaatst toegezegde transacties met transactie-integriteit en minimale overhead op uw bestaande infrastructuur. De modulaire architectuur biedt u de flexibiliteit om geselecteerde gegevensrecords, transactionele wijzigingen en wijzigingen in DDL (gegevensdefinitietaal) in verschillende topologieën te extraheren en te repliceren.

Met Oracle GoldenGate u uw database configureren voor hoge beschikbaarheid door bidirectionele replicatie te bieden. Hiermee u een **multi-master** of **actief-actieve configuratie**instellen. Het volgende diagram is een aanbevolen architectuur voor oracle GoldenGate active-active setup op Azure. In de volgende architectuur is de Oracle-database geconfigureerd met behulp van een hyperthreaded [geheugengeoptimaliseerde virtuele machine](../../../virtual-machines/windows/sizes-memory.md) met [beperkte core vCPU's](../../../virtual-machines/windows/constrained-vcpu.md) om licentiekosten te besparen en de prestaties te maximaliseren. Meerdere premium- of ultraschijven (beheerde schijven) worden gebruikt voor prestaties en beschikbaarheid.

![Oracle Database met beschikbaarheidszones met Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_gg_az.png)

> [!NOTE]
> Een vergelijkbare architectuur kan worden ingesteld met beschikbaarheidssets in regio's waar momenteel geen beschikbaarheidszones beschikbaar zijn.

Oracle GoldenGate heeft processen zoals Extract, Pump en Replicat waarmee u uw gegevens asynchroon repliceren van de ene Oracle-databaseserver naar de andere. Met deze processen u een bidirectionele replicatie instellen om een hoge beschikbaarheid van uw database te garanderen als er downtime op beschikbaarheidszoneniveau is. In het voorgaande diagram wordt het extractproces uitgevoerd op dezelfde server als uw Oracle-database, terwijl de gegevenspomp- en replicatprocessen worden uitgevoerd op een afzonderlijke server in dezelfde beschikbaarheidszone. Het Replicat-proces wordt gebruikt om gegevens uit de database in de andere beschikbaarheidszone te ontvangen en de gegevens te verbinden aan de Oracle-database in de beschikbaarheidszone. Op dezelfde manier verzendt het gegevenspompproces gegevens die door het extractproces zijn geëxtraheerd naar het Replicat-proces in de andere beschikbaarheidszone. 

Terwijl het voorgaande architectuurdiagram het gegevenspomp- en replicatproces toont dat op een afzonderlijke server is geconfigureerd, u alle Oracle GoldenGate-processen op dezelfde server instellen op basis van de capaciteit en het gebruik van uw server. Raadpleeg altijd uw AWR-rapport en de statistieken in Azure om inzicht te krijgen in het gebruikspatroon van uw server.

Bij het instellen van Oracle GoldenGate bidirectionele replicatie in verschillende beschikbaarheidszones of verschillende regio's, is het belangrijk om ervoor te zorgen dat de latentie tussen de verschillende componenten acceptabel is voor uw toepassing. De latentie tussen beschikbaarheidszones en regio's kan variëren en is afhankelijk van meerdere factoren. Het wordt aanbevolen om prestatietests in te stellen tussen uw toepassingslaag en uw databaselaag in verschillende beschikbaarheidszones en/of regio's om te controleren of deze voldoet aan de vereisten voor toepassingsprestaties.

De toepassingslaag kan worden ingesteld in een eigen subnet en de databaselaag kan worden gescheiden in een eigen subnet. Overweeg waar mogelijk [Azure Application Gateway](../../../application-gateway/overview.md) te gebruiken voor load-balance verkeer tussen uw toepassingsservers. Azure Application Gateway is een robuuste laadbalans voor webverkeer. Het biedt cookie-gebaseerde sessie affiniteit die een gebruikerssessie houdt op dezelfde server, waardoor het minimaliseren van de conflicten op de database. Alternatieven voor Application Gateway zijn [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) en [Azure Traffic Manager.](../../../traffic-manager/traffic-manager-overview.md)

### <a name="oracle-sharding"></a>Oracle Sharding

Sharding is een gegevenslaagpatroon dat werd geïntroduceerd in Oracle 12.2. Hiermee u uw gegevens horizontaal verdelen en schalen over onafhankelijke databases. Het is een share-nothing architectuur waarbij elke database wordt gehost op een speciale virtuele machine, die hoge lees- en schrijfdoorvoer mogelijk maakt naast tolerantie en verhoogde beschikbaarheid. Dit patroon elimineert enkele storingspunten, biedt foutisolatie en maakt rolling upgrades mogelijk zonder downtime. De uitvaltijd van een shard of een storing op datacenterniveau heeft geen invloed op de prestaties of beschikbaarheid van de andere shards in andere datacenters. 

Sharding is geschikt voor OLTP-toepassingen met een hoge doorvoer die zich geen downtime kunnen veroorloven. Alle rijen met dezelfde shardingsleutel zijn altijd gegarandeerd op dezelfde shard, waardoor de prestaties toenemen, waardoor de hoge consistentie toeneemt. Toepassingen die sharding gebruiken, moeten een goed gedefinieerd gegevensmodel en een strategie voor gegevensdistributie hebben (consistente hash, bereik, lijst of composiet) die voornamelijk toegang heeft tot gegevens met behulp van een shardingsleutel (bijvoorbeeld *customerId* of *accountNum).* Met Sharding u ook bepaalde gegevenssets dichter bij de eindklanten opslaan, zodat u voldoen aan uw prestatie- en nalevingsvereisten.

Het wordt aanbevolen dat u uw scherven repliceert voor hoge beschikbaarheid en herstel na noodgevallen. Deze installatie kan worden uitgevoerd met Oracle-technologieën zoals Oracle Data Guard of Oracle GoldenGate. Een replicatieeenheid kan een scherf, een onderdeel van een scherf of een groep scherven zijn. De beschikbaarheid van een geshard database wordt niet beïnvloed door een storing of vertraging van een of meer scherven. Voor hoge beschikbaarheid kunnen de stand-by scherven worden geplaatst in dezelfde beschikbaarheidszone waar de primaire scherven worden geplaatst. Voor noodherstel kunnen de stand-by scherven in een andere regio worden geplaatst. U ook shards in meerdere regio's inzetten om het verkeer in die regio's te bedienen. Lees meer over het configureren van hoge beschikbaarheid en replicatie van uw geshard database in [Oracle Sharding-documentatie.](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html)

Oracle Sharding bestaat voornamelijk uit de volgende componenten. Meer informatie over deze componenten is te vinden in [de documentatie van Oracle Sharding:](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)

- **Shard catalogus** - Oracle-database voor speciale doeleinden die een permanente opslag is voor alle sharddatabaseconfiguratiegegevens. Alle configuratiewijzigingen, zoals het toevoegen of verwijderen van shards, het toewijzen van de gegevens en DDL's in een sharddatabase, worden gestart in de shardcatalogus. De shardcatalogus bevat ook de hoofdkopie van alle gedupliceerde tabellen in een SDB. 

  De shardcatalogus gebruikt gematerialiseerde weergaven om wijzigingen in dubbele tabellen in alle sharden automatisch te repliceren. De shardcatalogusdatabase fungeert ook als querycoördinator die wordt gebruikt voor het verwerken van multishardquery's en query's die geen shardingssleutel opgeven. 
  
  Using Oracle Data Guard in conjunction with availability zones or availability sets for shard catalog high availability is a recommended best practice. De beschikbaarheid van de shardcatalogus heeft geen invloed op de beschikbaarheid van de geshard database. Een downtime in de shardcatalogus heeft alleen invloed op onderhoudsbewerkingen en multishard query's tijdens de korte periode dat de failover van de gegevenswacht is voltooid. Online transacties worden nog steeds gerouteerd en uitgevoerd door de SDB en worden niet beïnvloed door een catalogusstoring.

- **Shard-directeuren** - Lichtgewicht services die moeten worden geïmplementeerd in elke regio/beschikbaarheidszone waarin uw scherven zich bevinden. Shard Directors zijn Global Service Managers ingezet in het kader van Oracle Sharding. Voor hoge beschikbaarheid is het raadzaam om ten minste één sharddirector in elke beschikbaarheidszone te implementeren waarin uw shards bestaan. 

  Wanneer u in eerste instantie verbinding maakt met de database, wordt de routeringsinformatie ingesteld door een sharddirecteur en wordt deze in de cache opgeslagen voor volgende aanvragen, waarbij de sharddirecteur wordt omzeild. Zodra de sessie is ingesteld met een shard, worden alle SQL-query's en DMLs ondersteund en uitgevoerd in het bereik van de gegeven shard. Deze routering is snel en wordt gebruikt voor alle OLTP-workloads die intrashard-transacties uitvoeren. Het wordt aanbevolen om directe routering te gebruiken voor alle OLTP-workloads die de hoogste prestaties en beschikbaarheid vereisen. De routeringscache wordt automatisch vernieuwd wanneer een shard niet meer beschikbaar is of er wijzigingen optreden in de shardingtopologie. 
  
  Voor krachtige, gegevensafhankelijke routering raadt Oracle aan een verbindingsgroep te gebruiken wanneer u toegang krijgt tot gegevens in de geshard database. Oracle-verbindingsgroepen, taalspecifieke bibliotheken en stuurprogramma's ondersteunen Oracle Sharding. Raadpleeg [de documentatie van Oracle Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html#GUID-3D41F762-BE04-486D-8018-C7A210D809F9) voor meer informatie.

- **Wereldwijde service** - Wereldwijde service is vergelijkbaar met de reguliere databaseservice. Naast alle eigenschappen van een databaseservice heeft een globale service eigenschappen voor gesharddatabases, zoals regioaffiniteit tussen clients en shard- en replicatievertragingstolerantie. Er hoeft slechts één globale service te worden gemaakt om gegevens te lezen/schrijven naar/vanuit een geshard database. Wanneer u Active Data Guard gebruikt en alleen-lezen replica's van de shards instelt, u een andere gGobal-service maken voor alleen-lezen workloads. De client kan deze Global-services gebruiken om verbinding te maken met de database.

- **Shard databases** - Shard databases zijn uw Oracle databases. Elke database wordt gerepliceerd met Oracle Data Guard in een Broker-configuratie met Fast-Start Failover (FSFO) ingeschakeld. U hoeft geen failover gegevensbeveiliging en replicatie in te stellen op elke shard. Dit wordt automatisch geconfigureerd en geïmplementeerd wanneer de gedeelde database wordt gemaakt. Als een bepaalde shard uitvalt, mislukt Oracle Sharing automatisch via databaseverbindingen van het primaire naar de stand-by.

U Oracle sharded databases implementeren en beheren met twee interfaces: `GDSCTL` Oracle Enterprise Manager Cloud Control GUI en/of het command-line hulpprogramma. U zelfs de verschillende scherven controleren op beschikbaarheid en prestaties met behulp van Cloud control. De `GDSCTL DEPLOY` opdracht maakt automatisch de scherven en hun respectievelijke luisteraars. Bovendien implementeert deze opdracht automatisch de replicatieconfiguratie die wordt gebruikt voor hoge beschikbaarheid op shardniveau die door de beheerder is opgegeven.

Er zijn verschillende manieren om een database te sharden:

* Systeembeheer - Distribueert automatisch over shards met partitionering
* Door de gebruiker gedefinieerde sharding - Hiermee u de toewijzing van de gegevens aan de shards opgeven, wat goed werkt wanneer er vereisten voor regelgeving of gegevenslokalisatie zijn)
* Composietharding - Een combinatie van systeembeheer de sharding voor verschillende _shardspaces_
* Tabelsubpartities - Vergelijkbaar met een gewone partitietabel.

Lees meer over de verschillende [shardingmethoden](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-methods.html) in de documentatie van Oracle.

Hoewel een geshard database eruit kan zien als één database voor toepassingen en ontwikkelaars, is het bij het migreren van een niet-geshard database naar een geshard database vereist om te bepalen welke tabellen worden gedupliceerd versus geshard. 

Dubbele tabellen worden opgeslagen op alle scherven, terwijl geshard tabellen zijn verdeeld over verschillende scherven. De aanbeveling is om kleine en dimensionale tabellen te dupliceren en de feitentabellen te distribueren/te verharden. Gegevens kunnen worden geladen in uw sharded database met behulp van ofwel de shard catalogus als de centrale coördinator of door het uitvoeren van Data Pump op elke scherf. Lees meer over [het migreren van gegevens naar een geshard database](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-loading-data.html) in de documentatie van Oracle.

#### <a name="oracle-sharding-with-data-guard"></a>Oracle Sharding met Data Guard

Oracle Data Guard kan worden gebruikt voor het sharden met systeembeheerde, door de gebruiker gedefinieerde en samengestelde shardingmethoden.

Het volgende diagram is een referentiearchitectuur voor Oracle Sharding met Oracle Data Guard die wordt gebruikt voor een hoge beschikbaarheid van elke shard. Het architectuurdiagram toont een _samengestelde shardmethode_. Het architectuurdiagram zal waarschijnlijk verschillen voor toepassingen met verschillende vereisten voor gegevensplaats, taakverdeling, hoge beschikbaarheid, herstel na noodgevallen, enz. Met Oracle Sharding u aan deze vereisten voldoen en horizontaal en efficiënt schalen door deze opties aan te bieden. Een vergelijkbare architectuur kan zelfs worden geïmplementeerd met Oracle GoldenGate.

![Oracle Database Sharding met beschikbaarheidszones met Data Guard Broker - FSFO](./media/oracle-reference-architecture/oracledb_dg_sh_az.png)

Hoewel systeembeheer door het systeem het gemakkelijkst te configureren en te beheren is, is door de gebruiker gedefinieerde sharding of samengestelde sharding zeer geschikt voor scenario's waarin uw gegevens en toepassing geogedistribueerd zijn of in scenario's waarin u controle moet hebben over de replicatie van elke scherf. 

In de voorgaande architectuur wordt composietsharding gebruikt om de gegevens te geodistribueren en uw toepassingslagen horizontaal uit te schalen. Composietsharding is een combinatie van systeembeheer en door de gebruiker gedefinieerde sharding en biedt zo het voordeel van beide methoden. In het vorige scenario worden gegevens eerst geshard over meerdere shardruimten gescheiden per regio. Vervolgens worden de gegevens verder verdeeld door consistente hash over meerdere scherven in de shardruimte. Elke shardruimte bevat meerdere shardgroepen. Elke shardgroep heeft meerdere scherven en is een "eenheid" van replicatie, in dit geval. Elke shardgroep bevat alle gegevens in de shardruimte. Shardgroepen A1 en B1 zijn primaire shardgroepen, terwijl shardgroepen A2 en B2 standbys zijn. U ervoor kiezen om afzonderlijke scherven de eenheid van replicatie te hebben, in plaats van een shardgroep.

In de voorgaande architectuur wordt in elke beschikbaarheidszone een GSM/shard director ingezet voor hoge beschikbaarheid. De aanbeveling is om ten minste één GSM/shard director per datacenter/regio in te zetten. Bovendien wordt een instantie van de toepassingsserver geïmplementeerd in elke beschikbaarheidszone die een shardgroep bevat. Met deze instelling kan de toepassing de latentie tussen de toepassingsserver en de database/shardgroep laag houden. Als een database mislukt, kan de toepassingsserver in dezelfde zone als de stand-bydatabase aanvragen verwerken zodra de overgang van de databaserol plaatsvindt. Azure Application Gateway en de sharddirecteur houden de vraag- en antwoordlatentie en routeaanvragen dienovereenkomstig bij.

Vanuit het oogpunt van de toepassing doet het clientsysteem een verzoek naar Azure Application Gateway (of andere load-balancing-technologieën in Azure) die de aanvraag doorverwijst naar de regio die het dichtst bij de client staat. Azure Application Gateway ondersteunt ook plaksessies, zodat aanvragen die afkomstig zijn van dezelfde client worden doorgestuurd naar dezelfde toepassingsserver. De toepassingsserver maakt gebruik van verbindingspooling in stuurprogramma's voor gegevenstoegang. Deze functie is beschikbaar in drivers zoals JDBC, ODP.NET, OCI, enz. De bestuurders kunnen shardingsleutels herkennen die als onderdeel van de aanvraag zijn opgegeven. [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) voor JDBC-clients kan niet-Oracle-toepassingsclients zoals Apache Tomcat en IIS in staat stellen om met Oracle Sharding te werken. 

Tijdens het eerste verzoek maakt de toepassingsserver verbinding met de sharddirecteur in zijn regio om routeringsinformatie te krijgen voor de shard waarnaar de aanvraag moet worden doorgestuurd. Op basis van de doorgegeven shardingssleutel leidt de directeur de toepassingsserver naar de betreffende shard. De toepassingsserver slaat deze informatie in de cache op door een kaart te maken en voor volgende aanvragen omzeilt u de sharddirecteur en stuurt de aanvragen rechtstreeks naar de shard.

#### <a name="oracle-sharding-with-goldengate"></a>Oracle Sharding met GoldenGate

Het volgende diagram is een referentiearchitectuur voor Oracle Sharding met Oracle GoldenGate voor een hoge beschikbaarheid in de regio van elke shard. In tegenstelling tot de voorgaande architectuur, geeft deze architectuur alleen een hoge beschikbaarheid weer binnen één Azure-regio (meerdere beschikbaarheidszones). Men zou een multi-regio sharded database met hoge beschikbaarheid kunnen implementeren (vergelijkbaar met het vorige voorbeeld) met Oracle GoldenGate.

![Oracle Database Sharding met beschikbaarheidszones met GoldenGate](./media/oracle-reference-architecture/oracledb_gg_sh_az.png)

De voorgaande referentiearchitectuur gebruikt de _systeembeheermethode_ om de gegevens te sharden. Aangezien Oracle GoldenGate-replicatie op een segmentniveau wordt uitgevoerd, kan de helft van de gegevens die naar één shard worden gerepliceerd, worden gerepliceerd naar een andere scherf. De andere helft kan worden gerepliceerd naar een andere scherf. 

De manier waarop de gegevens worden gerepliceerd, is afhankelijk van de replicatiefactor. Met een replicatiefactor van 2 hebt u twee kopieën van elk stuk gegevens over uw drie shards in de shardgroep. Met een replicatiefactor van 3 en drie scherven in uw shardgroep worden alle gegevens in elke shard gerepliceerd naar elke andere shard in de shardgroep. Elke scherf in de shardgroep kan een andere replicatiefactor hebben. Met deze instelling u uw ontwerp voor hoge beschikbaarheid en noodherstel efficiënt definiëren binnen een shardgroep en in meerdere shardgroepen.

In de voorgaande architectuur bevatten shardgroep A en shardgroep B beide dezelfde gegevens, maar bevinden zich in verschillende beschikbaarheidszones. Als zowel shardgroep A als shardgroep B dezelfde replicatiefactor van 3 hebben, wordt elke rij/stuk van uw shardtabel 6 keer gerepliceerd in de twee shardgroepen. Als shardgroep A een replicatiefactor van 3 heeft en shardgroep B een replicatiefactor van 2 heeft, wordt elke rij/stuk 5 keer gerepliceerd in de twee shardgroepen.

Met deze instelling voorkomt u gegevensverlies als er een fout op instantieniveau of beschikbaarheidszone optreedt. De toepassingslaag is in staat om te lezen en te schrijven naar elke scherf. Om conflicten tot een minimum te beperken, wijst Oracle Sharding een 'hoofdsegment' aan voor elk bereik van hashwaarden. Deze functie zorgt ervoor dat schrijfaanvragen voor een bepaalde chunk worden gericht op de bijbehorende chunk. Daarnaast biedt Oracle GoldenGate automatische conflictdetectie en -oplossing om eventuele conflicten aan te pakken. Zie de documentatie van Oracle over het gebruik van [Oracle GoldenGate met een verharde database](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-high-availability.html#GUID-4FC0AC46-0B8B-4670-BBE4-052228492C72)voor meer informatie en beperkingen over het implementeren van GoldenGate met Oracle Sharding.

In de voorgaande architectuur wordt in elke beschikbaarheidszone een GSM/shard director ingezet voor hoge beschikbaarheid. De aanbeveling is om ten minste één GSM/shard-directeur per datacenter of regio in te zetten. Bovendien wordt een instantie van de toepassingsserver geïmplementeerd in elke beschikbaarheidszone die een shardgroep bevat. Met deze instelling kan de toepassing de latentie tussen de toepassingsserver en de database/shardgroep laag houden. Als een database uitvalt, kan de toepassingsserver in dezelfde zone als de stand-bydatabase aanvragen verwerken zodra de databaserol is overgezet. Azure Application Gateway en de sharddirecteur houden de vraag- en antwoordlatentie en routeaanvragen dienovereenkomstig bij.

Vanuit het oogpunt van de toepassing doet het clientsysteem een verzoek naar Azure Application Gateway (of andere load-balancing-technologieën in Azure) die de aanvraag doorverwijst naar de regio die het dichtst bij de client staat. Azure Application Gateway ondersteunt ook plaksessies, zodat aanvragen die afkomstig zijn van dezelfde client worden doorgestuurd naar dezelfde toepassingsserver. De toepassingsserver maakt gebruik van verbindingspooling in stuurprogramma's voor gegevenstoegang. Deze functie is beschikbaar in drivers zoals JDBC, ODP.NET, OCI, enz. De bestuurders kunnen shardingsleutels herkennen die als onderdeel van de aanvraag zijn opgegeven. [Oracle Universal Connection Pool (UCP)](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/jjucp/ucp-database-sharding-overview.html) voor JDBC-clients kan niet-Oracle-toepassingsclients zoals Apache Tomcat en IIS in staat stellen om met Oracle Sharding te werken. 

Tijdens het eerste verzoek maakt de toepassingsserver verbinding met de sharddirecteur in zijn regio om routeringsinformatie te krijgen voor de shard waarnaar de aanvraag moet worden doorgestuurd. Op basis van de doorgegeven shardingssleutel leidt de directeur de toepassingsserver naar de betreffende shard. De toepassingsserver slaat deze informatie in de cache op door een kaart te maken en voor volgende aanvragen omzeilt u de sharddirecteur en stuurt de aanvragen rechtstreeks naar de shard.

## <a name="patching-and-maintenance"></a>Patchen en onderhoud

Bij het implementeren van uw Oracle-workloads op Azure zorgt Microsoft voor alle patching op host-osniveau. Alle geplande OS-niveau onderhoud wordt meegedeeld aan klanten op voorhand om de klant voor dit geplande onderhoud. Twee servers van twee verschillende beschikbaarheidszones worden nooit tegelijkertijd gepatcht. Zie [De beschikbaarheid van virtuele machines beheren](../../../virtual-machines/linux/manage-availability.md) voor meer informatie over VM-onderhoud en patchen. 

Het patchen van uw besturingssysteem voor virtuele machines kan worden geautomatiseerd met [Azure Automation.](../../../automation/automation-tutorial-update-management.md) Het patchen en onderhouden van uw Oracle-database kan worden geautomatiseerd en gepland met [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines?view=azure-devops) of [Azure Automation](../../../automation/automation-tutorial-update-management.md) om downtime te minimaliseren. Zie [Continue levering en blauw/groene implementaties](/azure/devops/learn/what-is-continuous-delivery) om te begrijpen hoe deze kunnen worden gebruikt in de context van uw Oracle-databases.

## <a name="architecture-and-design-considerations"></a>Architectuur- en ontwerpoverwegingen

- Overweeg om hyperthreaded [geheugengeoptimaliseerde virtuele machine](../../../virtual-machines/windows/sizes-memory.md) te gebruiken met [beperkte core vCPU's](../../../virtual-machines/windows/constrained-vcpu.md) voor uw Oracle Database VM om licentiekosten te besparen en de prestaties te maximaliseren. Gebruik meerdere premium- of ultraschijven (beheerde schijven) voor prestaties en beschikbaarheid.
- Bij het gebruik van beheerde schijven kan de naam van de schijf/apparaat bij opnieuw opstarten worden gewijzigd. Het wordt aanbevolen dat u het apparaat UUID gebruikt in plaats van de naam om ervoor te zorgen dat uw mounts blijven bestaan tijdens het opnieuw opstarten. Meer informatie is [hier](../../../virtual-machines/linux/configure-raid.md#add-the-new-file-system-to-etcfstab)te vinden.
- Gebruik beschikbaarheidszones om een hoge beschikbaarheid in de regio te bereiken.
- Overweeg ultraschijven (indien beschikbaar) of premium schijven te gebruiken voor uw Oracle-database.
- Overweeg een stand-by Oracle-database in een andere Azure-regio in te stellen met Oracle Data Guard.
- Overweeg [nabijheidsplaatsingsgroepen](../../../virtual-machines/linux/co-location.md#proximity-placement-groups) te gebruiken om de latentie tussen uw toepassing en databaselaag te verminderen.
- Oracle [Enterprise Manager](https://docs.oracle.com/en/enterprise-manager/) instellen voor beheer, bewaking en logboekregistratie.
- Overweeg oracle Automatic Storage Management (ASM) te gebruiken voor gestroomlijnd opslagbeheer voor uw database.
- Gebruik [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) om patching en updates van uw database te beheren zonder downtime.
- Pas uw toepassingscode aan om cloud-native patronen toe te voegen, zoals [het patroon opnieuw proberen,](/azure/architecture/patterns/retry) [het patroon van de stroomonderbreker](/azure/architecture/patterns/circuit-breaker)en andere patronen die zijn gedefinieerd in de [cloudontwerppatronengids](/azure/architecture/patterns/) die uw toepassing veerkrachtiger kunnen maken.

## <a name="next-steps"></a>Volgende stappen

Bekijk de volgende Oracle-referentieartikelen die van toepassing zijn op uw scenario.

- [Inleiding tot Oracle Data Guard](https://docs.oracle.com/en/database/oracle/oracle-database/18/sbydb/introduction-to-oracle-data-guard-concepts.html#GUID-5E73667D-4A56-445E-911F-1E99092DD8D7)
- [Oracle Data Guard Broker Concepten](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/oracle-data-guard-broker-concepts.html)
- [Oracle GoldenGate configureren voor actieve actieve hoge beschikbaarheid](https://docs.oracle.com/goldengate/1212/gg-winux/GWUAD/wu_bidirectional.htm#GWUAD282)
- [Overzicht van Oracle Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/19/shard/sharding-overview.html)
- [Oracle Active Data Guard Far Sync Zero Data Loss op elke afstand](https://www.oracle.com/technetwork/database/availability/farsync-2267608.pdf)
