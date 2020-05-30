---
title: Migreren van SQL Server naar een beheerd exemplaar van Azure SQL
description: Meer informatie over het migreren van een Data Base van SQL Server naar Azure SQL Managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: douglas, carlrab
ms.date: 07/11/2019
ms.openlocfilehash: c0b34e17c202cb060773c53aa5775343ade9c2ee
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84193777"
---
# <a name="sql-server-instance-migration-to-azure-sql-managed-instance"></a>Migratie van SQL Server-exemplaren naar Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

In dit artikel vindt u informatie over de methoden voor het migreren van een exemplaar van SQL Server 2005 of hoger naar [Azure SQL Managed instance](sql-managed-instance-paas-overview.md). Zie [migreren naar een enkele of gegroepeerde Data Base](../database/migrate-to-database-from-sql-server.md)voor informatie over het migreren naar een enkele data base of elastische pool. Raadpleeg de [migratie handleiding voor Azure data base](https://datamigration.microsoft.com/)voor migratie-informatie over het migreren van andere platforms.

> [!NOTE]
> Als u snel een Azure SQL Managed instance wilt starten en proberen, kunt u in plaats van deze pagina naar snel aan de [slag](quickstart-content-reference-guide.md) gaan.

Op hoog niveau ziet het database migratie proces er als volgt uit:

![migratie proces](./media/migrate-to-instance-from-sql-server/migration-process.png)

- [Bepaal de compatibiliteit met SQL Managed instance](#assess-sql-managed-instance-compatibility) waarbij u er zeker van moet zijn dat er geen blokkerende problemen zijn die uw migraties kunnen voor komen.
  - Deze stap omvat ook het maken van de [prestatie basislijn](#create-performance-baseline) om het resource gebruik in uw bron SQL Server exemplaar te bepalen. Deze stap is nodig als u het SQL-beheerde exemplaar met een juiste grootte wilt implementeren en wilt controleren of de prestaties na de migratie niet worden beïnvloed.
- [Connectiviteits opties voor apps kiezen](connect-application-instance.md)
- [Implementeer op een optimale SQL Managed instance](#deploy-to-an-optimally-sized-managed-instance) waarbij u de technische kenmerken (aantal vCores, hoeveelheid geheugen) en de prestatie tier (Bedrijfskritiek, algemeen) van uw door SQL beheerd exemplaar kiest.
- [Selecteer migratie methode en migreer](#select-migration-method-and-migrate) waar u uw data bases migreert met behulp van offline migratie (systeem eigen back-up/herstel, data base-import/export) of online migratie (Data Migration service, transactionele replicatie).
- Controleer [toepassingen](#monitor-applications) om er zeker van te zijn dat u de prestaties verwacht.

> [!NOTE]
> Als u een afzonderlijke Data Base wilt migreren naar een enkele data base of elastische pool, raadpleegt [u een SQL Server-Data Base migreren naar Azure SQL database](../database/migrate-to-database-from-sql-server.md).

## <a name="assess-sql-managed-instance-compatibility"></a>Compatibiliteit met SQL Managed instance evalueren

Bepaal eerst of SQL Managed instance compatibel is met de database vereisten van uw toepassing. SQL Managed instance is ontworpen om eenvoudig lift-en Shift-migratie te bieden voor de meeste bestaande toepassingen die gebruikmaken van SQL Server. Het kan echter ook voor komen dat u functies of mogelijkheden nodig hebt die nog niet worden ondersteund en dat de kosten voor het implementeren van een tijdelijke oplossing te hoog zijn.

Gebruik [Data Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview) om mogelijke compatibiliteits problemen te detecteren die invloed hebben op de database functionaliteit op Azure SQL database. Als er een aantal problemen met de blok kering zijn gerapporteerd, moet u mogelijk rekening houden met een alternatieve optie, zoals [SQL Server op virtuele machines van Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). Enkele voorbeelden:

- Als u rechtstreeks toegang tot het besturings systeem of het bestands systeem nodig hebt, bijvoorbeeld om een derde of aangepaste agents te installeren op dezelfde virtuele machine met SQL Server.
- Als u strikte afhankelijkheid hebt voor functies die nog niet worden ondersteund, zoals FileStream/bestands tabel, poly base en trans acties met meerdere exemplaren.
- Als absoluut een specifieke versie van SQL Server moet blijven (2012, bijvoorbeeld).
- Als uw reken vereisten veel lager zijn dan de aanbiedingen voor beheerde instanties (één vCore, bijvoorbeeld) en de optie voor het samen voegen van data bases is niet acceptabel.

Als u alle geïdentificeerde migratie blokkeringen hebt opgelost en de migratie naar SQL Managed instance hebt voortgezet, moet u er rekening mee houden dat sommige wijzigingen van invloed kunnen zijn op de prestaties van uw workload:

- Een verplicht volledig herstel model en een regel matig geautomatiseerd back-upschema kunnen invloed hebben op de prestaties van uw werk belasting of onderhouds-en ETL-acties als u regel matig eenvoudig of bulksgewijs geregistreerd model hebt gebruikt of back-ups op aanvraag hebt gestopt.
- Verschillende configuraties op server-of database niveau zoals traceer vlaggen of compatibiliteits niveaus
- Nieuwe functies die u gebruikt, zoals TDE (transparent data base Encryption) of groepen voor automatische failover, kunnen invloed hebben op CPU-en IO-gebruik.

SQL Managed instance garandeert een Beschik baarheid van 99,99%, zelfs in kritieke scenario's, waardoor overhead die door deze functies wordt veroorzaakt, niet kan worden uitgeschakeld. Zie [de hoofd oorzaken van SQL Server en Azure SQL Managed instance](https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/)voor meer informatie.

### <a name="create-performance-baseline"></a>Prestatie basislijn maken

Als u de prestaties van uw werk belasting op het beheerde exemplaar wilt vergelijken met de oorspronkelijke werk belasting die wordt uitgevoerd op SQL Server, moet u een basis lijn voor de prestaties maken die wordt gebruikt voor de vergelijking.

De basis lijn voor prestaties is een set para meters zoals gemiddeld/Maxi maal CPU-gebruik, gemiddelde/maximale schijf-i/o-latentie, door Voer, IOPS, gemiddeld/Max. aantal pagina-verwachting, gemiddelde maximale grootte van tempdb. Als u na de migratie vergelijk bare of nog betere para meters wilt hebben, is het belang rijk om de basislijn waarden voor deze para meters te meten en vast te leggen. Naast de systeem parameters moet u een set representatieve query's of de belangrijkste query's in uw workload selecteren en de minimale/gemiddelde/maximale duur, het CPU-gebruik voor de geselecteerde query's meten. Met deze waarden kunt u de prestaties van de werk belasting die wordt uitgevoerd op een beheerd exemplaar, vergelijken met de oorspronkelijke waarden van de bron SQL Server.

Enkele van de para meters die u moet meten op uw SQL Server-exemplaar zijn:

- [Controleer het CPU-gebruik op uw SQL Server-exemplaar](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Monitor-CPU-usage-on-SQL-Server/ba-p/680777#M131) en noteer het gemiddelde en maximum CPU-gebruik.
- [Controleer het geheugen gebruik op uw SQL Server-exemplaar](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-memory-usage) en bepaal de hoeveelheid geheugen die wordt gebruikt door verschillende onderdelen, zoals de buffer groep, de plannings cache, de column-Store-groep, [in-Memory OLTP](https://docs.microsoft.com/sql/relational-databases/in-memory-oltp/monitor-and-troubleshoot-memory-usage?view=sql-server-2017), enzovoort. Bovendien moet u de gemiddelde en piek waarden voor het prestatie meter item page Life verwachting geheugen vinden.
- Controleer het gebruik van de schijf-i/o op de bron SQL Server instantie met [sys. dm_io_virtual_file_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) -weer gave of [prestatie meter items](https://docs.microsoft.com/sql/relational-databases/performance-monitor/monitor-disk-usage).
- Controleer de werk belasting-en query prestaties of uw SQL Server-exemplaar door dynamische beheer weergaven of query Store te controleren als u migreert van SQL Server 2016 + versie. Bepaal de gemiddelde duur en het CPU-gebruik van de belangrijkste query's in uw workload om ze te vergelijken met de query's die worden uitgevoerd op het beheerde exemplaar.

> [!Note]
> Als u een probleem ondervindt met uw werk belasting op SQL Server zoals een hoog CPU-gebruik, een constante geheugen druk, tempdb-of parameterisering-problemen, moet u deze proberen op te lossen in uw bron SQL Server-exemplaar voordat u de basis lijn en migratie onderneemt. Het migreren van bekende problemen naar een nieuwe systeem migh leidt tot onverwachte resultaten en maakt een vergelijking van de prestaties ongeldig.

Als gevolg van deze activiteit moet u de gemiddelde en piek waarden voor CPU, geheugen en i/o-gebruik op uw bron systeem hebben gedocumenteerd, evenals de gemiddelde en maximale duur en het CPU-gebruik van de dominante en de meest kritieke query's in uw werk belasting. U moet deze waarden later gebruiken om de prestaties van uw werk belasting op het beheerde exemplaar te vergelijken met de basislijn prestaties van de werk belasting op de bron SQL Server.

## <a name="deploy-to-an-optimally-sized-managed-instance"></a>Implementeren in een optimale beheerde instantie

Een door SQL beheerd exemplaar is afgestemd op on-premises workloads die worden gepland om naar de cloud te gaan. Er wordt een [Nieuw aankoop model](../database/service-tiers-vcore.md) geïntroduceerd dat meer flexibiliteit biedt bij het selecteren van het juiste niveau van resources voor uw workloads. In de on-premises wereld bent u waarschijnlijk gewend om de grootte van deze werk belastingen te wijzigen met behulp van fysieke kernen en i/o-band breedte. Het inkoop model voor een beheerd exemplaar is gebaseerd op virtuele kernen of ' vCores ', met extra opslag en IO afzonderlijk beschikbaar. Het vCore-model is een eenvoudigere manier om inzicht te krijgen in uw reken vereisten in de Cloud en wat u nu on-premises gebruikt. Met dit nieuwe model kunt u de juiste grootte van uw doel omgeving in de Cloud. Enkele algemene richt lijnen die u kunnen helpen bij het kiezen van de juiste service-laag en-kenmerken, worden hier beschreven:

- Op basis van het CPU-gebruik van de basis lijn kunt u een beheerd exemplaar inrichten dat overeenkomt met het aantal kernen dat u gebruikt op SQL Server. in dat geval moeten de CPU-kenmerken mogelijk worden geschaald om te voldoen aan de [VM-kenmerken waar het beheerde exemplaar is geïnstalleerd](resource-limits.md#hardware-generation-characteristics).
- Kies op basis van het geheugen gebruik van basis lijn [de servicelaag die overeenkomt met het geheugen](resource-limits.md#hardware-generation-characteristics). De hoeveelheid geheugen kan niet rechtstreeks worden gekozen, dus u moet het beheerde exemplaar selecteren met de hoeveelheid vCores die overeenkomt met het geheugen (bijvoorbeeld 5,1 GB/vCore in GEN5).
- Op basis van de IO-latentie basis lijn van het bestand subsysteem kiest u tussen Algemeen (latentie groter dan 5ms) en Bedrijfskritiek service lagen (latentie van minder dan 3 MS).
- Op basis van de door Voer van de basis lijn wordt de grootte van de gegevens of logboek bestanden vooraf toegewezen om de verwachte IO-prestaties te verkrijgen.

U kunt reken-en opslag Resources kiezen tijdens de implementatie en deze daarna wijzigen zonder uitval tijd voor uw toepassing te maken met behulp van de [Azure Portal](../database/scale-resources.md):

![grootte van beheerd exemplaar](./media/migrate-to-instance-from-sql-server/managed-instance-sizing.png)

Zie [een beheerd exemplaar maken](instance-create-quickstart.md)voor meer informatie over het maken van de VNet-infra structuur en een beheerd exemplaar.

> [!IMPORTANT]
> Het is belang rijk om uw doel-VNet en-subnet altijd in overeenstemming te blijven met de [vnet-vereisten voor beheerde exemplaren](connectivity-architecture-overview.md#network-requirements). Eventuele incompatibiliteiten kunnen verhinderen dat u nieuwe instanties maakt of die u al hebt gemaakt. Meer informatie over [het maken van nieuwe en het](virtual-network-subnet-create-arm-template.md) [configureren van bestaande](vnet-existing-add-subnet.md) netwerken.

## <a name="select-migration-method-and-migrate"></a>Migratie methode selecteren en migreren

SQL Managed instance streeft naar gebruikers scenario's met massale data base-migratie van on-premises of Azure VM-database implementaties. Ze zijn een optimale keuze wanneer u de back-end van de toepassingen die regel matig gebruikmaken van het exemplaar niveau en/of de functionaliteit voor meerdere data bases, wilt opheffen en verplaatsen. Als dit het geval is, kunt u een volledige instantie verplaatsen naar een overeenkomende omgeving in azure zonder dat u uw toepassingen opnieuw moet ontwerpen.

Als u SQL-exemplaren wilt verplaatsen, moet u zorgvuldig plannen:

- De migratie van alle data bases die moeten worden Co (die worden uitgevoerd op hetzelfde exemplaar)
- De migratie van objecten op exemplaar niveau waarvan uw toepassing afhankelijk is, zoals aanmeldingen, referenties, SQL-Agent taken en-Opera tors en triggers op server niveau.

SQL Managed instance is een beheerde service waarmee u enkele van de reguliere DBA-activiteiten kunt delegeren aan het platform wanneer ze zijn ingebouwd. Daarom hoeven gegevens op exemplaar niveau niet te worden gemigreerd, zoals onderhouds taken voor reguliere back-ups of AlwaysOn-configuratie, omdat [hoge Beschik baarheid](../database/high-availability-sla.md) is ingebouwd.

SQL Managed instance ondersteunt de volgende opties voor database migratie (momenteel zijn dit de enige migratie methoden die worden ondersteund):

- Azure Database Migration Service-migratie met bijna nul uitval tijd,
- Systeem eigen `RESTORE DATABASE FROM URL` -maakt gebruik van systeem eigen back-ups van SQL Server en vergt enige downtime.

### <a name="azure-database-migration-service"></a>Azure Database Migration Service

De [Azure database Migration service (DMS)](../../dms/dms-overview.md) is een volledig beheerde service die is ontworpen om naadloze migraties van meerdere database bronnen naar Azure-gegevens platforms mogelijk te maken met minimale downtime. Deze service stroomlijnt de taken die nodig zijn om bestaande derden en SQL Server data bases naar Azure te verplaatsen. Implementatie-opties in open bare preview omvatten data bases in Azure SQL Database en SQL Server data bases in een virtuele machine van Azure. DMS is de aanbevolen migratie methode voor uw bedrijfs werkbelastingen.

Als u SQL Server Integration Services (SSIS) op uw SQL Server on-premises gebruikt, wordt er nog geen ondersteuning geboden voor de migratie van SSIS-catalogus (SSISDB) waarin SSIS-pakketten worden opgeslagen, maar u kunt wel Azure-SSIS Integration Runtime (IR) inrichten in Azure Data Factory (ADF) waarmee een nieuwe SSISDB in een beheerd exemplaar wordt gemaakt en u vervolgens uw pakketten opnieuw kunt implementeren, raadpleegt u [Azure-SSIS IR in ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime).

Zie [uw on-premises data base migreren naar een beheerd exemplaar met behulp van DMS](../../dms/tutorial-sql-server-to-managed-instance.md)voor meer informatie over dit scenario en over de configuratie stappen voor DMS.  

### <a name="native-restore-from-url"></a>Systeemeigen HERSTEL via URL

HERSTEL van systeem eigen back-ups (. bak-bestanden) die zijn gemaakt van een SQL Server-exemplaar, beschikbaar op [Azure Storage](https://azure.microsoft.com/services/storage/), is een van de belangrijkste mogelijkheden van een SQL Managed instance die snelle en eenvoudige offline database migratie mogelijk maakt.

In het volgende diagram ziet u een overzicht van het proces:

![migratie-stroom](./media/migrate-to-instance-from-sql-server/migration-flow.png)

De volgende tabel bevat meer informatie over de methoden die u kunt gebruiken, afhankelijk van de bron SQL Server versie die u gebruikt:

|Stap|SQL-engine en-versie|Back-up/herstel methode|
|---|---|---|
|Back-up naar Azure Storage plaatsen|Eerdere SQL 2012 SP1 CU2|Het bak-bestand rechtstreeks uploaden naar Azure Storage|
||2012 SP1 CU2-2016|Directe back-up met afgeschaft [met referentie](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql) syntaxis|
||2016 en hoger|Directe back-ups met behulp [van SAS-referenties](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url)|
|Herstellen vanuit Azure Storage naar een beheerd exemplaar|[HERSTELLEN vanaf URL met SAS-referentie](restore-sample-database-quickstart.md)|

> [!IMPORTANT]
>
> - Bij het migreren van een Data Base die wordt beveiligd door [transparent Data Encryption](../database/transparent-data-encryption-tde-overview.md) naar een beheerd exemplaar met behulp van de systeem eigen terugzet optie, moet het bijbehorende certificaat van de on-premises of de Azure VM-SQL Server worden gemigreerd voordat de data base wordt hersteld. Zie [TDe-certificaat migreren naar een beheerd exemplaar](tde-certificate-migrate.md) voor gedetailleerde stappen.
> - Het herstellen van systeem databases wordt niet ondersteund. Als u objecten op exemplaar niveau (opgeslagen in Master-of msdb-data bases) wilt migreren, raden we u aan deze uit te voeren en T-SQL-scripts op het bestemmings exemplaar uitvoeren.

Zie [herstellen van een back-up naar een beheerd exemplaar](restore-sample-database-quickstart.md)voor een Snelstartgids waarin wordt getoond hoe u een back-up van een Data Base herstelt naar een beheerd exemplaar met behulp van een SAS-referentie.

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

## <a name="monitor-applications"></a>Toepassingen bewaken

Wanneer u de migratie naar een beheerd exemplaar hebt voltooid, moet u het toepassings gedrag en de prestaties van uw workload volgen. Dit proces omvat de volgende activiteiten:

- [Vergelijk de prestaties van de werk belasting die wordt uitgevoerd op het beheerde exemplaar](#compare-performance-with-the-baseline) met de [prestatie basislijn die u hebt gemaakt op de bron SQL Server](#create-performance-baseline).
- [Bewaak de prestaties van uw werk belasting](#monitor-performance) voortdurend om mogelijke problemen en verbeteringen te identificeren.

### <a name="compare-performance-with-the-baseline"></a>Prestaties vergelijken met de basis lijn

De eerste activiteit die u onmiddellijk na een geslaagde migratie moet uitvoeren, is door de prestaties van de werk belasting te vergelijken met de prestaties van de werk belasting van de basis lijn. Het doel van deze activiteit is om te bevestigen dat de prestaties van de werk belasting op uw beheerde exemplaar voldoen aan uw behoeften.

Database migratie naar een beheerd exemplaar houdt de data base-instellingen en het oorspronkelijke compatibiliteits niveau in de meeste gevallen bij. De oorspronkelijke instellingen blijven waar mogelijk bewaard om het risico van bepaalde prestatie verminderingen ten opzichte van de bron SQL Server te verminderen. Als het compatibiliteits niveau van een gebruikers database 100 of hoger was vóór de migratie, blijft deze ongewijzigd na de migratie. Als het compatibiliteits niveau van een gebruikers database 90 was vóór de migratie, in de bijgewerkte data base, wordt het compatibiliteits niveau ingesteld op 100. Dit is het laagste ondersteunde compatibiliteits niveau in het beheerde exemplaar. Het compatibiliteits niveau van de systeem databases is 140. Omdat migratie naar een beheerd exemplaar daad werkelijk naar de nieuwste versie van SQL Server data base-engine wordt gemigreerd, moet u er rekening mee houden dat u de prestaties van uw workload opnieuw moet testen om problemen met verrassende te voor komen.

Zorg ervoor dat u de volgende activiteiten hebt voltooid voor een vereiste:

- Uw instellingen op een beheerd exemplaar uitlijnen met de instellingen van de bron SQL Server instantie door verschillende instanties, Data Base, temdb-instellingen en configuraties te onderzoeken. Zorg ervoor dat u geen instellingen hebt gewijzigd, zoals compatibiliteits niveaus of versleuteling voordat u de eerste prestatie vergelijking uitvoert. u kunt ook het risico accepteren dat sommige van de nieuwe functies die u hebt ingeschakeld, invloed hebben op sommige query's. Als u de migratie Risico's wilt verminderen, wijzigt u het database compatibiliteits niveau pas nadat de prestaties zijn gecontroleerd.
- Implementeer [opslag best practice richt lijnen voor algemeen](https://techcommunity.microsoft.com/t5/DataCAT/Storage-performance-best-practices-and-considerations-for-Azure/ba-p/305525) , zoals het vooraf toewijzen van de grootte van de bestanden om de betere prestaties te verkrijgen.
- Meer informatie over de [belangrijkste omgevings verschillen die de prestatie verschillen kunnen veroorzaken tussen het beheerde exemplaar en het SQL Server]( https://azure.microsoft.com/blog/key-causes-of-performance-differences-between-sql-managed-instance-and-sql-server/) en de Risico's identificeren die van invloed kunnen zijn op de prestaties.
- Zorg ervoor dat het ingeschakelde query archief en automatisch afstemmen op uw beheerde exemplaar blijven staan. Met deze functies kunt u de prestaties van de werk belasting meten en automatisch de mogelijke prestatie problemen oplossen. Meer informatie over het gebruik van query Store als een optimaal hulp programma voor het verkrijgen van informatie over de prestaties van de werk belasting vóór en na de wijziging van het database compatibiliteits niveau, zoals wordt uitgelegd in [stabiliteit van prestaties bewaren tijdens de upgrade naar nieuwere SQL Server versie](https://docs.microsoft.com/sql/relational-databases/performance/query-store-usage-scenarios#CEUpgrade).
Wanneer u de omgeving hebt voor bereid die vergelijkbaar is met uw on-premises omgeving, kunt u beginnen met het uitvoeren van de werk belasting en de prestaties meten. Het meet proces moet dezelfde para meters bevatten die u hebt gemeten [tijdens het maken van de basislijn prestaties van de werk belasting-eenheden op de bron SQL Server](#create-performance-baseline).
Als gevolg hiervan moet u prestatie parameters vergelijken met de basis lijn en kritieke verschillen identificeren.

> [!NOTE]
> In veel gevallen is het niet mogelijk om exact overeenkomende prestaties te verkrijgen voor een beheerd exemplaar en SQL Server. Azure SQL Managed instance is een SQL Server data base-engine, maar de configuratie van de infra structuur en de hoge Beschik baarheid van het beheerde exemplaar kan enige verschillen veroorzaken. Het kan voor komen dat sommige query's sneller worden uitgevoerd, terwijl een deel ervan langzamer kan zijn. Het doel van de vergelijking is te controleren of de prestaties van de werk belasting in het beheerde exemplaar overeenkomen met de prestaties op SQL Server (gemiddeld), en er zijn belang rijke query's met de prestaties die niet overeenkomen met de oorspronkelijke prestaties.

De resultaten van de prestatie vergelijking kunnen er als volgt uitzien:

- De prestaties van de werk belasting op het beheerde exemplaar zijn uitgelijnd of beter dat de prestaties van de werk belasting op SQL Server. In dit geval hebt u bevestigd dat de migratie is gelukt.
- Het meren deel van de prestatie parameters en de query's in de werk belasting gaat prima, met enkele uitzonde ringen met verminderde prestaties. In dit geval moet u de verschillen en hun belang identificeren. Als er sprake is van een aantal belang rijke query's met verminderde prestaties, moet u onderzoeken of de onderliggende SQL-plannen zijn gewijzigd of dat de query's enkele resource limieten hebben bereikt. Dit probleem kan worden veroorzaakt door een aantal hints toe te passen op de essentiële query's (bijvoorbeeld gewijzigde compatibiliteits niveau, verouderde cardinality Estimator) ofwel rechtstreeks hetzij met plan richtlijnen, opnieuw te opbouwen of door statistieken en indexen te maken die van invloed kunnen zijn op de plannen.
- De meeste query's zijn langzamer in het beheerde exemplaar vergeleken met de bron SQL Server. In dit geval probeert de hoofd oorzaken van het verschil te identificeren, zoals het [bereiken van een bepaalde resource limiet]( resource-limits.md#service-tier-characteristics) , zoals i/o-limieten, geheugen limieten, limieten voor het aantal exemplaren, enzovoort. Als er geen resource limieten zijn die het verschil kunnen veroorzaken, probeert u het compatibiliteits niveau van de data base te wijzigen of data base-instellingen te wijzigen, zoals de schatting van verouderde kardinaliteit en de test opnieuw te starten. Bekijk de aanbevelingen van het beheerde exemplaar of query Store-weer gaven om de query's te identificeren die de prestaties van teruggedraaide.

> [!IMPORTANT]
> Een door Azure SQL beheerd exemplaar heeft een ingebouwde functie voor automatische correctie van plannen die standaard is ingeschakeld. Deze functie zorgt ervoor dat query's die goed werken in het plakken, in de toekomst niet worden gedegradeerd. Zorg ervoor dat deze functie is ingeschakeld en dat u de werk belasting lang genoeg hebt uitgevoerd met de oude instellingen voordat u nieuwe instellingen wijzigt om beheerde exemplaren in te scha kelen voor meer informatie over de basislijn prestaties en-plannen.

Breng de wijziging van de para meters aan of werk de service lagen bij om de optimale configuratie te convergeren tot u de werk belasting krijgt die aan uw behoeften voldoet.

### <a name="monitor-performance"></a>Prestaties bewaken

SQL Managed instance biedt veel geavanceerde hulpprogram ma's voor het controleren en oplossen van problemen, en u moet ze gebruiken om de prestaties van uw exemplaar te bewaken. Enkele van de para meters die u moet bewaken, zijn:

- CPU-gebruik op het exemplaar om te bepalen heeft het aantal vCores dat u hebt ingericht, het juiste resultaat voor uw werk belasting.
- Pagina-Life verwachting op uw beheerde instantie om te bepalen hebt [u meer geheugen nodig](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Do-you-need-more-memory-on-Azure-SQL-Managed-Instance/ba-p/563444).
- Wacht `INSTANCE_LOG_GOVERNOR` `PAGEIOLATCH` op de statistieken, zoals of die aangeven dat u opslag-i/o-problemen hebt, met name voor het algemeen niveau waar u mogelijk bestanden vooraf moet toewijzen om betere IO-prestaties te krijgen.

## <a name="leverage-advanced-paas-features"></a>Maak gebruik van geavanceerde PaaS-functies

Als u zich op een volledig beheerd platform bevindt en u hebt gecontroleerd of de prestaties van de werk belasting overeenkomen met uw SQL Server de werk belasting, moet u de voor delen die automatisch worden gegeven als onderdeel van de service, uitvoeren.

Zelfs als u tijdens de migratie geen wijzigingen aanbrengt in het beheerde exemplaar, is er een hoge kans dat u een aantal van de nieuwe functies inschakelt terwijl u uw exemplaar gaat gebruiken om te profiteren van de nieuwste verbeteringen in de data base-engine. Sommige wijzigingen zijn alleen ingeschakeld wanneer het [database compatibiliteits niveau is gewijzigd](https://docs.microsoft.com/sql/relational-databases/databases/view-or-change-the-compatibility-level-of-a-database).

U hoeft bijvoorbeeld geen back-ups te maken op een beheerd exemplaar. de service voert automatisch back-ups voor u uit. U hoeft zich geen zorgen meer te maken over het plannen, maken en beheren van back-ups. Met Managed Instance kunt u op elk gewenst moment binnen deze Bewaar periode herstellen met behulp van [PITR (Point in time Recovery)](../database/recovery-using-backups.md#point-in-time-restore). Bovendien hoeft u zich geen zorgen te maken over het instellen van hoge Beschik baarheid als [hoge Beschik baarheid](../database/high-availability-sla.md) is ingebouwd.

U kunt de beveiliging versterken door [Azure Active Directory verificatie](../database/security-overview.md), [controle](auditing-configure.md), [detectie van bedreigingen](../database/advanced-data-security.md), [beveiliging op rijniveau](https://docs.microsoft.com/sql/relational-databases/security/row-level-security)en [dynamische gegevens maskering](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking) te gebruiken).

Naast geavanceerde beheer-en beveiligings functies biedt het beheerde exemplaar een set geavanceerde hulp middelen waarmee u [uw werk belasting kunt bewaken en afstemmen](../database/monitor-tune-overview.md). Met [Azure SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) kunt u een grote set beheerde exemplaren bewaken en de bewaking van een groot aantal exemplaren en data bases centraliseren. [Automatisch afstemmen](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning#automatic-plan-correction) in Managed instance bewaakt voortdurend de prestaties van de uitvoerings statistieken van uw SQL-plan en corrigeert de geïdentificeerde prestatie problemen automatisch.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is Azure SQL Managed instance?](sql-managed-instance-paas-overview.md)voor meer informatie over Azure SQL Managed instance.
- Zie [een Azure SQL Managed instance maken](instance-create-quickstart.md)voor een zelf studie met een back-up.
- Zie [uw on-premises data base migreren naar Azure SQL Managed instance met behulp van DMS voor een](../../dms/tutorial-sql-server-to-managed-instance.md)zelf studie over migratie met behulp van DMS.  
