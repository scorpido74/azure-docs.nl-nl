---
title: Migreren van SQL Server naar beheerde instantie
description: Meer informatie over het migreren van een database van SQL Server-instantie naar Azure SQL Database - Beheerde instantie.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: 6bae9e871be2a5d56d057d2a077de53329b8c3ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79208933"
---
# <a name="sql-server-instance-migration-to-azure-sql-database-managed-instance"></a>SQL Server-instantie migratie naar Azure SQL Database beheerd

In dit artikel leert u over de methoden voor het migreren van een SQL Server 2005- of later-versieinstantie naar [azure SQL Database-beheerde instantie](sql-database-managed-instance.md). Zie [Migreren naar één of samengevoegde database voor](sql-database-cloud-migrate.md)informatie over migreren naar één database of een enkele database. Zie [Azure Database Migration Guide](https://datamigration.microsoft.com/)voor migratiegegevens over migreren vanaf andere platforms.

> [!NOTE]
> Als u snel managed instance wilt starten en proberen, u naar [snelstartgids](sql-database-managed-instance-quickstart-guide.md) gaan in plaats van naar deze pagina. 

Op een hoog niveau ziet het databasemigratieproces eruit als volgt:

![migratieproces](./media/sql-database-managed-instance-migration/migration-process.png)

- [Beoordeel de compatibiliteit met beheerde instantie](#assess-managed-instance-compatibility) wanneer u ervoor moet zorgen dat er geen blokkeringsproblemen zijn die uw migraties kunnen voorkomen.
  - Deze stap omvat ook het maken van [prestatiebasislijnen](#create-performance-baseline) om het resourcegebruik op uw sql server-exemplaar voor bronnen te bepalen. Deze stap is nodig als u wilt dat o de juiste beheerde instantie implementeert en controleert of de prestaties na migratie niet worden beïnvloed.
- [Opties voor app-connectiviteit kiezen](sql-database-managed-instance-connect-app.md)
- [Implementeer naar een optimaal beheerde instantie](#deploy-to-an-optimally-sized-managed-instance) waarbij u technische kenmerken kiest (aantal vCores, hoeveelheid geheugen) en prestatielaag (Bedrijfskritiek, Algemeen Doel) van uw beheerde instantie.
- [Selecteer migratiemethode en migreer](#select-migration-method-and-migrate) waar u uw databases migreert met offline migratie (native backup/restore, database importe/export) of online migratie (Data Migration Service, Transactionele Replicatie).
- [Monitor toepassingen](#monitor-applications) om ervoor te zorgen dat u prestaties verwacht.

> [!NOTE]
> Zie [Een SQL Server-database migreren naar Azure SQL Database als](sql-database-single-database-migrate.md)u een afzonderlijke database wilt migreren naar één database of een elastische groep.

## <a name="assess-managed-instance-compatibility"></a>Compatibiliteit met beheerde instantie beoordelen

Bepaal eerst of beheerde instantie compatibel is met de databasevereisten van uw toepassing. De beheerde optie voor het implementeren van instance is ontworpen om eenvoudige lift- en shiftmigratie te bieden voor de meeste bestaande toepassingen die SQL Server on-premises of op virtuele machines gebruiken. Soms hebt u echter functies of mogelijkheden nodig die nog niet worden ondersteund en zijn de kosten voor het implementeren van een tijdelijke oplossing te hoog.

Gebruik [De Gegevensmigratieassistent (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) om mogelijke compatibiliteitsproblemen op te sporen die van invloed zijn op de databasefunctionaliteit in Azure SQL Database. DMA ondersteunt beheerde instantie nog niet als migratiebestemming, maar het wordt aanbevolen om de beoordeling tegen Azure SQL Database uit te voeren en de lijst met gerapporteerde functiepariteits- en compatibiliteitsproblemen zorgvuldig te controleren ten opzichte van productdocumentatie. Zie [Azure SQL Database-functies](sql-database-features.md) om te controleren of er een aantal gerapporteerde blokkeringsproblemen zijn die niet worden geblokkeerd in beheerde instantie, omdat de meeste blokkeringsproblemen die een migratie naar Azure SQL Database verhinderen, zijn verwijderd met beheerde instantie. Functies zoals cross-database query's, cross-database transacties binnen dezelfde instantie, gekoppelde server naar andere SQL-bronnen, CLR, globale temp tabellen, instantie niveau weergaven, Service Broker en dergelijke zijn beschikbaar in beheerde instanties.

Als er een aantal gemelde blokkeringsproblemen zijn die niet zijn verwijderd met de beheerde optie voor het implementeren van een instantie, moet u mogelijk een alternatieve optie overwegen, zoals [SQL Server op virtuele Azure-machines.](https://azure.microsoft.com/services/virtual-machines/sql-server/) Hier volgen enkele voorbeelden:

- Als u directe toegang tot het besturingssysteem of bestandssysteem nodig hebt, bijvoorbeeld om externe of aangepaste agents op dezelfde virtuele machine met SQL Server te installeren.
- Als u strikt afhankelijk bent van functies die nog steeds niet worden ondersteund, zoals FileStream / FileTable, PolyBase en transacties tussen verschillende gevallen.
- Als het absoluut nodig is om bij een specifieke versie van SQL Server te blijven (2012, bijvoorbeeld).
- Als uw rekenvereisten veel lager zijn dan de beheerde instantie biedt (een vCore, bijvoorbeeld) en database consolidatie is niet aanvaardbaar optie.

Als u alle geïdentificeerde migratieblokkers hebt opgelost en de migratie naar Beheerde instantie hebt voortgezet, moet u er rekening mee houden dat sommige wijzigingen van invloed kunnen zijn op de prestaties van uw werkbelasting:
- Het verplichte volledige herstelmodel en het reguliere geautomatiseerde back-upschema kunnen van invloed zijn op de prestaties van uw werkbelasting of onderhoud/ETL-acties als u periodiek een eenvoudig/bulk-gelogd model hebt gebruikt of back-ups op aanvraag hebt gestopt.
- Verschillende configuraties op server- of databaseniveau, zoals traceflags of compatibiliteitsniveaus
- Nieuwe functies die u gebruikt, zoals Transparent Database Encryption (TDE) of auto-failovergroepen, kunnen van invloed zijn op het CPU- en IO-gebruik.

Beheerde instantie garandeert 99,99% beschikbaarheid, zelfs in de kritieke scenario's, zodat overhead veroorzaakt door deze functies niet kan worden uitgeschakeld. Zie voor meer informatie [de hoofdoorzaken die verschillende prestaties op SQL Server en Beheerde instantie kunnen veroorzaken.](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)

### <a name="create-performance-baseline"></a>Prestatiebasislijn maken

Als u de prestaties van uw workload op Managed Instance moet vergelijken met uw oorspronkelijke workload die op SQL Server wordt uitgevoerd, moet u een prestatiebasislijn maken die wordt gebruikt voor vergelijking. 

Prestatiebasislijn is een set parameters zoals gemiddeld/max CPU-gebruik, gemiddelde/max-schijfIO-latentie, doorvoer, IOPS, gemiddelde/max-levensduur van de pagina, gemiddelde maximale grootte van tempdb. U wilt vergelijkbare of zelfs betere parameters hebben na de migratie, dus het is belangrijk om de basislijnwaarden voor deze parameters te meten en vast te leggen. Naast systeemparameters moet u een set representatieve query's of de belangrijkste query's in uw werkbelasting selecteren en de gemiddelde/max-duur van min/max, CPU-gebruik voor de geselecteerde query's meten. Met deze waarden u de prestaties van werkbelasting die wordt uitgevoerd op Managed Instance vergelijken met de oorspronkelijke waarden op uw bron SQL Server.

Enkele van de parameters die u moet meten op uw SQL Server-exemplaar zijn: 
- [Monitor het CPU-gebruik op uw SQL Server-exemplaar](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) en neem het gemiddelde en piek-CPU-gebruik op.
- [Monitor het geheugengebruik van uw SQL Server-instantie](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) en bepaal de hoeveelheid geheugen die wordt gebruikt door verschillende componenten, zoals bufferpool, plancache, kolom-storepool, [IN-memory OLTP,](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017)enz. Daarnaast moet u de gemiddelde en piekwaarden van page life expectancy memory performance teller vinden.
- Monitor het IO-gebruik van de schijf op de bron SQL Server-instantie met behulp van [sys.dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) weergave- of [prestatiemeteritems.](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage)
- Controleer de werkbelasting en queryprestaties of uw SQL Server-instantie door dynamische beheerweergaven of queryarchief te onderzoeken als u migreert vanuit de SQL Server 2016+-versie. Identificeer de gemiddelde duur en het CPU-gebruik van de belangrijkste query's in uw werkbelasting om ze te vergelijken met de query's die worden uitgevoerd op de beheerde instantie.

> [!Note]
> Als u een probleem opmerkt met uw workload op SQL Server, zoals een hoog CPU-gebruik, constante geheugendruk, tempdb of parameterisatieproblemen, moet u proberen deze op te lossen op uw sql server-instantie voordat u de basislijn en migratie neemt. Het migreren van know-problemen naar een nieuw systeemmigh leidt tot onverwachte resultaten en maakt elke prestatievergelijking ongeldig.

Als gevolg van deze activiteit moet u hebben gedocumenteerd gemiddelde en piekwaarden voor CPU, geheugen en IO-gebruik op uw bronsysteem, evenals de gemiddelde en maximale duur en CPU-gebruik van de dominante en de meest kritieke query's in uw werkbelasting. U moet deze waarden later gebruiken om de prestaties van uw werkbelasting op Managed Instance te vergelijken met de basislijnprestaties van de werkbelasting op de bron SQL Server.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Implementeren op een optimaal beheerde instantie

Beheerde instantie is afgestemd op on-premises workloads die van plan zijn om naar de cloud te gaan. Het introduceert een [nieuw inkoopmodel](sql-database-service-tiers-vcore.md) dat meer flexibiliteit biedt bij het selecteren van het juiste niveau van resources voor uw workloads. In de on-premises wereld bent u waarschijnlijk gewend aan het dimensioneren van deze workloads met behulp van fysieke cores en IO-bandbreedte. Het inkoopmodel voor beheerde instantie is gebaseerd op virtuele cores, of "vCores", met extra opslag en IO afzonderlijk beschikbaar. Het vCore-model is een eenvoudigere manier om uw rekenvereisten in de cloud te begrijpen ten opzichte van wat u vandaag on-premises gebruikt. Met dit nieuwe model u uw doelomgeving in de cloud op de juiste grootte plaatsen. Enkele algemene richtlijnen die u kunnen helpen om de juiste servicelaag en -kenmerken te kiezen, worden hier beschreven:
- Op basis van het CPU-gebruik basislijn u een beheerde instantie inrichten die overeenkomt met het aantal cores dat u op SQL Server gebruikt, met in gedachten dat CPU-kenmerken mogelijk moeten worden geschaald om aan [vm-kenmerken](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics)te voldoen waar Managed Instance is geïnstalleerd.
- Kies op basis van het basislijngeheugengebruik [de servicelaag met overeenkomend geheugen.](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics) De hoeveelheid geheugen kan niet direct worden gekozen, dus u moet de beheerde instantie selecteren met de hoeveelheid vCores die overeenkomend geheugen heeft (bijvoorbeeld 5,1 GB/vCore in Gen5). 
- Op basis van de io-latentie basislijn van het bestandssubsysteem u kiezen tussen Algemeen Doel (latentie groter dan 5 ms) en Business Critical-servicelagen (latentie van minder dan 3 ms).
- Op basis van basislijndoorvoer wijst u vooraf de grootte van gegevens of logboekbestanden toe om verwachte IO-prestaties te krijgen.

U reken- en opslagbronnen kiezen tijdens de implementatieen en deze vervolgens achteraf wijzigen zonder downtime voor uw toepassing in te voeren via de [Azure-portal:](sql-database-scale-resources.md)

![beheerde instantiegrootte](./media/sql-database-managed-instance-migration/managed-instance-sizing.png)

Zie [Een beheerde instantie maken](sql-database-managed-instance-get-started.md)voor meer informatie over het maken van de VNet-infrastructuur en een beheerde instantie.

> [!IMPORTANT]
> Het is belangrijk om uw bestemming VNet en subnet altijd in overeenstemming met [de beheerde instantie VNet eisen](sql-database-managed-instance-connectivity-architecture.md#network-requirements)te houden. Elke onverenigbaarheid kan voorkomen dat u nieuwe exemplaren maakt of deze gebruikt die u al hebt gemaakt. Meer informatie over [het maken van nieuwe](sql-database-managed-instance-create-vnet-subnet.md) en het configureren van [bestaande](sql-database-managed-instance-configure-vnet-subnet.md) netwerken.

## <a name="select-migration-method-and-migrate"></a>Migratiemethode selecteren en migreren

De optie beheerde instantieimplementatie is gericht op gebruikersscenario's die massadatabasemigratie vereisen van on-premises of IaaS-database-implementaties. Ze zijn een optimale keuze wanneer u de achterkant van de toepassingen moet optillen en verschuiven die regelmatig gebruik maken van instantieniveau en/of cross-database functionaliteiten. Als dit uw scenario is, u een hele instantie verplaatsen naar een overeenkomstige omgeving in Azure zonder dat u uw toepassingen opnieuw hoeft te ontwerpen.

Als u SQL-exemplaren wilt verplaatsen, moet u zorgvuldig plannen:

- De migratie van alle databases die moeten worden ondergebracht (databases die op dezelfde instantie worden uitgevoerd)
- De migratie van objecten op instantieniveau waarvan uw toepassing afhankelijk is, waaronder aanmeldingen, referenties, SQL Agent-taken en -operators en triggers op serverniveau.

Beheerde instantie is een beheerde service waarmee u een aantal van de reguliere DBA-activiteiten delegeren aan het platform terwijl ze zijn ingebouwd. Daarom hoeven sommige gegevens op instantieniveau niet te worden gemigreerd, zoals onderhoudstaken voor regelmatige back-ups of Always On-configuratie, omdat [hoge beschikbaarheid](sql-database-high-availability.md) is ingebouwd.

Beheerde instantie ondersteunt de volgende opties voor databasemigratie (momenteel zijn dit de enige ondersteunde migratiemethoden):

- Azure Database Migration Service - migratie met bijna nul downtime,
- Native `RESTORE DATABASE FROM URL` - maakt gebruik van native back-ups van SQL Server en vereist enige downtime.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

De [Azure Database Migration Service (DMS)](../dms/dms-overview.md) is een volledig beheerde service die is ontworpen om naadloze migraties van meerdere databasebronnen naar Azure Data-platforms mogelijk te maken met minimale downtime. Deze service stroomlijnt de taken die nodig zijn om bestaande externe en SQL Server-databases naar Azure te verplaatsen. Implementatieopties bij openbare preview omvatten databases in Azure SQL Database- en SQL Server-databases in een Azure Virtual Machine. DMS is de aanbevolen migratiemethode voor uw bedrijfsworkloads.

Als u SQL Server Integration Services (SSIS) on-premises op uw SQL Server gebruikt, ondersteunt DMS nog geen migrerende SSIS-catalogus (SSISDB) die SSIS-pakketten opslaat, maar u Azure-SSIS Integration Runtime (IR) inAzure Data Factory (ADF) inrichten waarmee een nieuwe SSISDB in een beheerde instantie wordt gemaakt en vervolgens u uw pakketten opnieuw implementeren, zie [Azure-SSIS IR maken in ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Zie [Uw on-premises database migreren naar beheerde instantie met DMS](../dms/tutorial-sql-server-to-managed-instance.md)voor meer informatie over dit scenario en configuratiestappen voor DMS.  

### <a name="native-restore-from-url"></a>Systeemeigen HERSTEL via URL

HERSTEL van native back-ups (.bak-bestanden) die afkomstig zijn van SQL Server on-premises of [SQL Server op Virtuele Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/), beschikbaar op Azure [Storage,](https://azure.microsoft.com/services/storage/)is een van de belangrijkste mogelijkheden van de beheerde optie voor het implementeren van instance die snelle en eenvoudige offline databasemigratie mogelijk maakt.

Het volgende diagram geeft een overzicht op hoog niveau van het proces:

![migratiestroom](./media/sql-database-managed-instance-migration/migration-flow.png)

In de volgende tabel vindt u meer informatie over de methoden die u gebruiken, afhankelijk van de bron SQL Server-versie die u uitvoert:

|Stap|SQL Engine en versie|Back-up / herstelmethode|
|---|---|---|
|Back-up maken naar Azure Storage|Voorafgaande SQL 2012 SP1 CU2|.bakbestand rechtstreeks uploaden naar Azure-opslag|
||2012 SP1 CU2 - 2016|Directe back-up met afgeschafte syntaxis [CREDENTIAL](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql)|
||2016 en hoger|Directe back-up [met SAS-referentie](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Herstellen van Azure-opslag naar beheerde instantie|[HERSTELLEN VAN URL met SAS-referentie](sql-database-managed-instance-get-started-restore.md)|

> [!IMPORTANT]
> - Wanneer u een database migreert die is beveiligd door [Transparante gegevensversleuteling](transparent-data-encryption-azure-sql.md) naar een beheerde instantie met behulp van de native restore-optie, moet het bijbehorende certificaat van de on-premises of IaaS SQL Server worden gemigreerd voordat de database wordt hersteld. Zie [TDE-cert migreren naar beheerde instantie](sql-database-managed-instance-migrate-tde-certificate.md) voor gedetailleerde stappen
> - Herstel van systeemdatabases wordt niet ondersteund. Als u objecten op instantieniveau wilt migreren (opgeslagen in master- of msdb-databases), raden we u aan ze uit te schrijven en T-SQL-scripts uit te voeren op de doelinstantie.

Zie [Herstellen van back-up naar een beheerde instantie voor](sql-database-managed-instance-get-started-restore.md)een snelle start met een snel begin waarin wordt weergegeven hoe u een databaseback-up herstellen naar een beheerde instantie.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]


## <a name="monitor-applications"></a>Toepassingen bewaken

Zodra u de migratie naar Beheerde instantie hebt voltooid, moet u het toepassingsgedrag en de prestaties van uw werkbelasting bijhouden. Dit proces omvat de volgende activiteiten:
- [Vergelijk de prestaties van de werkbelasting die wordt uitgevoerd op de beheerde instantie](#compare-performance-with-the-baseline) met de [prestatiebasislijn die u hebt gemaakt op de bron SQL Server.](#create-performance-baseline)
- Controleer [continu de prestaties van uw werklast](#monitor-performance) om potentiële problemen en verbeteringen te identificeren.

### <a name="compare-performance-with-the-baseline"></a>Prestaties vergelijken met de basislijn

De eerste activiteit die u onmiddellijk na een succesvolle migratie moet uitvoeren, is om de prestaties van de werkbelasting te vergelijken met de prestaties van de basislijnwerkbelasting. Het doel van deze activiteit is om te bevestigen dat de werkbelastingprestaties van uw beheerde instantie aan uw behoeften voldoen. 

Databasemigratie naar Beheerde instantie houdt in de meeste gevallen database-instellingen en het oorspronkelijke compatibiliteitsniveau bij. De oorspronkelijke instellingen blijven waar mogelijk behouden om het risico op bepaalde prestatiedegradaties in vergelijking met uw bron SQL Server te verminderen. Als het compatibiliteitsniveau van een gebruikersdatabase 100 of hoger was vóór de migratie, blijft het na migratie hetzelfde. Als het compatibiliteitsniveau van een gebruikersdatabase 90 was vóór de migratie, wordt in de bijgewerkte database het compatibiliteitsniveau ingesteld op 100, het laagste ondersteunde compatibiliteitsniveau in beheerde instantie. Compatibiliteitsniveau van systeemdatabases is 140. Aangezien migratie naar Managed Instance daadwerkelijk migreert naar de nieuwste versie van SQL Server Database Engine, moet u zich ervan bewust zijn dat u de prestaties van uw werkbelasting opnieuw moet testen om een aantal verrassende prestatieproblemen te voorkomen.

Zorg er als voorwaarde voor dat u de volgende activiteiten hebt voltooid:
- Lijn uw instellingen op Managed Instance uit op de instellingen van de sql-server-instantie van de bron door verschillende instantie, database, temdb-instellingen en -configuraties te onderzoeken. Zorg ervoor dat u instellingen zoals compatibiliteitsniveaus of versleuteling niet hebt gewijzigd voordat u de eerste prestatievergelijking uitvoert, of accepteer het risico dat sommige van de nieuwe functies die u hebt ingeschakeld, van invloed kunnen zijn op sommige query's. Als u migratierisico's wilt beperken, wijzigt u het compatibiliteitsniveau van de database pas na prestatiebewaking.
- Implementeer [richtlijnen voor best practice se op de opslag voor algemeen gebruik,](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) zoals het vooraf toewijzen van de grootte van de bestanden om betere prestaties te krijgen.
- Lees meer over de [belangrijkste omgevingsverschillen die de prestatieverschillen tussen Managed Instance en SQL Server kunnen veroorzaken]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) en identificeer de risico's die van invloed kunnen zijn op de prestaties.
- Zorg ervoor dat u queryarchief en automatische afstemming op uw beheerde instantie behoudt. Met deze functies u de prestaties van de werkbelasting meten en de potentiële prestatieproblemen automatisch oplossen. Meer informatie over het gebruik van Query Store als een optimaal hulpmiddel voor het verkrijgen van informatie over de prestaties van de werkbelasting voor en na de wijziging van het databasecompatibiliteitsniveau, zoals uitgelegd in [De prestatiestabiliteit behouden tijdens de upgrade naar nieuwere SQL Server-versie.](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade)
Zodra u de omgeving hebt voorbereid die zoveel mogelijk vergelijkbaar is met uw on-premises omgeving, u beginnen met het uitvoeren van uw werklast en het meten van prestaties. Het meetproces moet dezelfde parameters bevatten die u hebt gemeten [terwijl u basislijnprestaties van uw werkbelastingmetingen op de bron SQL Server maakt.](#create-performance-baseline)
Als gevolg hiervan moet u prestatieparameters vergelijken met de basislijn en kritieke verschillen identificeren.

> [!NOTE]
> In veel gevallen u niet precies overeenkomenmet de prestaties op Managed Instance en SQL Server krijgen. Managed Instance is een SQL Server-databaseengine, maar infrastructuur en configuratie met hoge beschikbaarheid op Managed Instance kunnen enig verschil met zich meemaken. Je zou verwachten dat sommige query's sneller zou zijn, terwijl sommige andere langzamer zou kunnen zijn. Het doel van de vergelijking is om te controleren of de prestaties van de werkbelasting in Managed Instance overeenkomen met de prestaties op SQL Server (gemiddeld) en te identificeren zijn er kritieke query's met de prestaties die niet overeenkomen met uw oorspronkelijke prestaties.

Het resultaat van de prestatievergelijking zou kunnen zijn:
- Workloadprestaties op Managed Instance zijn uitgelijnd of beter dan de workloadprestaties op SQL Server. In dit geval hebt u met succes bevestigd dat migratie is geslaagd.
- De meeste prestatieparameters en de query's in de werkbelasting werken prima, met enkele uitzonderingen met gedegradeerde prestaties. In dit geval moet u de verschillen en het belang ervan identificeren. Als er een aantal belangrijke query's met gedegradeerde prestaties zijn, moet u onderzoeken of de onderliggende SQL-plannen zijn gewijzigd of dat de query's een aantal resourcelimieten raken. Mitigatie in dit geval kan zijn om enkele hints toe te passen op de kritieke query's (bijvoorbeeld gewijzigd compatibiliteitsniveau, legacy kardinaliteitsschatter) of met behulp van plangidsen, om statistieken en indexen die van invloed kunnen zijn op de plannen opnieuw op te bouwen of te maken. 
- De meeste query's zijn trager op Managed Instance in vergelijking met uw bron SQL Server. Probeer in dit geval de onderliggende oorzaken van het verschil te identificeren, zoals [het bereiken van een bronlimiet]( sql-database-managed-instance-resource-limits.md#service-tier-characteristics) zoals IO-limieten, geheugenlimiet, limiet voor instantielogboeken, enz. Als er geen resourcelimieten zijn die het verschil kunnen veroorzaken, probeert u het compatibiliteitsniveau van de database te wijzigen of database-instellingen zoals schatting van de verouderde kardinaliteit te wijzigen en de test opnieuw te starten. Bekijk de aanbevelingen van weergaven van beheerde instantie of queryarchief om de query's te identificeren die de prestaties hebben gewijzigd.

> [!IMPORTANT]
> Managed Instance heeft een ingebouwde automatische plancorrectiefunctie die standaard is ingeschakeld. Deze functie zorgt ervoor dat query's die prima werkten in de pasta in de toekomst niet zouden verslechteren. Zorg ervoor dat deze functie is ingeschakeld en dat u de werkbelasting lang genoeg hebt uitgevoerd met de oude instellingen voordat u nieuwe instellingen wijzigt om Managed Instance meer te weten te komen over de basislijnprestaties en -plannen.

Wijzig de parameters of upgradeservicelagen om te convergeren naar de optimale configuratie totdat u de werkbelastingprestaties krijgt die aan uw behoeften voldoen.

### <a name="monitor-performance"></a>Prestaties bewaken

Managed Instance biedt veel geavanceerde tools voor het bewaken en oplossen van problemen, en u moet ze gebruiken om de prestaties van uw instantie te controleren. Enkele van de parameters die je zou moeten controleren zijn:
- CPU-gebruik op de instantie om te bepalen doet het aantal vCores dat u ingericht is de juiste match voor uw werkbelasting.
- De levensverwachting van de pagina op uw beheerde instantie om te [bepalen, hebt u extra geheugen nodig.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444)
- Wacht statistieken `INSTANCE_LOG_GOVERNOR` `PAGEIOLATCH` zoals of dat zal vertellen heb je storage IO-problemen, vooral op de niveau algemeen doel, waar u mogelijk bestanden vooraf moet toewijzen om betere IO-prestaties te krijgen.

## <a name="leverage-advanced-paas-features"></a>Maak gebruik van geavanceerde PaaS-functies

Zodra u zich op een volledig beheerd platform bevindt en u hebt geverifieerd dat workloadprestaties overeenkomen met de prestaties van uw SQL Server-workload, u profiteren die automatisch worden geleverd als onderdeel van de SQL Database-service. 

Zelfs als u tijdens de migratie geen wijzigingen aanbrengt in beheerde instantie, is de kans groot dat u een aantal van de nieuwe functies inschakelt terwijl u uw instantie inschakelt om te profiteren van de nieuwste verbeteringen in de databaseengine. Sommige wijzigingen worden alleen ingeschakeld nadat het [compatibiliteitsniveau van](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database)de database is gewijzigd.


U hoeft bijvoorbeeld geen back-ups te maken op beheerde instantie - de service voert automatisch back-ups voor u uit. U hoeft zich geen zorgen meer te maken over het plannen, nemen en beheren van back-ups. Beheerde instantie biedt u de mogelijkheid om te herstellen naar elk moment binnen deze bewaarperiode met behulp van [Point in Time Recovery (PITR)](sql-database-recovery-using-backups.md#point-in-time-restore). Bovendien hoeft u zich geen zorgen te maken over het instellen van hoge beschikbaarheid, omdat [hoge beschikbaarheid](sql-database-high-availability.md) is ingebouwd.

Als u de beveiliging wilt versterken, u azure [active directory-verificatie,](sql-database-security-overview.md) [controle,](sql-database-managed-instance-auditing.md) [bedreigingsdetectie,](sql-database-advanced-data-security.md) [rijbeveiliging](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)en [dynamische gegevensmaskering gebruiken.](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking)

Naast geavanceerde beheer- en beveiligingsfuncties biedt Managed Instance een reeks geavanceerde tools waarmee u uw werkbelasting [controleren en afstemmen.](sql-database-monitor-tune-overview.md) [Azure SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) stelt u in staat om een grote set beheerde exemplaren te controleren en de bewaking van een groot aantal exemplaren en databases te centraliseren. [Automatische afstemming](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) in Managed Instance controleert continu de prestaties van uw SQL-planuitvoeringsstatistieken en lost automatisch de geïdentificeerde prestatieproblemen op.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een beheerde instantie voor](sql-database-managed-instance.md)informatie over beheerde instanties? .
- Zie [Een beheerde instantie maken](sql-database-managed-instance-get-started.md)voor een zelfstudie met een back-up herstel.
- Zie [Uw on-premises database migreren naar beheerde instantie met Behulp van DMS](../dms/tutorial-sql-server-to-managed-instance.md)voor zelfstudie met migratie met DMS.  
