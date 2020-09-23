---
title: Wat is de service Azure SQL Database?
description: 'Krijg kennis met SQL Database: technische details en mogelijkheden van de micro soft relationele Database Management System (RDBMS) in de Cloud.'
keywords: inleiding in sql,intro in sql,wat is sql-database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/21/2020
ms.openlocfilehash: 38f52178ec9c736f3ee51839678401753365d48d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907059"
---
# <a name="what-is-azure-sql-database"></a>Wat is Azure SQL Database?
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database is een volledig beheerde PaaS-data base-engine (platform as a Service) waarmee de meeste database beheer functies, zoals upgrades, patches, back-ups en bewaking, zonder tussen komst van de gebruiker worden verwerkt. Azure SQL Database is altijd actief op de nieuwste stabiele versie van de SQL Server data base-engine en het besturings systeem patches met 99,99% Beschik baarheid. Met de PaaS-mogelijkheden die zijn ingebouwd in Azure SQL Database kunt u zich richten op de Domain-specifieke database beheer-en optimalisatie activiteiten die essentieel zijn voor uw bedrijf.

Met Azure SQL Database kunt u een Maxi maal beschik bare gegevenslaag met hoge prestaties maken voor de toepassingen en oplossingen in Azure. SQL Database kan de juiste keuze zijn voor een groot aantal moderne Cloud toepassingen, omdat u hiermee zowel relationele gegevens als [niet-relationele structuren](../multi-model-features.md)kunt verwerken, zoals grafieken, JSON, ruimtelijke en XML.

Azure SQL Database is gebaseerd op de laatste stabiele versie van de [Microsoft SQL server data base-engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json). U kunt geavanceerde functies voor het verwerken van query's gebruiken, zoals [hoogwaardige in-Memory technologieën](../in-memory-oltp-overview.md) en [intelligente query verwerking](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json). De nieuwste mogelijkheden van SQL Server worden eerst vrijgegeven voor SQL Database en vervolgens SQL Server zichzelf. U beschikt over de nieuwste SQL Server mogelijkheden zonder overhead voor het uitvoeren van patches of het upgraden, testen over miljoenen data bases. 

Met SQL Database kunt u eenvoudig prestaties in twee verschillende aankoop modellen definiëren en schalen: een [op vCore gebaseerd inkoop model](service-tiers-vcore.md) en een [op DTU gebaseerd aankoop model](service-tiers-dtu.md). SQL Database is een volledig beheerde service met ingebouwde hoge Beschik baarheid, back-ups en andere veelvoorkomende onderhouds bewerkingen. Micro soft behandelt alle patches en updates van de code van het SQL-en-besturings systeem. U hoeft de onderliggende infra structuur niet te beheren.

Als u niet bekend bent met Azure SQL Database, raadpleegt u de *Azure SQL database Overview* video van onze diep gaande [Azure SQL-video reeks](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/Azure-SQL-Database-Overview-7-of-61/player]

## <a name="deployment-models"></a>Implementatiemodellen

Azure SQL Database biedt de volgende implementatie opties voor een Data Base:

