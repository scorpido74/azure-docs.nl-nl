---
title: Wat is de service Azure SQL Database?
description: 'Krijg kennis met SQL Database: technische details en mogelijkheden van de micro soft relationele Database Management System (RDBMS) in de Cloud.'
keywords: inleiding in sql,intro in sql,wat is sql-database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: fd9e41418eac670bd1cb52be40dbd25c17af6fac
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255689"
---
# <a name="what-is-the-azure-sql-database-service"></a>Wat is de service Azure SQL Database?

Azure SQL Database is een relationele data base voor algemeen gebruik, die als beheerde service wordt aangestuurd. Met deze oplossing kunt u een Maxi maal beschik bare gegevenslaag met hoge prestaties maken voor de toepassingen en oplossingen in Azure. SQL Database kan de juiste keuze zijn voor een groot aantal moderne Cloud toepassingen, omdat u hiermee zowel relationele gegevens als [niet-relationele structuren](sql-database-multi-model-features.md)kunt verwerken, zoals grafieken, JSON, ruimtelijke en XML.

Het is gebaseerd op de laatste stabiele versie van de [Microsoft SQL server data base-engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). U kunt geavanceerde functies voor het verwerken van query's gebruiken, zoals [hoogwaardige in-Memory technologieën](sql-database-in-memory.md) en [intelligente query verwerking](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json). De nieuwste mogelijkheden van SQL Server worden eerst vrijgegeven voor SQL Database en vervolgens SQL Server zichzelf. U beschikt over de nieuwste SQL Server mogelijkheden zonder overhead voor het uitvoeren van patches of het upgraden, testen over miljoenen data bases. 

Met SQL Database kunt u eenvoudig prestaties in twee verschillende aankoop modellen definiëren en schalen: een [op vCore gebaseerd inkoop model](sql-database-service-tiers-vcore.md) en een [op DTU gebaseerd aankoop model](sql-database-service-tiers-dtu.md). SQL Database is een volledig beheerde service met ingebouwde hoge Beschik baarheid, back-ups en andere veelvoorkomende onderhouds bewerkingen. Micro soft behandelt alle patches en updates van de code van het SQL-en-besturings systeem. U hoeft de onderliggende infra structuur niet te beheren.

> [!NOTE]
> Zie de [woorden lijst voor SQL database termen](sql-database-glossary-terms.md)voor relevante voor waarden en hun definities.

## <a name="deployment-models"></a>Implementatiemodellen

Azure SQL Database biedt de volgende implementatieopties voor een Azure SQL-database:

![Diagram van implementatie opties](./media/sql-database-technical-overview/deployment-options.png)