- [Eén data base](single-database-overview.md) vertegenwoordigt een volledig beheerde, geïsoleerde data base. U kunt deze optie gebruiken als u moderne Cloud toepassingen en micro Services hebt die een enkele betrouw bare gegevens bron nodig hebben. Eén data base is vergelijkbaar met een [Inge sloten data base](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) in de [SQL server data base-engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- [Elastische pool](elastic-pool-overview.md) is een verzameling van [afzonderlijke data bases](single-database-overview.md) met een gedeelde set resources, zoals CPU of geheugen. U kunt afzonderlijke data bases naar en uit een elastische pool verplaatsen.

> [!IMPORTANT]
> Zie [SQL database functies](features-comparison.md)voor meer informatie over de functie verschillen tussen SQL Database en SQL Server, evenals de verschillen tussen de verschillende Azure SQL database opties.

SQL Database biedt voorspel bare prestaties met meerdere resource typen, service lagen en reken grootten. Het biedt dynamische schaal baarheid zonder uitval tijd, ingebouwde intelligente optimalisatie, wereld wijde schaal baarheid en beschik baarheid en geavanceerde beveiligings opties. Met deze mogelijkheden kunt u zich richten op het ontwikkelen van snelle apps en het versnellen van uw time-to-Market, in plaats van het beheren van virtuele machines en infra structuur. SQL Database bevindt zich momenteel in 38 data centers over de hele wereld, zodat u uw data base kunt uitvoeren in een Data Center bij u in de buurt.

## <a name="scalable-performance-and-pools"></a>Schaalbare prestaties en pools

U kunt de toegewezen hoeveelheid resources definiëren. 
- Met afzonderlijke data bases is elke Data Base van elkaar geïsoleerd en is ze draagbaar. Elk heeft een eigen gegarandeerde hoeveelheid reken-, geheugen-en opslag resources. De hoeveelheid resources die aan de data base is toegewezen, is toegewezen aan die data base en wordt niet gedeeld met andere data bases in Azure. U kunt [afzonderlijke database bronnen](single-database-scale.md) dynamisch omhoog en omlaag schalen. De optie voor één Data Base biedt verschillende berekenings-, geheugen-en opslag bronnen voor verschillende behoeften. U kunt bijvoorbeeld 1 tot 80 vCores of 32 GB tot 4 TB krijgen. Met de [grootschalige](service-tier-hyperscale.md) voor afzonderlijke data bases kunt u schalen naar 100 TB, met snelle back-up-en herstel mogelijkheden.
- Met elastische Pools kunt u resources toewijzen die worden gedeeld door alle data bases in de groep. U kunt een nieuwe data base maken of de bestaande afzonderlijke data bases verplaatsen naar een resource groep om het gebruik van resources te maximaliseren en geld te besparen. Met deze optie krijgt u ook de mogelijkheid om [bronnen van elastische Pools](elastic-pool-scale.md) dynamisch omhoog en omlaag te schalen.

U kunt uw eerste app bouwen op een kleine, afzonderlijke Data Base met een lage prijs per maand in de laag voor algemeen gebruik. U kunt de servicelaag vervolgens hand matig of via een programma op elk gewenst moment wijzigen in de bedrijfskritische servicelaag om te voldoen aan de behoeften van uw oplossing. U kunt het prestatieniveau aanpassen zonder uitvaltijd voor uw app of voor uw klanten. Doordat de schaalbaarheid dynamisch is, kan uw database op een transparante manier reageren op snel veranderende resourcevereisten. U betaalt alleen voor de resources die u nodig hebt wanneer u ze nodig hebt.

*Dynamische schaal baarheid* wijkt af van *automatisch schalen*. Automatisch schalen vindt plaats wanneer een service automatisch wordt geschaald op basis van criteria, terwijl u met dynamische schaalbaarheid handmatig kunt schalen zonder uitvaltijd. De optie voor één data base ondersteunt hand matige dynamische schaal baarheid, maar niet automatisch schalen. Voor een meer automatische ervaring zou u elastische pools kunnen gebruiken. Hiermee kunnen databases resources in een pool delen op basis van afzonderlijke databasebehoeften. Een andere optie is het gebruik van scripts waarmee de schaal baarheid voor één data base kan worden geautomatiseerd. Zie voor een voor beeld [Power shell gebruiken om één Data Base te bewaken en te schalen](scripts/monitor-and-scale-database-powershell.md).

### <a name="purchasing-models"></a>Aankoopmodellen

SQL Database biedt de volgende aankoop modellen:
- [Met het op vCore gebaseerde aankoop model](service-tiers-vcore.md) kunt u het aantal vCores, de hoeveelheid geheugen en de hoeveelheid en snelheid van de opslag kiezen. Met het op vCore gebaseerde aankoop model kunt u ook Azure Hybrid Benefit gebruiken om [SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) kosten besparingen te verkrijgen. Zie de sectie ' Veelgestelde vragen ' verderop in dit artikel voor meer informatie over de Azure Hybrid Benefit.
- Het [op DTU gebaseerde aankoop model](service-tiers-dtu.md) biedt een combi natie van compute-, geheugen-en I/O-resources in drie service lagen, ter ondersteuning van licht tot zware data base-workloads. Reken grootten binnen elke laag bieden een andere combi natie van deze resources, waaraan u extra opslag resources kunt toevoegen.
- Het [serverloze model](serverless-tier-overview.md) schaalt automatisch Compute op basis van de werkbelasting vraag en factureert de hoeveelheid reken kracht die per seconde wordt gebruikt. De compute-laag zonder server onderbreekt ook automatisch data bases tijdens inactieve Peri Oden wanneer alleen opslag wordt gefactureerd en hervat automatisch data bases wanneer de activiteit wordt geretourneerd.

### <a name="service-tiers"></a>Servicelagen

Azure SQL Database biedt drie service lagen die zijn ontworpen voor verschillende soorten toepassingen:
- [Algemeen/Standard-](service-tier-general-purpose.md) servicelaag ontworpen voor algemene werk belastingen. Het biedt berekenings-en opslag opties op basis van budget gericht evenwicht.
- [Bedrijfskritiek/Premium-](service-tier-business-critical.md) servicelaag ontworpen voor OLTP-toepassingen met een hoge transactie frequentie en een minimale latentie-I/O. Het biedt de hoogste flexibiliteit voor storingen door gebruik te maken van verschillende geïsoleerde replica's.
- [Grootschalige](service-tier-hyperscale.md) service tier ontworpen voor zeer grote OLTP-data base en de mogelijkheid om opslag en schaal berekening voorzichtig te schalen.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastische pools voor optimaal resourcegebruik

Voor veel bedrijven en toepassingen is het kunnen maken van enkele databases en het naar wens omhoog of omlaag schalen van de prestaties al voldoende, vooral als de gebruikspatronen redelijk voorspelbaar zijn. Met onvoorspelbare gebruiks patronen kunt u de kosten en uw bedrijfs model moeilijk beheren. [Elastische pools](elastic-pool-overview.md) zijn ontworpen om dit probleem te verhelpen. U wijst prestatie bronnen toe aan een pool in plaats van een afzonderlijke data base. U betaalt voor de collectieve prestaties van de pool in plaats van voor de prestaties van één data base.

   ![Afbeelding die elastische Pools in Basic-, Standard-en Premium-edities weergeeft](./media/sql-database-paas-overview/sqldb_elastic_pools.png)

Met elastische Pools hoeft u zich niet te richten op het omhoog en omlaag plaatsen van database prestaties als de vraag naar resources schommelt. De pooldatabases maken naar behoefte gebruik van de prestatieresources van de elastische pool. Gepoolde data bases worden gebruikt, maar de limieten van de pool worden niet overschreden, waardoor uw kosten voorspelbaar blijven, zelfs als het gebruik van afzonderlijke data bases niet.

U kunt [data bases aan de pool toevoegen en eruit verwijderen](elastic-pool-overview.md), en uw app schalen van een aantal data bases tot duizenden, allemaal binnen een budget dat u beheert. U kunt ook de minimale en maximale hoeveelheid beschik bare resources voor data bases in de pool beheren om ervoor te zorgen dat er geen data base in de pool is die alle pool resources gebruikt en dat elke gepoolde Data Base een gegarandeerd minimum aan resources heeft. Zie [ontwerp patronen voor SaaS-toepassingen met meerdere tenants met SQL database](saas-tenancy-app-design-patterns.md)voor meer informatie over ontwerp patronen voor SaaS-toepassingen (Software as a Service) die gebruikmaken van elastische Pools.

Scripts kunnen helpen bij het bewaken en schalen van elastische pools. Zie voor een voor beeld [Power shell gebruiken voor het controleren en schalen van een elastische pool in Azure SQL database](scripts/monitor-and-scale-pool-powershell.md).


### <a name="blend-single-databases-with-pooled-databases"></a>Individuele databases combineren met pooldatabases

U kunt afzonderlijke data bases combi neren met elastische Pools en de service lagen van individuele data bases en elastische Pools wijzigen om aan uw situatie aan te passen. U kunt ook andere Azure-Services combi neren en vergelijken met SQL Database om te voldoen aan uw unieke behoeften voor het ontwerpen van apps, de kosten en de efficiëntie van resources te verhogen en nieuwe zakelijke kansen te verg Roten.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Uitgebreide mogelijkheden voor bewaking en waarschuwingen

Azure SQL Database biedt geavanceerde functies voor controle en probleem oplossing waarmee u meer inzicht krijgt in de kenmerken van de werk belasting. Deze functies en hulpprogram ma's zijn onder andere:
 - De ingebouwde bewakings mogelijkheden die worden geboden door de nieuwste versie van de SQL Server data base-engine. Hiermee kunt u real-time prestatie inzichten zoeken. 
 - PaaS bewakings mogelijkheden van Azure waarmee u een groot aantal data base-exemplaren kunt bewaken en problemen kunt oplossen.

[Query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store), een ingebouwde SQL Server bewakings functie, registreert de prestaties van uw query's in realtime en stelt u in staat om de mogelijke prestatie problemen en de belangrijkste consumenten te identificeren. Automatische afstemming en aanbevelingen bieden advies over de query's met de teruggedraaide-prestaties en ontbrekende of gedupliceerde indexen. Met automatisch afstemmen in SQL Database kunt u hand matig de scripts Toep assen waarmee de problemen kunnen worden opgelost, of de oplossing laten SQL Database Toep assen. SQL Database kunt ook testen en controleren of de oplossing een deel van de voor delen biedt en de wijziging bewaren of herstellen, afhankelijk van het resultaat. Naast de mogelijkheden van query Store en automatisch afstemmen kunt u standaard [dmv's en XEvent](monitoring-with-dmvs.md) gebruiken om de prestaties van de werk belasting te controleren.

Azure biedt ingebouwde hulpprogram ma's voor [prestatie bewaking](performance-guidance.md) en [waarschuwingen](alerts-insights-configure-portal.md) , gecombineerd met prestatie classificaties waarmee u de status van duizenden data bases kunt bewaken. Met deze hulpprogram ma's kunt u snel de gevolgen van het omhoog of omlaag schalen van de schaal bepalen op basis van uw huidige of verwachte prestatie behoeften. Daarnaast kunnen SQL Database [metrische gegevens en bron logboeken verzenden](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) voor een betere controle. U kunt SQL Database configureren voor het opslaan van resourcegebruik, werkrollen en sessies, en connectiviteit in een van deze Azure-resources:

- **Azure Storage**: voor het archiveren van grote hoeveel heden telemetrie voor een kleine prijs.
- **Azure Event hubs**: voor het integreren van SQL database telemetrie met uw aangepaste bewakings oplossing of dynamische pijp lijnen.
- **Azure monitor-logboeken**: voor een ingebouwde bewakings oplossing met rapportage-, waarschuwings-en beperkende mogelijkheden.

![Diagram van Azure-bewakings architectuur](./media/sql-database-paas-overview/architecture.png)

## <a name="availability-capabilities"></a>Beschikbaarheid

Met Azure SQL Database kan uw bedrijf tijdens onderbrekingen blijven werken. In een traditionele SQL Server omgeving hebt u doorgaans minstens twee machines lokaal ingesteld. Deze machines hebben dezelfde, synchroon gehouden kopieën van de gegevens die u kunt beveiligen tegen een storing van één machine of onderdeel. Deze omgeving biedt hoge Beschik baarheid, maar is niet beschermd tegen een natuur ramp die uw Data Center vernietigt.

Bij herstel na nood gevallen wordt ervan uitgegaan dat een onherstelbare gebeurtenis geografisch is gelokaliseerd om een andere computer of set machines te hebben met een kopie van uw gegevens ver weg. In SQL Server kunt u AlwaysOn-beschikbaarheids groepen gebruiken die worden uitgevoerd in de async-modus om deze mogelijkheid te verkrijgen. Mensen willen vaak niet wachten op replicatie voordat een trans actie wordt doorgevoerd, waardoor er mogelijk gegevens verloren gaan wanneer u niet-geplande failovers uitvoert.

Data bases in de service lagen Premium en Bedrijfskritiek [doen er al ongeveer hetzelfde uit](high-availability-sla.md#premium-and-business-critical-service-tier-availability) als de synchronisatie van een beschikbaarheids groep. Data bases in lagere service lagen bieden redundantie via opslag door gebruik te maken van een [ander, maar gelijkwaardig mechanisme](high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability). Ingebouwde logica helpt bij het beveiligen van één computer storing. De functie actieve geo-replicatie biedt u de mogelijkheid om te beveiligen tegen nood gevallen waarbij een hele regio wordt vernietigd.

Azure-beschikbaarheidszones probeert te beveiligen tegen de storing van één Data Center-gebouw binnen één regio. Het helpt u te beschermen tegen het stroom verlies of het netwerk tot een gebouw. In SQL Database plaatst u de verschillende replica's in verschillende beschikbaarheids zones (verschillende gebouwen, effectief).

De service level agreement [(Sla)](https://azure.microsoft.com/support/legal/sla/) van Azure, mogelijk gemaakt door een wereld wijd netwerk van door micro soft beheerde data centers, zorgt ervoor dat uw app wordt uitgevoerd op 24/7. Het Azure-platform beheert alle data bases volledig en garandeert geen gegevens verlies en een hoog percentage Beschik baarheid van gegevens. Azure verwerkt automatisch patches, back-ups, replicatie, fout detectie, onderliggende mogelijke hardware-, software-of netwerk fouten, het implementeren van oplossingen voor fouten, failovers, database upgrades en andere onderhouds taken. Standaard-beschikbaarheid wordt bereikt door de lagen voor berekeningen en opslag te scheiden. Premium-Beschik baarheid wordt bereikt door Compute en opslag op één knoop punt voor prestaties te integreren en vervolgens technologie te implementeren die vergelijkbaar is met beschikbaarheids groepen. Zie [SQL database Beschik baarheid](high-availability-sla.md)voor een volledige bespreking van de mogelijkheden voor hoge Beschik baarheid van Azure SQL database. 

Daarnaast biedt SQL Database ingebouwde functies voor [bedrijfs continuïteit en wereld wijde schaal baarheid](business-continuity-high-availability-disaster-recover-hadr-overview.md) . Deze omvatten:

- [Automatische back-ups](automated-backups-overview.md):

  Met SQL Database worden automatisch volledige, differentiële en transactie logboek back-ups van data bases uitgevoerd, zodat u naar elk gewenst moment kunt herstellen. Voor afzonderlijke data bases en gepoolde data bases kunt u SQL Database zodanig configureren dat de back-ups van de volledige data base worden opgeslagen Azure Storage voor lange termijn retentie. Voor beheerde instanties kunt u ook back-ups met alleen kopiëren uitvoeren voor lange termijn retentie.

- [Herstel naar](recovery-using-backups.md)een bepaald tijdstip:

  Alle SQL Database implementatie opties ondersteunen herstel naar een bepaald tijdstip binnen de automatische Bewaar periode voor back-ups voor elke Data Base.
- [Actieve geo-replicatie](active-geo-replication-overview.md):

  Met de opties voor de ene data base en de gepoolde Data Base kunt u Maxi maal vier Lees bare secundaire data bases configureren in dezelfde of wereld wijd gedistribueerde Azure-data centers. Als u bijvoorbeeld een SaaS-toepassing hebt met een catalogus database die een groot aantal gelijktijdige alleen-lezen trans acties heeft, gebruikt u actieve geo-replicatie om de globale lees schaal in te scha kelen. Hiermee verwijdert u knel punten op de primaire die moeten worden gelezen. Voor beheerde instanties gebruikt u groepen voor automatische failover.
- [Groepen voor automatische failover](auto-failover-group-overview.md):

  Met alle SQL Database implementatie opties kunt u failover-groepen gebruiken om hoge Beschik baarheid en taak verdeling op wereld wijde schaal in te scha kelen. Dit omvat transparante geo-replicatie en failover van grote sets data bases, elastische groepen en beheerde exemplaren. Met failover-groepen kunt u wereld wijd gedistribueerde SaaS-toepassingen maken, met minimale beheer overhead. Hiermee blijven alle complexe bewaking, route ring en failover-indeling SQL Database.
- [Zone-redundante data bases](high-availability-sla.md):

  Met SQL Database kunt u Premium-of Bedrijfskritiek-data bases of elastische Pools inrichten in meerdere beschikbaarheids zones. Omdat deze data bases en elastische Pools meerdere redundante replica's hebben voor maximale Beschik baarheid, biedt het plaatsen van deze replica's in meerdere beschikbaarheids zones een hogere tolerantie. Dit omvat de mogelijkheid om automatisch te herstellen vanuit de schaal storingen van het Data Center zonder gegevens verlies.

## <a name="built-in-intelligence"></a>Ingebouwde intelligentie

Met SQL Database krijgt u ingebouwde intelligentie waarmee u de kosten van het uitvoeren en beheren van data bases aanzienlijk kunt verlagen en die de prestaties en beveiliging van uw toepassing maximaliseren. Als miljoenen klanten workloads rond de klok worden uitgevoerd, SQL Database een enorme hoeveelheid telemetriegegevens verzameld en verwerkt, terwijl de privacy van klanten ook volledig wordt geëerbiedigd. Verschillende algoritmen evalueren de telemetriegegevens continu, zodat de service kan leren en aanpassen met uw toepassing.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatisch bewaking en afstemming van prestaties

SQL Database biedt gedetailleerde informatie over de query’s die u wilt bewaken. SQL Database leert over uw database patronen en kunt u uw database schema aanpassen aan uw werk belasting. SQL Database geeft [aanbevelingen voor het afstemmen van de prestaties](database-advisor-implement-performance-recommendations.md), waarmee u de aanbevolen acties kunt weergeven en uitvoeren.

Een voortdurende bewaking van een Data Base is echter een hard en tijdrovende taak, met name wanneer u met veel data bases werkt. [Intelligent Insights](intelligent-insights-overview.md) voert deze taak voor u uit door automatisch SQL database prestaties op schaal te controleren. Er wordt gemeld dat er problemen zijn bij het afnemen van prestaties, het duidt de hoofd oorzaak van elk probleem aan en er worden aanbevelingen gedaan voor prestatie verbetering wanneer dat mogelijk is.

Het beheren van een groot aantal data bases is mogelijk niet efficiënt, zelfs niet met alle beschik bare hulpprogram ma's en rapporten die SQL Database en Azure bieden. In plaats van de data base hand matig te controleren en te verfijnen, kunt u overwegen enkele van de bewakings-en afstemmings acties te delegeren voor SQL Database met [Automatische afstemming](automatic-tuning-overview.md) SQL Database voert automatisch aanbevelingen, tests en verifieert alle afstemmings acties uit om ervoor te zorgen dat de prestaties verbeteren. Op deze manier kunnen SQL Database automatisch worden aangepast aan uw werk belasting op een gecontroleerde en veilige manier. Automatische afstemming betekent dat de prestaties van uw data base zorgvuldig worden bewaakt en vergeleken voor en na elke afstem actie. Als de prestaties niet worden verbeterd, wordt de afstemmings actie teruggedraaid.

Veel van onze partners waarop [SaaS multi-tenant-apps](saas-tenancy-app-design-patterns.md) worden uitgevoerd op SQL database, zijn afhankelijk van het automatisch afstemmen van de prestaties om ervoor te zorgen dat hun toepassingen stabiele en voorspel bare prestaties hebben. Dankzij deze functie wordt de kans op prestatieproblemen (op welk moment dan ook) aanzienlijk kleiner. Omdat in een deel van hun klant basis ook SQL Server worden gebruikt, gebruiken ze de aanbevelingen voor het indexeren van SQL Database om hun klanten SQL Server te helpen.

Er zijn twee automatisch afstemmings aspecten [beschikbaar in SQL database](automatic-tuning-overview.md):

- **Automatisch indexbeheer**: hiermee worden indexen geïdentificeerd die moeten worden toegevoegd aan of verwijderd uit uw database.
- **Automatische plan correctie**: identificeert problematische abonnementen en lost problemen met de prestaties van het SQL-plan op.

### <a name="adaptive-query-processing"></a>Verwerking van adaptieve query’s

U kunt [adaptieve query verwerking](/sql/relational-databases/performance/intelligent-query-processing)gebruiken, inclusief Interleaved uitvoering voor tabel functies met meerdere instructies, feedback over geheugen toekenning in batch modus en adaptieve samen voegingen in batch modus. Elk van deze functies voor adaptieve query verwerking is vergelijkbaar met de technieken ' leren en aanpassen ', waardoor er meer prestatie problemen worden opgelost die te maken hebben met historische onsamenhangende optimalisatie problemen voor query's.

## <a name="advanced-security-and-compliance"></a>Geavanceerde beveiliging en naleving van voorschriften

SQL Database biedt een reeks [ingebouwde functies voor beveiliging en naleving](../../active-directory/identity-protection/security-overview.md) zodat uw toepassing voldoet aan diverse vereisten op het gebied van beveiliging en de naleving van voorschriften.

> [!IMPORTANT]
> Micro soft heeft Azure SQL Database gecertificeerd (alle implementatie-opties) tegen een aantal nalevings standaarden. Raadpleeg voor meer informatie het [vertrouwens centrum van Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), waar u de meest recente lijst met SQL database nalevings certificeringen kunt vinden.

### <a name="advance-threat-protection"></a>Geavanceerde beveiliging tegen bedreigingen

Azure Defender voor SQL is een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Het bevat functionaliteit voor het beheren van uw database problemen en het detecteren van afwijkende activiteiten die kunnen wijzen op een bedreiging voor uw data base. Het biedt één locatie om deze mogelijkheden in te scha kelen en te beheren.

- [Evaluatie van beveiligings problemen](sql-vulnerability-assessment.md):

  Met deze service kunt u mogelijke beveiligings problemen met de data base detecteren, bijhouden en helpen oplossen. Deze service biedt u inzicht in de status van de beveiliging en bruikbare stappen om beveiligingsproblemen op te lossen en de beveiliging van uw database te verbeteren.
- [Detectie van bedreigingen](threat-detection-configure.md):

  Deze functie detecteert afwijkende activiteiten die ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot of misbruik te maken van uw data base. Hiermee wordt uw database continu gecontroleerd op verdachte activiteiten en wordt u onmiddellijk gewaarschuwd bij mogelijke beveiligingsproblemen, SQL-injectieaanvallen en afwijkende databasetoegangspatronen. Meldingen over bedreigingsdetectie bevatten gedetailleerde informatie over verdachte activiteiten en aanbevelingen voor het onderzoeken en tegenhouden ervan.

### <a name="auditing-for-compliance-and-security"></a>Controles voor naleving en beveiliging

Met [auditing](../../azure-sql/database/auditing-overview.md) worden database gebeurtenissen bijgehouden en naar een audit logboek in uw Azure Storage-account geschreven. Controle kan u helpen om wet- en regelgeving na te leven, activiteiten in de database te begrijpen en inzicht te krijgen in de afwijkingen en discrepanties die kunnen wijzen op problemen voor het bedrijf of vermoedelijke schendingen van de beveiliging.

### <a name="data-encryption"></a>Gegevensversleuteling

SQL Database helpt u om uw gegevens te beveiligen met versleuteling. Voor gegevens in beweging wordt [trans port Layer Security](https://support.microsoft.com/kb/3135244)gebruikt. Voor Data-at-rest wordt er gebruik gemaakt van [transparante gegevens versleuteling](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Voor gegevens die in gebruik zijn, wordt gebruikgemaakt van [Always encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="data-discovery-and-classification"></a>Gegevensdetectie en -classificatie

[Gegevens detectie en-classificatie](data-discovery-and-classification-overview.md) bieden mogelijkheden die zijn ingebouwd in Azure SQL database voor het detecteren, classificeren, labelen en beveiligen van gevoelige gegevens in uw data bases. Het biedt inzicht in de status van de database classificatie en houdt de toegang tot gevoelige gegevens in de data base en de grenzen van de randen.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integratie en meervoudige verificatie

Dankzij [Azure Active Directory-integratie](authentication-aad-overview.md) kunt u in SQL Database de identiteit van databasegebruikers en andere Microsoft-services centraal beheren. Deze mogelijkheid vereenvoudigt het beheer van machtigingen en verbetert de beveiliging. Azure Active Directory ondersteunt [multi-factor Authentication](authentication-mfa-ssms-overview.md) om de beveiliging van gegevens en toepassingen te verg Roten, terwijl een eenmalige aanmeld procedure wordt ondersteund.

## <a name="easy-to-use-tools"></a>Gebruiksvriendelijke hulpprogramma’s

SQL Database maakt het bouwen en onderhouden van toepassingen makkelijker en productiever. Met SQL Database kunt u zich concentreren op dat waar u het beste in bent: fantastische apps bouwen. U kunt SQL Database beheren en ontwikkelen met behulp van hulpprogram ma's en vaardig heden die u al hebt.

|Hulpprogramma|Beschrijving|
|:---|:---|
|[Azure Portal](https://portal.azure.com/)|Een webtoepassing voor het beheren van alle Azure-Services.|
|[Azure Data Studio](/sql/azure-data-studio/)|Een platform voor meerdere platforms dat wordt uitgevoerd in Windows, macOS en Linux.|
|[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)|Een gratis, Download bare client toepassing voor het beheren van een SQL-infra structuur, van SQL Server tot SQL Database.|
|[SQL Server Data Tools in Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)|Een gratis, Download bare client toepassing voor het ontwikkelen van SQL Server relationele data bases, data bases in Azure SQL Database, integratie Services-pakketten, Analysis Services gegevens modellen en Reporting Services-rapporten.|
|[Visual Studio Code](https://code.visualstudio.com/docs)|Een gratis, Download bare, open-source code-editor voor Windows, macOS en Linux. Het ondersteunt extensies, waaronder de [MSSQL-extensie](https://aka.ms/mssql-marketplace) voor het uitvoeren van query's Microsoft SQL Server, Azure SQL database en Azure Azure Synapse Analytics (voorheen SQL Data Warehouse).|

SQL Database biedt ondersteuning voor het bouwen van toepassingen met python, Java, Node.js, PHP, Ruby en .NET op macOS, Linux en Windows. SQL Database ondersteunt dezelfde [verbindingsbibliotheken](connect-query-content-reference-guide.md#libraries) als SQL Server.

[!INCLUDE [sql-database-create-manage-portal](../includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>Veelgestelde vragen over SQL Database

### <a name="can-i-control-when-patching-downtime-occurs"></a>Kan ik bepalen wanneer uitval tijd van patches optreedt?

Nee. De impact van patching is doorgaans niet merkbaar als u [probeert logica](develop-overview.md#resiliency) in uw app te gebruiken. Zie [plannen voor Azure-onderhouds gebeurtenissen in Azure SQL database](planned-maintenance.md)voor meer informatie.



## <a name="engage-with-the-sql-server-engineering-team"></a>Contact met het technische team van SQL Server

- [DBA stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): vragen over het beheer van data bases.
- [Stack overflow](https://stackoverflow.com/questions/tagged/sql-server): Stel ontwikkel vragen in.
- [Micro soft Q&een vraag pagina](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html): Stel technische vragen in.
- [Feedback](https://aka.ms/sqlfeedback): fouten en aanvraag functies rapporteren.
- [Reddit](https://www.reddit.com/r/SQLServer/): bespreek SQL Server.

## <a name="next-steps"></a>Volgende stappen

- Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/sql-database/) voor kostprijs vergelijkingen en reken machines met betrekking tot afzonderlijke data bases en elastische Pools.
- Bekijk deze Quick starts om aan de slag te gaan:

  - [Een database maken in Azure Portal](single-database-create-quickstart.md)  
  - [Een Data Base maken met de Azure CLI](az-cli-script-samples-content-guide.md)
  - [Een Data Base maken met behulp van Power shell](powershell-script-content-guide.md)

- Zie de volgende artikelen voor een reeks Azure CLI- en PowerShell-voorbeelden:
  - [Azure CLI-voorbeelden voor SQL Database](az-cli-script-samples-content-guide.md)
  - [Azure PowerShell-voorbeelden voor SQL Database](powershell-script-content-guide.md)

- Zie [Azure-route kaart voor SQL database](https://azure.microsoft.com/roadmap/?category=databases)voor meer informatie over de nieuwe mogelijkheden die worden aangekondigd.
- Raadpleeg het [Azure SQL database blog](https://azure.microsoft.com/blog/topics/database), waar SQL Server leden van het product team van de blog over SQL database nieuws en functies.