- [Eén data base](sql-database-single-database.md) vertegenwoordigt een volledig beheerde, geïsoleerde data base. U kunt deze optie gebruiken als u moderne Cloud toepassingen en micro Services hebt die een enkele betrouw bare gegevens bron nodig hebben. Eén data base is vergelijkbaar met een [Inge sloten data base](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) in [Microsoft SQL server data base-engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- Het [beheerde exemplaar](sql-database-managed-instance.md) is een volledig beheerd exemplaar van de [Microsoft SQL server data base-engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). Het bevat een set data bases die samen kunnen worden gebruikt. Gebruik deze optie voor een eenvoudige migratie van on-premises SQL Server data bases naar de Azure-Cloud en voor toepassingen die de database functies moeten gebruiken die SQL Server data base-engine biedt.
- [Elastische pool](sql-database-elastic-pool.md) is een verzameling van [afzonderlijke data bases](sql-database-single-database.md) met een gedeelde set resources, zoals CPU of geheugen. U kunt afzonderlijke data bases naar en uit een elastische pool verplaatsen.

> [!IMPORTANT]
> Zie [SQL database functies](sql-database-features.md)voor meer informatie over de functie verschillen tussen SQL Database en SQL Server en de verschillen tussen de verschillende Azure SQL database implementatie opties.

SQL Database biedt voorspel bare prestaties met meerdere resource typen, service lagen en reken grootten. Het biedt dynamische schaal baarheid zonder uitval tijd, ingebouwde intelligente optimalisatie, wereld wijde schaal baarheid en beschik baarheid en geavanceerde beveiligings opties. Met deze mogelijkheden kunt u zich richten op het ontwikkelen van snelle apps en het versnellen van uw time-to-Market, in plaats van het beheren van virtuele machines en infra structuur. De SQL Database-service bevindt zich momenteel in 38 data centers over de hele wereld, zodat u uw data base kunt uitvoeren in een Data Center bij u in de buurt.

## <a name="scalable-performance-and-pools"></a>Schaalbare prestaties en pools

U kunt de toegewezen hoeveelheid resources definiëren. 
- Met afzonderlijke data bases is elke Data Base van elkaar geïsoleerd en is ze draagbaar. Elk heeft een eigen gegarandeerde hoeveelheid reken-, geheugen-en opslag resources. De hoeveelheid resources die aan de data base is toegewezen, is toegewezen aan die data base en wordt niet gedeeld met andere data bases in Azure. U kunt [afzonderlijke database bronnen](sql-database-single-database-scale.md) dynamisch omhoog en omlaag schalen. De optie voor één Data Base biedt verschillende berekenings-, geheugen-en opslag bronnen voor verschillende behoeften. U kunt bijvoorbeeld 1 tot 80 vCores of 32 GB tot 4 TB krijgen. Met de [grootschalige](sql-database-service-tier-hyperscale.md) voor één Data Base kunt u schalen naar 100 TB, met snelle back-up-en herstel mogelijkheden.
- Met elastische Pools kunt u resources toewijzen die worden gedeeld door alle data bases in de groep. U kunt een nieuwe data base maken of de bestaande afzonderlijke data bases verplaatsen naar een resource groep om het gebruik van resources te maximaliseren en geld te besparen. Met deze optie krijgt u ook de mogelijkheid om [bronnen van elastische Pools](sql-database-elastic-pool-scale.md) dynamisch omhoog en omlaag te schalen.
- Met beheerde instanties wordt elk beheerd exemplaar geïsoleerd van andere instanties met gegarandeerde bronnen. Binnen een beheerd exemplaar delen de exemplaar databases een aantal resources. U kunt [beheerde exemplaar bronnen](sql-database-managed-instance-resource-limits.md) dynamisch omhoog en omlaag schalen.

U kunt uw eerste app bouwen op een kleine, afzonderlijke Data Base met een lage prijs per maand in de laag voor algemeen gebruik. U kunt de servicelaag vervolgens hand matig of via een programma op elk gewenst moment wijzigen in de bedrijfskritische servicelaag om te voldoen aan de behoeften van uw oplossing. U kunt het prestatieniveau aanpassen zonder uitvaltijd voor uw app of voor uw klanten. Doordat de schaalbaarheid dynamisch is, kan uw database op een transparante manier reageren op snel veranderende resourcevereisten. U betaalt alleen voor de resources die u nodig hebt wanneer u ze nodig hebt.

*Dynamische schaal baarheid* wijkt af van *automatisch schalen*. Automatisch schalen vindt plaats wanneer een service automatisch wordt geschaald op basis van criteria, terwijl u met dynamische schaalbaarheid handmatig kunt schalen zonder uitvaltijd. De optie voor één data base ondersteunt hand matige dynamische schaal baarheid, maar niet automatisch schalen. Voor een meer automatische ervaring kunt u gebruikmaken van elastische Pools, waarmee data bases bronnen in een pool kunnen delen op basis van de behoeften van individuele data bases. Een andere optie is het gebruik van scripts waarmee de schaal baarheid voor één data base kan worden geautomatiseerd. Zie voor een voor beeld [Power shell gebruiken om één Data Base te bewaken en te schalen](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Aankoopmodellen

SQL Database biedt de volgende aankoop modellen:
- [Met het op vCore gebaseerde aankoop model](sql-database-service-tiers-vcore.md) kunt u het aantal vCores, de hoeveelheid geheugen en de hoeveelheid en snelheid van de opslag kiezen. Met het op vCore gebaseerde aankoop model kunt u ook Azure Hybrid Benefit gebruiken om [SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) kosten besparingen te verkrijgen. Zie de sectie ' Veelgestelde vragen ' verderop in dit artikel voor meer informatie over de Azure Hybrid Benefit.
- Het [op DTU gebaseerde aankoop model](sql-database-service-tiers-dtu.md) biedt een combi natie van compute-, geheugen-en I/O-resources in drie service lagen, ter ondersteuning van licht tot zware data base-workloads. Reken grootten binnen elke laag bieden een andere combi natie van deze resources, waaraan u extra opslag resources kunt toevoegen.
- Het [serverloze model](sql-database-serverless.md) schaalt automatisch Compute op basis van de werkbelasting vraag en factureert de hoeveelheid reken kracht die per seconde wordt gebruikt. De compute-laag zonder server onderbreekt ook automatisch data bases tijdens inactieve Peri Oden wanneer alleen opslag wordt gefactureerd en hervat automatisch data bases wanneer de activiteit wordt geretourneerd.

### <a name="service-tiers"></a>Servicelagen

Azure SQL Database biedt drie service lagen die zijn ontworpen voor verschillende soorten toepassingen:
- [Algemeen/Standard-](sql-database-service-tier-general-purpose.md) servicelaag ontworpen voor algemene werk belastingen. Het biedt berekenings-en opslag opties op basis van budget gericht evenwicht.
- [Bedrijfskritiek/Premium-](sql-database-service-tier-business-critical.md) servicelaag ontworpen voor OLTP-toepassingen met een hoge transactie frequentie en een minimale latentie-I/O. Het biedt de hoogste flexibiliteit voor storingen door gebruik te maken van verschillende geïsoleerde replica's.
- [Grootschalige](sql-database-service-tier-hyperscale.md) service tier ontworpen voor zeer grote OLTP-data base en de mogelijkheid om opslag en schaal berekening voorzichtig te schalen.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastische pools voor optimaal resourcegebruik

Voor veel bedrijven en toepassingen is het kunnen maken van enkele databases en het naar wens omhoog of omlaag schalen van de prestaties al voldoende, vooral als de gebruikspatronen redelijk voorspelbaar zijn. Met onvoorspelbare gebruiks patronen kunt u de kosten en uw bedrijfs model moeilijk beheren. [Elastische pools](sql-database-elastic-pool.md) zijn ontworpen om dit probleem te verhelpen. U wijst prestatie bronnen toe aan een pool in plaats van een afzonderlijke data base. U betaalt voor de collectieve prestaties van de pool in plaats van voor de prestaties van één data base.

   ![Afbeelding die elastische Pools in Basic-, Standard-en Premium-edities weergeeft](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Met elastische Pools hoeft u zich niet te richten op het omhoog en omlaag plaatsen van database prestaties als de vraag naar resources schommelt. De pooldatabases maken naar behoefte gebruik van de prestatieresources van de elastische pool. Gepoolde data bases worden gebruikt, maar de limieten van de pool worden niet overschreden, waardoor uw kosten voorspelbaar blijven, zelfs als het gebruik van afzonderlijke data bases niet.

U kunt [data bases aan de pool toevoegen en eruit verwijderen](sql-database-elastic-pool-manage-portal.md), en uw app schalen van een aantal data bases tot duizenden, allemaal binnen een budget dat u beheert. U kunt ook de minimale en maximale hoeveelheid beschik bare resources voor data bases in de pool beheren om ervoor te zorgen dat er geen data base in de pool is die alle pool resources gebruikt en dat elke gepoolde Data Base een gegarandeerd minimum aan resources heeft. Zie [ontwerp patronen voor SaaS-toepassingen met meerdere tenants met SQL database](sql-database-design-patterns-multi-tenancy-saas-applications.md)voor meer informatie over ontwerp patronen voor SaaS-toepassingen (Software as a Service) die gebruikmaken van elastische Pools.

Scripts kunnen helpen bij het bewaken en schalen van elastische pools. Zie voor een voor beeld [Power shell gebruiken voor het bewaken en schalen van een elastische SQL-groep in Azure SQL database](scripts/sql-database-monitor-and-scale-pool-powershell.md).

> [!IMPORTANT]
> Een beheerd exemplaar biedt geen ondersteuning voor elastische Pools. In plaats daarvan is een beheerd exemplaar een verzameling exemplaar databases die beheerde exemplaar resources delen.

### <a name="blend-single-databases-with-pooled-databases"></a>Individuele databases combineren met pooldatabases

U kunt afzonderlijke data bases combi neren met elastische Pools en de service lagen van individuele data bases en elastische Pools wijzigen om aan uw situatie aan te passen. U kunt ook andere Azure-Services combi neren en vergelijken met SQL Database om te voldoen aan uw unieke behoeften voor het ontwerpen van apps, de kosten en de efficiëntie van resources te verhogen en nieuwe zakelijke kansen te verg Roten.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Uitgebreide mogelijkheden voor bewaking en waarschuwingen

Azure SQL Database biedt geavanceerde functies voor controle en probleem oplossing waarmee u meer inzicht krijgt in de kenmerken van de werk belasting. Deze functies en hulpprogram ma's zijn onder andere:
 - De ingebouwde bewakings mogelijkheden van de nieuwste versie van SQL Server data base-engine. Hiermee kunt u real-time prestatie inzichten zoeken. 
 - PaaS bewakings mogelijkheden van Azure waarmee u een groot aantal data base-exemplaren kunt bewaken en problemen kunt oplossen.

[Query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store), een ingebouwde SQL Server bewakings functie, registreert de prestaties van uw query's in realtime en stelt u in staat om de mogelijke prestatie problemen en de belangrijkste consumenten te identificeren. Automatische afstemming en aanbevelingen bieden advies over de query's met de teruggedraaide-prestaties en ontbrekende of gedupliceerde indexen. Met automatisch afstemmen in SQL Database kunt u hand matig de scripts Toep assen waarmee de problemen kunnen worden opgelost, of de oplossing laten SQL Database Toep assen. SQL Database kunt ook testen en controleren of de oplossing een deel van de voor delen biedt en de wijziging bewaren of herstellen, afhankelijk van het resultaat. Naast de mogelijkheden van query Store en automatisch afstemmen kunt u standaard [dmv's en XEvent](sql-database-monitoring-with-dmvs.md) gebruiken om de prestaties van de werk belasting te controleren.

Azure biedt ingebouwde hulpprogram ma's voor [prestatie bewaking](sql-database-performance.md) en [waarschuwingen](sql-database-insights-alerts-portal.md) , gecombineerd met prestatie classificaties waarmee u de status van duizenden data bases kunt bewaken. Met deze hulpprogram ma's kunt u snel de gevolgen van het omhoog of omlaag schalen van de schaal bepalen op basis van uw huidige of verwachte prestatie behoeften. Daarnaast kan SQL Database [metrische gegevens en diagnostische logboeken verzenden](sql-database-metrics-diag-logging.md) die de bewaking vergemakkelijken. U kunt SQL Database configureren voor het opslaan van resourcegebruik, werkrollen en sessies, en connectiviteit in een van deze Azure-resources:

- **Azure Storage**: voor het archiveren van grote hoeveelheden telemetriegegevens voor een lage prijs.
- **Azure Event hubs**: voor het integreren van SQL database telemetrie met uw aangepaste bewakings oplossing of dynamische pijp lijnen.
- **Azure monitor-logboeken**: voor een ingebouwde bewakings oplossing met rapportage-, waarschuwings-en beperkende mogelijkheden.

![Diagram van Azure-bewakings architectuur](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Beschikbaarheid

In een traditionele SQL Server omgeving hebt u doorgaans minstens twee machines lokaal ingesteld. Deze machines hebben dezelfde, synchroon gehouden kopieën van de gegevens die u kunt beveiligen tegen een storing van één machine of onderdeel. Deze omgeving biedt hoge Beschik baarheid, maar is niet beschermd tegen een natuur ramp die uw Data Center vernietigt.

Bij herstel na nood gevallen wordt ervan uitgegaan dat een onherstelbare gebeurtenis geografisch is gelokaliseerd om een andere computer of set machines te hebben met een kopie van uw gegevens ver weg. In SQL Server kunt u AlwaysOn-beschikbaarheids groepen gebruiken die worden uitgevoerd in de async-modus om deze mogelijkheid te verkrijgen. Mensen willen vaak niet wachten op replicatie voordat een trans actie wordt doorgevoerd, waardoor er mogelijk gegevens verloren gaan wanneer u niet-geplande failovers uitvoert.

Data bases in de Premium-en Business Critical-service lagen [komen al iets overeen](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) met de synchronisatie van een beschikbaarheids groep. Data bases in lagere service lagen bieden redundantie via opslag door gebruik te maken van een [ander, maar gelijkwaardig mechanisme](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability). Ingebouwde logica helpt bij het beveiligen van één computer storing. De functie actieve geo-replicatie biedt u de mogelijkheid om te beveiligen tegen nood gevallen waarbij een hele regio wordt vernietigd.

Azure-beschikbaarheidszones probeert te beveiligen tegen de storing van één Data Center-gebouw binnen één regio. Het helpt u te beschermen tegen het stroom verlies of het netwerk tot een gebouw. In SQL Database plaatst u de verschillende replica's in verschillende beschikbaarheids zones (verschillende gebouwen, effectief).

De service level agreement [(Sla)](https://azure.microsoft.com/support/legal/sla/) van Azure, mogelijk gemaakt door een wereld wijd netwerk van door micro soft beheerde data centers, zorgt ervoor dat uw app wordt uitgevoerd op 24/7. Het Azure-platform beheert alle data bases volledig en garandeert geen gegevens verlies en een hoog percentage Beschik baarheid van gegevens. Azure verwerkt automatisch patches, back-ups, replicatie, fout detectie, onderliggende mogelijke hardware-, software-of netwerk fouten, het implementeren van oplossingen voor fouten, failovers, database upgrades en andere onderhouds taken. Standaard-beschikbaarheid wordt bereikt door de lagen voor berekeningen en opslag te scheiden. Premium-Beschik baarheid wordt bereikt door Compute en opslag op één knoop punt voor prestaties te integreren en vervolgens technologie te implementeren die vergelijkbaar is met beschikbaarheids groepen. Zie [SQL database Beschik baarheid](sql-database-high-availability.md)voor een volledige bespreking van de mogelijkheden voor hoge Beschik baarheid van Azure SQL database. 

Daarnaast biedt SQL Database ingebouwde functies voor [bedrijfs continuïteit en wereld wijde schaal baarheid](sql-database-business-continuity.md) . Deze omvatten:

- [Automatische back-ups](sql-database-automated-backups.md):

  Met SQL Database worden automatisch volledige, differentiële en transactie logboek back-ups van SQL-data bases uitgevoerd, zodat u naar elk gewenst moment kunt herstellen. Voor afzonderlijke data bases en gepoolde data bases kunt u SQL Database zodanig configureren dat de back-ups van de volledige data base worden opgeslagen Azure Storage voor lange termijn retentie. Voor beheerde instanties kunt u ook back-ups met alleen kopiëren uitvoeren voor lange termijn retentie.

- [Herstel naar](sql-database-recovery-using-backups.md)een bepaald tijdstip:

  Alle SQL Database implementatie opties ondersteunen herstel naar een bepaald tijdstip binnen de automatische Bewaar periode voor back-ups voor alle SQL database.
- [Actieve geo-replicatie](sql-database-active-geo-replication.md):

  Met de opties voor de ene data base en de gepoolde Data Base kunt u Maxi maal vier Lees bare secundaire data bases configureren in dezelfde of wereld wijd gedistribueerde Azure-data centers. Als u bijvoorbeeld een SaaS-toepassing hebt met een catalogus database die een groot aantal gelijktijdige alleen-lezen trans acties heeft, gebruikt u actieve geo-replicatie om de globale lees schaal in te scha kelen. Hiermee verwijdert u knel punten op de primaire die moeten worden gelezen. Voor beheerde instanties gebruikt u groepen voor automatische failover.
- [Groepen voor automatische failover](sql-database-auto-failover-group.md):

  Met alle SQL Database implementatie opties kunt u failover-groepen gebruiken om hoge Beschik baarheid en taak verdeling op wereld wijde schaal in te scha kelen. Dit omvat transparante geo-replicatie en failover van grote sets data bases, elastische groepen en beheerde exemplaren. Met failover-groepen kunt u wereld wijd gedistribueerde SaaS-toepassingen maken, met minimale beheer overhead. Hiermee blijven alle complexe bewaking, route ring en failover-indeling SQL Database.
- [Zone-redundante data bases](sql-database-high-availability.md):

  Met SQL Database kunt u Premium-of business critical-data bases of elastische Pools inrichten in meerdere beschikbaarheids zones. Omdat deze data bases en elastische Pools meerdere redundante replica's hebben voor maximale Beschik baarheid, biedt het plaatsen van deze replica's in meerdere beschikbaarheids zones een hogere tolerantie. Dit omvat de mogelijkheid om automatisch te herstellen vanuit de schaal storingen van het Data Center zonder gegevens verlies.

## <a name="built-in-intelligence"></a>Ingebouwde intelligentie

Met SQL Database krijgt u ingebouwde intelligentie waarmee u de kosten van het uitvoeren en beheren van data bases aanzienlijk kunt verlagen en die de prestaties en beveiliging van uw toepassing maximaliseren. Als miljoenen klanten workloads rond de klok worden uitgevoerd, SQL Database een enorme hoeveelheid telemetriegegevens verzameld en verwerkt, terwijl de privacy van klanten ook volledig wordt geëerbiedigd. Verschillende algoritmen evalueren de telemetriegegevens continu, zodat de service kan leren en aanpassen met uw toepassing.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatisch bewaking en afstemming van prestaties

SQL Database biedt gedetailleerde informatie over de query’s die u wilt bewaken. SQL Database leert over uw database patronen en kunt u uw database schema aanpassen aan uw werk belasting. SQL Database geeft [aanbevelingen voor het afstemmen van de prestaties](sql-database-advisor.md), waarmee u de aanbevolen acties kunt weergeven en uitvoeren.

Een voortdurende bewaking van een Data Base is echter een hard en tijdrovende taak, met name bij het omgaan met veel data bases. [Intelligent Insights](sql-database-intelligent-insights.md) voert deze taak voor u uit door automatisch SQL database prestaties op schaal te controleren. Er wordt u geïnformeerd over problemen bij het afnemen van prestaties, de hoofd oorzaak van elk probleem wordt aangegeven en aanbevelingen voor prestatie verbetering wanneer mogelijk.

Het beheren van een groot aantal data bases is mogelijk niet efficiënt, zelfs niet met alle beschik bare hulpprogram ma's en rapporten die SQL Database en Azure bieden. In plaats van de data base hand matig te controleren en te verfijnen, kunt u overwegen enkele van de bewakings-en afstemmings acties te delegeren voor SQL Database met [Automatische afstemming](sql-database-automatic-tuning.md) SQL Database voert automatisch aanbevelingen, tests en verifieert alle afstemmings acties uit om ervoor te zorgen dat de prestaties verbeteren. Op deze manier kunnen SQL Database automatisch worden aangepast aan uw werk belasting op een gecontroleerde en veilige manier. Automatische afstemming betekent dat de prestaties van uw data base zorgvuldig worden bewaakt en vergeleken voor en na elke afstem actie. Als de prestaties niet worden verbeterd, wordt de afstemmings actie teruggedraaid.

Veel van onze partners waarop [SaaS multi-tenant-apps](sql-database-design-patterns-multi-tenancy-saas-applications.md) worden uitgevoerd op SQL database, zijn afhankelijk van het automatisch afstemmen van de prestaties om ervoor te zorgen dat hun toepassingen stabiele en voorspel bare prestaties hebben. Dankzij deze functie wordt de kans op prestatieproblemen (op welk moment dan ook) aanzienlijk kleiner. Omdat in een deel van hun klant basis ook SQL Server worden gebruikt, gebruiken ze de aanbevelingen voor het indexeren van SQL Database om hun klanten SQL Server te helpen.

Er zijn twee automatisch afstemmings aspecten [beschikbaar in SQL database](sql-database-automatic-tuning.md):

- **Automatisch indexbeheer**: hiermee worden indexen geïdentificeerd die moeten worden toegevoegd aan of verwijderd uit uw database.
- **Automatische plan correctie**: identificeert problematische abonnementen en lost problemen met de prestaties van het SQL-plan op.

### <a name="adaptive-query-processing"></a>Verwerking van adaptieve query’s

U kunt [adaptieve query verwerking](/sql/relational-databases/performance/intelligent-query-processing)gebruiken, inclusief Interleaved uitvoering voor tabel functies met meerdere instructies, feedback over geheugen toekenning in batch modus en adaptieve samen voegingen in batch modus. Elk van deze functies voor adaptieve query verwerking is vergelijkbaar met de technieken ' leren en aanpassen ', waardoor er meer prestatie problemen worden opgelost die te maken hebben met historische onsamenhangende optimalisatie problemen voor query's.

## <a name="advanced-security-and-compliance"></a>Geavanceerde beveiliging en naleving van voorschriften

SQL Database biedt een reeks [ingebouwde functies voor beveiliging en naleving](sql-database-security-overview.md) zodat uw toepassing voldoet aan diverse vereisten op het gebied van beveiliging en de naleving van voorschriften.

> [!IMPORTANT]
> Micro soft heeft Azure SQL Database gecertificeerd (alle implementatie-opties) tegen een aantal nalevings standaarden. Raadpleeg voor meer informatie het [vertrouwens centrum van Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), waar u de meest recente lijst met SQL database nalevings certificeringen kunt vinden.

### <a name="advance-threat-protection"></a>Geavanceerde beveiliging tegen bedreigingen

Geavanceerde gegevens beveiliging is een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Het bevat functionaliteit voor het detecteren en classificeren van gevoelige gegevens, het beheren van uw database problemen en het opsporen van afwijkende activiteiten die kunnen wijzen op een bedreiging voor uw data base. Het biedt één locatie om deze mogelijkheden in te scha kelen en te beheren.

- [Gegevens detectie en-classificatie](sql-database-data-discovery-and-classification.md):

  Deze functie biedt mogelijkheden die zijn ingebouwd in Azure SQL Database voor het detecteren, classificeren, labelen en beveiligen van gevoelige gegevens in uw data bases. Het biedt inzicht in de status van de database classificatie en houdt de toegang tot gevoelige gegevens in de data base en de grenzen van de randen.
- [Evaluatie van beveiligings problemen](sql-vulnerability-assessment.md):

  Met deze service kunt u mogelijke beveiligings problemen met de data base detecteren, bijhouden en helpen oplossen. Deze service biedt u inzicht in de status van de beveiliging en bruikbare stappen om beveiligingsproblemen op te lossen en de beveiliging van uw database te verbeteren.
- [Detectie van bedreigingen](sql-database-threat-detection.md):

  Deze functie detecteert afwijkende activiteiten die ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van uw data base. Hiermee wordt uw database continu gecontroleerd op verdachte activiteiten en wordt u onmiddellijk gewaarschuwd bij mogelijke beveiligingsproblemen, SQL-injectieaanvallen en afwijkende databasetoegangspatronen. Waarschuwingen voor detectie van bedreigingen bieden Details van de verdachte activiteit en aanbevolen actie voor het onderzoeken en oplossen van de dreiging.

### <a name="auditing-for-compliance-and-security"></a>Controles voor naleving en beveiliging

Met [auditing](sql-database-auditing.md) worden database gebeurtenissen bijgehouden en naar een audit logboek in uw Azure Storage-account geschreven. Controle kan u helpen de naleving van de regelgeving te hand haven, database activiteiten te begrijpen en inzicht te krijgen in verschillen en afwijkingen die kunnen wijzen op problemen met het bedrijf of vermoedelijke beveiligings schendingen.

### <a name="data-encryption"></a>Gegevensversleuteling

SQL Database helpt uw gegevens te beveiligen door versleuteling te bieden. Voor gegevens in beweging wordt [trans port Layer Security](https://support.microsoft.com/kb/3135244)gebruikt. Voor Data-at-rest wordt er gebruik gemaakt van [transparante gegevens versleuteling](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Voor gegevens die in gebruik zijn, wordt [altijd versleuteld](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine)gebruikt.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integratie en meervoudige verificatie

Dankzij [Azure Active Directory-integratie](sql-database-aad-authentication.md) kunt u in SQL Database de identiteit van databasegebruikers en andere Microsoft-services centraal beheren. Deze mogelijkheid vereenvoudigt het beheer van machtigingen en verbetert de beveiliging. Azure Active Directory ondersteunt [multi-factor Authentication](sql-database-ssms-mfa-authentication.md) om de beveiliging van gegevens en toepassingen te verg Roten, terwijl een eenmalige aanmeld procedure wordt ondersteund.

## <a name="easy-to-use-tools"></a>Gebruiksvriendelijke hulpprogramma’s

SQL Database maakt het bouwen en onderhouden van toepassingen makkelijker en productiever. Met SQL Database kunt u zich concentreren op dat waar u het beste in bent: fantastische apps bouwen. U kunt SQL Database beheren en ontwikkelen met behulp van hulpprogram ma's en vaardig heden die u al hebt.

- [De Azure Portal](https://portal.azure.com/):

  Een webtoepassing voor het beheren van alle Azure-Services.
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms):

  Een gratis, Download bare client toepassing voor het beheren van een SQL-infra structuur, van SQL Server tot SQL Database.
- [SQL Server Data tools in Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt):

  Een gratis, Download bare client toepassing voor het ontwikkelen van SQL Server relationele data bases, SQL-data bases, integratie Services-pakketten, Analysis Services gegevens modellen en rapporten van Reporting Services.
- [Visual Studio Code](https://code.visualstudio.com/docs):

  Een gratis, Download bare, open-source code-editor voor Windows, macOS en Linux. Het ondersteunt extensies, waaronder de [MSSQL-extensie](https://aka.ms/mssql-marketplace) voor het uitvoeren van query's Microsoft SQL Server, Azure SQL Database en Azure SQL Data Warehouse.

SQL Database biedt ondersteuning voor het bouwen van toepassingen met python, Java, node. js, PHP, Ruby en .NET op macOS, Linux en Windows. SQL Database ondersteunt dezelfde [verbindingsbibliotheken](sql-database-libraries.md) als SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Veelgestelde vragen over SQL Database

### <a name="what-is-the-current-version-of-sql-database"></a>Wat is de huidige versie van SQL Database?

De huidige versie van SQL Database is V12. Versie V11 is buiten gebruik gesteld.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Kan ik bepalen wanneer uitval tijd van patches optreedt?

Nee. De impact van patching is doorgaans niet merkbaar als u [probeert logica](sql-database-develop-overview.md#resiliency) in uw app te gebruiken. Zie [plannen voor Azure-onderhouds gebeurtenissen in Azure SQL database](sql-database-planned-maintenance.md)voor meer informatie.

### <a name="azure-hybrid-benefit-questions"></a>Vragen over Azure Hybrid Benefit

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Zijn er twee gebruiks rechten met Azure Hybrid Benefit voor SQL Server?

U hebt 180 dagen aan dubbele gebruiks rechten van de licentie om ervoor te zorgen dat migraties naadloos worden uitgevoerd. Na deze periode van 180 dagen kunt u de SQL Server licentie in de Cloud alleen gebruiken in SQL Database. U hebt geen rechten voor dubbel gebruik meer on-premises en in de Cloud.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Hoe verschilt Azure Hybrid Benefit voor SQL Server van License Mobility?

We bieden de voor delen van License Mobility aan SQL Server klanten met Software Assurance. Hierdoor kan hun licenties opnieuw worden toegewezen aan de gedeelde servers van een partner. U kunt dit voor deel gebruiken op Azure IaaS en AWS EC2.

Azure Hybrid Benefit voor SQL Server wijkt af van de mobiliteit van licenties op twee belang rijke gebieden:

- Dit biedt economische voor delen voor het verplaatsen van uiterst gevirtualiseerde werk belastingen naar Azure. SQL Server Enterprise Edition-klanten kunnen in de Algemeen SKU vier kernen krijgen in azure voor elke kern waarover ze on-premises beschikken voor zeer gevirtualiseerde toepassingen. Licentie mobiliteit staat geen bijzondere kosten voordelen toe voor het verplaatsen van gevirtualiseerde werk belastingen naar de Cloud.
- Het biedt een PaaS-doel op Azure (SQL Database Managed instance) dat zeer compatibel is met SQL Server on-premises.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Wat zijn de specifieke rechten van de Azure Hybrid Benefit voor SQL Server?

SQL Database-klanten beschikken over de volgende rechten die zijn gekoppeld aan Azure Hybrid Benefit voor SQL Server:

|Licentie footprint|Wat doet Azure Hybrid Benefit voor SQL Server u doen?|
|---|---|
|SQL Server Enterprise Edition core-klanten met SA|<li>Kan het basis tarief betalen op Algemeen of Bedrijfskritiek SKU</li><br><li>1 kern on-premises = 4 kernen in Algemeen SKU</li><br><li>1 kern on-premises = 1 kern geheugen in Bedrijfskritiek SKU</li>|
|SQL Server Standard Edition core-klanten met SA|<li>Het basis tarief voor alleen Algemeen SKU kan worden betaald</li><br><li>1 kern on-premises = 1 kern geheugen in Algemeen SKU</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Contact met het technische team van SQL Server

- [DBA stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): vragen over het beheer van data bases.
- [Stack overflow](https://stackoverflow.com/questions/tagged/sql-server): Stel ontwikkel vragen in.
- [MSDN-Forums](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Stel technische vragen.
- [Feedback](https://aka.ms/sqlfeedback): rapporten van fouten en aanvragen.
- [Reddit](https://www.reddit.com/r/SQLServer/): bespreek SQL Server.

## <a name="next-steps"></a>Volgende stappen

- Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/sql-database/) voor kostprijs vergelijkingen en reken machines met betrekking tot afzonderlijke data bases en elastische Pools.
- Bekijk deze Quick starts om aan de slag te gaan:

  - [Een SQL-database maken in Azure Portal](sql-database-single-database-get-started.md)  
  - [Een SQL-database maken met de Azure CLI](sql-database-get-started-cli.md)
  - [Een SQL-database maken met PowerShell](sql-database-get-started-powershell.md)

- Zie de volgende artikelen voor een reeks Azure CLI- en PowerShell-voorbeelden:
  - [Azure CLI-voorbeelden voor SQL Database](sql-database-cli-samples.md)
  - [Azure PowerShell-voorbeelden voor SQL Database](sql-database-powershell-samples.md)

- Zie [Azure-route kaart voor SQL database](https://azure.microsoft.com/roadmap/?category=databases)voor meer informatie over de nieuwe mogelijkheden die worden aangekondigd.
- Raadpleeg het [Azure SQL database blog](https://azure.microsoft.com/blog/topics/database), waar SQL Server leden van het product team van de blog over SQL database nieuws en functies.

