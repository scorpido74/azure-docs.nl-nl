---
title: Wat is de service Azure SQL Database? | Microsoft Docs
description: 'Maak kennis met SQL-Database: technische informatie en mogelijkheden van het relationele database beheersysteem (RDBMS) in de cloud van Microsoft.'
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
ms.openlocfilehash: f7fc5b32fb18da60816056c72dde8c53d439befe
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812186"
---
# <a name="what-is-azure-sql-database-service"></a>Wat is Azure SQL Database-Service?

Azure SQL Database is een service voor algemeen gebruik van relationele data bases waarmee u een Maxi maal beschik bare gegevenslaag met hoge prestaties voor de toepassingen en oplossingen in Microsoft Azure Cloud kunt maken. SQL Database kan de juiste keuze zijn voor een groot aantal moderne Cloud toepassingen, omdat u hiermee krachtige functies kunt gebruiken voor het verwerken van relationele gegevens en [niet-relationele structuren](sql-database-multi-model-features.md) zoals grafieken, JSON, ruimtelijke en XML. Het is gebaseerd op de nieuwste stabiele versie van de [Microsoft SQL server data base-engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) en biedt u de mogelijkheid om een uitgebreide set geavanceerde functies voor het verwerken van query's te gebruiken, zoals [hoge prestaties in de geheugen technologieën](sql-database-in-memory.md) en [intelligente query verwerking ](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json).
In het kader van de cloudstrategie van Microsoft worden nieuwe mogelijkheden van SQL Server eerst uitgebracht in SQL Database en vervolgens in SQL Server zelf. Dankzij deze aanpak kunt u beschikken over de nieuwste mogelijkheden van SQL Server zonder overhead voor patching en upgrading, en zijn de nieuwe functies getest op miljoenen databases. Met SQL Database kunt u eenvoudig prestaties in twee verschillende aankoop modellen definiëren en schalen: een [op vCore gebaseerd inkoop model](sql-database-service-tiers-vcore.md) en een [op DTU gebaseerd aankoop model](sql-database-service-tiers-dtu.md). SQL Database is een volledig beheerde service met ingebouwde high-beschikbaarheids, back-ups en andere veelvoorkomende onderhouds bewerkingen. Micro soft behandelt alle patches en updates van de SQL-en besturingssysteem code naadloos en samen vatting van alle beheer van de onderliggende infra structuur.

> [!NOTE]
> Zie voor een verklarende woorden lijst in Azure SQL Database [SQL database termen woorden lijst](sql-database-glossary-terms.md)

Azure SQL Database biedt de volgende implementatieopties voor een Azure SQL-database:

![implementatieopties](./media/sql-database-technical-overview/deployment-options.png)

- [Eén data base](sql-database-single-database.md) vertegenwoordigt een volledig beheerde, geïsoleerde data base die perfecte keuze is voor de moderne Cloud toepassingen en micro services die een enkele betrouw bare gegevens bron nodig hebben. Eén data base is vergelijkbaar met een [Inge sloten data base](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) in [Microsoft SQL server data base-engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- Een [beheerd exemplaar](sql-database-managed-instance.md) is een volledig beheerd exemplaar van de [Microsoft SQL server data base-engine](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) met een set data bases die samen kunnen worden gebruikt. Het is een perfecte keuze voor het eenvoudig migreren van on-premises SQL Server data bases naar Azure Cloud en voor toepassingen die gebruikmaken van krachtige database functies die SQL Server data base-engine biedt.
- [Elastische pool](sql-database-elastic-pool.md) is een verzameling van [afzonderlijke data bases](sql-database-single-database.md) met een gedeelde set resources, zoals CPU of geheugen. U kunt afzonderlijke data bases naar en uit een elastische pool verplaatsen.

> [!IMPORTANT]
> Zie [SQL-functies](sql-database-features.md)voor meer informatie over de functie verschillen tussen SQL Database en SQL Server, evenals de verschillen tussen de verschillende Azure SQL database implementatie opties.

SQL Database biedt voorspel bare prestaties met meerdere resource typen, service lagen en reken grootten die dynamische schaal baarheid bieden zonder downtime, ingebouwde intelligente optimalisatie, wereld wijde schaal baarheid en beschik baarheid en geavanceerde beveiliging opties: alle met bijna nul beheer. Dankzij deze mogelijkheden kunt u zich richten op het sneller ontwikkelen en op de markt brengen van apps, in plaats van kostbare tijd en middelen in te zetten voor het beheer van virtuele machines en infrastructuur. De SQL Database-service is momenteel beschikbaar in 38 datacenters over de hele wereld, en er komen er regelmatig meer bij. Dat betekent dat u uw database kunt uitvoeren in een datacenter bij u in de buurt.

## <a name="scalable-performance-and-pools"></a>Schaalbare prestaties en pools

Met alle soorten SQL Database kunt u de hoeveelheid resources definiëren die worden toegewezen. 
- Met afzonderlijke data bases is elke Data Base geïsoleerd van elkaar en draagbaar, elk met een eigen gegarandeerde hoeveelheid reken-, geheugen-en opslag resources. De hoeveelheid resources die aan de data base is toegewezen, is toegewezen aan die data base en zal niet worden gedeeld met andere data bases in de Azure-Cloud. Het biedt u ook de mogelijkheid om de [resources van één data base](sql-database-single-database-scale.md) dynamisch omhoog en omlaag te schalen. Individuele database biedt verschillende berekenings-, geheugen-en opslag bronnen voor verschillende behoeften die variëren van 1 tot 80 vCores, 32 GB tot 4 TB, enzovoort. Met de [grootschalige](sql-database-service-tier-hyperscale.md) voor één Data Base kunt u schalen naar 100 TB, met snelle back-up-en herstel mogelijkheden.
- Met elastische Pools kunt u resources toewijzen die worden gedeeld door alle data bases in de groep. U kunt een nieuwe data base maken of de bestaande afzonderlijke data bases verplaatsen naar een resource groep om het gebruik van resources te maximaliseren en geld te besparen, en de mogelijkheid om [bronnen voor elastische Pools](sql-database-elastic-pool-scale.md) dynamisch omhoog en omlaag te schalen.
- Met beheerde instanties wordt elk beheerd exemplaar geïsoleerd van andere instanties met gegarandeerde bronnen. Binnen een beheerd exemplaar delen de exemplaar databases een set resources, en de mogelijkheid om [beheerde exemplaar bronnen](sql-database-managed-instance-resource-limits.md) dynamisch omhoog en omlaag te schalen.

U kunt uw eerste app bouwen op een kleine, afzonderlijke Data Base met een lage prijs per maand in de servicelaag voor algemeen gebruik en de servicelaag vervolgens hand matig of via een programma op elk gewenst moment wijzigen in de servicelaag van het bedrijf, zodat deze voldoet aan de behoeften van uw oplossing. U kunt het prestatieniveau aanpassen zonder uitvaltijd voor uw app of voor uw klanten. Dankzij dynamische schaalbaarheid kan uw database op een transparante manier snel reageren op veranderende resourcevereisten en betaalt u alleen voor de resources die u nodig hebt wanneer u ze nodig.

Dynamische schaalbaarheid is iets anders dan automatisch schalen. Automatisch schalen vindt plaats wanneer een service automatisch wordt geschaald op basis van criteria, terwijl u met dynamische schaalbaarheid handmatig kunt schalen zonder uitvaltijd. Een enkele data base ondersteunt hand matige dynamische schaal baarheid, maar niet automatisch schalen. Voor een meer *automatische* ervaring zou u elastische pools kunnen gebruiken. Hiermee kunnen databases resources in een pool delen op basis van afzonderlijke databasebehoeften. Er zijn echter scripts waarmee de schaal baarheid voor één data base kan worden geautomatiseerd. Zie voor een voor beeld [Power shell gebruiken om één Data Base te bewaken en te schalen](scripts/sql-database-monitor-and-scale-database-powershell.md).

### <a name="purchasing-models-service-tiers-compute-sizes-and-storage-amounts"></a>Koop modellen, service lagen, reken grootten en opslag bedragen

SQL Database biedt twee aankoop modellen:
- [Met het op vCore gebaseerde aankoop model](sql-database-service-tiers-vcore.md) kunt u het aantal vCores, de hoeveelheid geheugen en de hoeveelheid en snelheid van de opslag kiezen. Met het op vCore gebaseerde aankoop model kunt u ook Azure Hybrid Benefit gebruiken om [SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) kosten besparingen te verkrijgen. Zie [Veelgestelde vragen](#sql-database-frequently-asked-questions-faq)voor meer informatie over de Azure Hybrid Benefit.
- Het [op DTU gebaseerde aankoop model](sql-database-service-tiers-dtu.md) biedt een combi natie van compute-, geheugen-en i/o-resources in drie service lagen ter ondersteuning van Lightweight-to-Heavyweight data base-workloads. Reken grootten binnen elke laag bieden een andere combi natie van deze resources, waaraan u extra opslag resources kunt toevoegen.

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastische pools voor optimaal resourcegebruik

Voor veel bedrijven en toepassingen is het kunnen maken van enkele databases en het naar wens omhoog of omlaag schalen van de prestaties al voldoende, vooral als de gebruikspatronen redelijk voorspelbaar zijn. Bij onvoorspelbare gebruikspatronen kan het echter lastig zijn uw kosten en bedrijfsmodel effectief te beheren. [Elastische pools](sql-database-elastic-pool.md) zijn ontworpen om dit probleem te verhelpen. Het concept is eenvoudig. U wijst prestatie resources toe aan een pool in plaats van een afzonderlijke data base en betaalt voor de collectieve prestaties van de pool in plaats van voor de prestaties van één data base.

   ![elastische pools](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Met elastische pools hoeft u zich niet bezig te houden met het verhogen en verlagen van de databaseprestaties als de vraag naar resources fluctueert. De pooldatabases maken naar behoefte gebruik van de prestatieresources van de elastische pool. Pooldatabases tellen mee voor het verbruik tot het maximum voor de pool is bereikt. Zo blijven uw kosten voorspelbaar, ook al is uw gebruik van de individuele database dat niet. Bovendien kunt u [databases aan de groep toevoegen of eruit verwijderen](sql-database-elastic-pool-manage-portal.md). Zo kan uw app kan worden opgeschaald van een handjevol databases naar duizenden databases, allemaal binnen het budget dat u zelf bepaalt. U kunt ook de minimale en maximale beschikbare resources die voor databases beschikbaar zijn in de pool beheren om ervoor te zorgen dat er geen database in de pool is die alle poolresources gebruikt en dat elke pooldatabase een gegarandeerd minimum aan resources heeft. Zie [Design Patterns for Multi-tenant SaaS Applications with SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) (Ontwerppatronen voor multitenant SaaS-toepassingen met behulp van SQL Database) voor meer informatie over ontwerppatronen voor SaaS-toepassingen met elastische groepen.

Scripts kunnen helpen bij het bewaken en schalen van elastische pools. Zie [PowerShell gebruiken voor het controleren en schalen van een elastische SQL-pool in Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md) voor een voorbeeld

> [!IMPORTANT]
> Een beheerd exemplaar biedt geen ondersteuning voor elastische Pools. In plaats daarvan is een beheerd exemplaar een verzameling exemplaar databases die beheerde exemplaar resources delen.

### <a name="blend-single-databases-with-pooled-databases"></a>Individuele databases combineren met pooldatabases

U kunt afzonderlijke data bases combi neren met elastische Pools en de service lagen van individuele data bases en elastische Pools snel en eenvoudig aanpassen aan uw situatie. Dankzij de kracht en het bereik van Azure kunt u andere Azure-services combineren en integreren met SQL Database om te voldoen aan de behoeften voor uw unieke app-ontwerp, kosten te besparen en resources efficiënt te beheren. Daarnaast kunt u nieuwe zakelijke verkoopkansen creëren.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Uitgebreide mogelijkheden voor bewaking en waarschuwingen

Azure SQL Database biedt een aantal geavanceerde functies voor het bewaken en oplossen van problemen, waarmee u de volledige inzichten kunt verkrijgen in de kenmerken van de werk belasting. De functies en hulpprogram ma's kunnen als volgt worden gecategoriseerd:
 - De ingebouwde bewakings mogelijkheden van de nieuwste versie van SQL Server data base-engine waarmee u real-time prestatie inzichten kunt vinden. 
 - PaaS bewakings mogelijkheden van het Azure-platform waarmee u eenvoudig een groot aantal data base-exemplaren kunt bewaken en ook de aanbevelingen voor het oplossen van problemen biedt die u kunnen helpen bij het oplossen van prestatie problemen.

De belangrijkste ingebouwde functie voor het controleren van de data base-engine die u moet gebruiken, is een [query archief](sql-database-operate-query-store.md) component waarmee de prestaties van uw query's in realtime worden vastgelegd en waarmee u de potentiële prestatie problemen en de belangrijkste bron kunt identificeren licht. Automatische afstemming en aanbevelingen bieden adviezen over de query's met de teruggedraaide-prestaties en ontbrekende of gedupliceerde indexen. Met automatisch afstemmen in Azure SQL Database kunt u hand matig de scripts Toep assen waarmee de problemen kunnen worden opgelost of Azure SQL Database de oplossing toe te passen, testen en verifiëren. Dit biedt enige voor deel, waardoor de wijziging wordt behouden of hersteld, afhankelijk van het resultaat. Naast de mogelijkheden van query Store en automatisch afstemmen kunt u ook standaard [dmv's en XEvent](sql-database-monitoring-with-dmvs.md) gebruiken om de prestaties van de werk belasting te controleren.

Azure-platform biedt de ingebouwde hulpprogram ma's voor [prestatie bewaking](sql-database-performance.md) en [waarschuwingen](sql-database-insights-alerts-portal.md) , gecombineerd met de prestatie classificaties waarmee u de status van duizenden data bases eenvoudig kunt bewaken. Met deze hulpprogram ma's kunt u snel de gevolgen van het omhoog of omlaag schalen op basis van uw huidige of verwachte prestatie vereisten beoordelen. Daarnaast kan SQL Database [metrische gegevens en diagnostische logboeken verzenden](sql-database-metrics-diag-logging.md) die de bewaking vergemakkelijken. U kunt SQL Database configureren voor het opslaan van resourcegebruik, werkrollen en sessies, en connectiviteit in een van deze Azure-resources:

- **Azure Storage**: Voor het archiveren van grote hoeveel heden telemetrie voor een kleine prijs.
- **Azure Event Hub**: Voor het integreren van SQL Database telemetrie met uw aangepaste bewakings oplossing of dynamische pijp lijnen.
- **Azure monitor logboeken**: Voor een ingebouwde bewakings oplossing met rapportage-, waarschuwings-en beperkende mogelijkheden.

    ![architectuur](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Beschikbaarheid

In een traditionele SQL Server omgeving hebt u doorgaans (ten minste) 2 machines lokaal ingesteld met exacte (synchroon onderhanden) kopieën van de gegevens (met behulp van functies als AlwaysOn-beschikbaarheids groepen of exemplaren van failoverclusters) om te beveiligen tegen een fout in één machine/onderdeel. Dit biedt hoge Beschik baarheid, maar biedt geen bescherming tegen een natuur ramp die uw Data Center vernietigt.

Bij herstel na nood gevallen wordt ervan uitgegaan dat een onherstelbare gebeurtenis geografisch kan worden gelokaliseerd zodat er een andere machine of set machines met een kopie van uw gegevens ver weg is.  In SQL Server kunt u AlwaysOn-beschikbaarheids groepen gebruiken die worden uitgevoerd in de async-modus om deze mogelijkheid te verkrijgen.  De snelheid van het licht probleem betekent meestal dat personen niet hoeven te wachten op replicatie voordat een trans actie wordt door gegeven, zodat er gegevens verloren kunnen gaan wanneer u niet-geplande failovers uitvoert.

Data bases in de Premium-en Business Critical-service lagen [komen al iets overeen](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) met de synchronisatie van een beschikbaarheids groep. Data bases in lagere service lagen bieden redundantie via opslag met behulp van een [ander, maar gelijkwaardig mechanisme](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability). Er is logica die bescherming biedt tegen een storing van één machine.  De functie actieve geo-replicatie biedt u de mogelijkheid om te beveiligen tegen nood gevallen waarbij een hele regio wordt vernietigd.

Azure-beschikbaarheidszones speelt een probleem met het hoge Beschik baarheid.  Er wordt geprobeerd te beveiligen tegen storingen van één Data Center-gebouw binnen één regio.  Het wil daarom beveiligen tegen het stroom verlies of het netwerk tot een gebouw. In SQL Azure werkt dit door de verschillende replica's in verschillende beschikbaarheids zones (verschillende gebouwen, effectief) te plaatsen en anders als voorheen te werken.

De toonaangevende Azure-beschik service level agreement baarheid van 99,99% [(Sla)](https://azure.microsoft.com/support/legal/sla/), die wordt ondersteund door een wereld wijd netwerk van door micro soft beheerde data centers, zorgt ervoor dat uw app wordt uitgevoerd op 24/7. Het Azure-platform beheert elke Data Base volledig en garandeert geen gegevens verlies en hoog percentage Beschik baarheid van gegevens. Patches, back-ups, replicatie, foutdetectie, onderliggende potentiële hardware-, software- of netwerkfouten, de implementatie van bugfixes, failovers, database-upgrades en andere onderhoudstaken worden in Azure automatisch afgehandeld. Standaard-beschikbaarheid wordt bereikt door de lagen voor berekeningen en opslag te scheiden. Premium-Beschik baarheid wordt bereikt door Compute en opslag te integreren op één knoop punt voor prestaties en vervolgens technologie te implementeren die vergelijkbaar is met beschikbaarheids groepen in de voor vallen. Zie [SQL database Beschik baarheid](sql-database-high-availability.md)voor een volledige bespreking van de mogelijkheden voor hoge Beschik baarheid van Azure SQL database. Daarnaast biedt SQL Database ingebouwde functies voor [bedrijfscontinuïteit en wereldwijde schaalbaarheid](sql-database-business-continuity.md), zoals:

- **[Automatische back-ups](sql-database-automated-backups.md)** :

  SQL Database voert automatisch volledige, differentiële en transactie logboek back-ups van Azure SQL-data bases uit zodat u naar elk gewenst moment kunt herstellen. Voor afzonderlijke data bases en gepoolde data bases kunt u SQL Database configureren voor het opslaan van volledige database back-ups naar Azure Storage voor lange termijn retentie van back-ups. Voor beheerde instanties kunt u ook back-ups met alleen kopiëren uitvoeren voor lange termijn retentie.

- **[Herstel naar](sql-database-recovery-using-backups.md)** een bepaald tijdstip:

  Alle SQL Database implementatie opties ondersteunen herstel naar een bepaald tijdstip binnen de automatische Bewaar periode voor back-ups voor Azure SQL database.
- **[Actieve geo-replicatie](sql-database-active-geo-replication.md)** :

  Met één data base en gepoolde data bases kunt u Maxi maal vier Lees bare secundaire data bases configureren in dezelfde of wereld wijd gedistribueerde Azure-data centers.  Als u bijvoorbeeld een SaaS-toepassing hebt met een catalogusdatabase met een groot volume aan gelijktijdige alleen-lezen transacties, kunt u actieve geo-replicatie gebruiken om het lezen wereldwijd te schalen en knelpunten weg te nemen die ontstaan door de lees-workloads op de primaire database. Voor beheerde instanties gebruikt u groepen voor automatische failover.
- **[Groepen voor automatische failover](sql-database-auto-failover-group.md)** :

  Met alle SQL Database implementatie opties kunt u failover-groepen gebruiken om hoge Beschik baarheid en taak verdeling op wereld wijde schaal mogelijk te maken, waaronder transparante geo-replicatie en failover van grote sets data bases, elastische groepen en beheerde exemplaren. Met failover-groepen kunt u wereld wijd gedistribueerde SaaS-toepassingen maken met minimale beheer overhead, waardoor alle complexe bewaking, route ring en failover-indeling SQL Database.
- **[Zone-redundante data bases](sql-database-high-availability.md)** :

  Met SQL Database kunt u Premium-of business critical-data bases of elastische Pools inrichten in meerdere beschikbaarheids zones. Omdat deze databases en elastische pools meerdere redundante replica's hebben om een hoge beschikbaarheid te garanderen, biedt het plaatsen van deze replica's in meerdere beschikbaarheidszones betere weerbaarheid, inclusief de mogelijkheid om zonder verlies van gegevens automatisch te herstellen van schalingsfouten in het datacenter.

## <a name="built-in-intelligence"></a>Ingebouwde intelligentie

Met SQL Database beschikt u over ingebouwde intelligentie waarmee u de kosten van het uitvoeren en beheren van databases drastisch verlaagt en de prestaties en beveiliging van uw toepassing optimaliseert. SQL Database verwerkt 24 uur per dag miljoenen workloads van klanten en verzamelt en verwerkt daarbij enorme hoeveelheden telemetriegegevens, waarbij de privacy van klanten te allen tijde volledig wordt gerespecteerd. Er worden verschillen de algoritmen gebruikt om de telemetriegegevens continu te evalueren zodat de service zich kan aanpassen aan uw toepassing. Op basis van deze analyse krijgt de service de aanbevelingen voor prestatie verbetering die zijn afgestemd op uw specifieke werk belasting.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatisch bewaking en afstemming van prestaties

SQL Database biedt gedetailleerde informatie over de query’s die u wilt bewaken. SQL Database leert uw databasepatronen zodat u uw databaseschema kunt aanpassen aan uw workload. SQL Database geeft [aanbevelingen voor het afstemmen van de prestaties](sql-database-advisor.md), waarmee u de aanbevolen acties kunt weergeven en uitvoeren.

Een voortdurende bewaking van een Data Base is echter een hard en tijdrovende taak, met name bij het omgaan met veel data bases. Met [Intelligent Insights](sql-database-intelligent-insights.md) wordt deze taak automatisch voor u uitgevoerd door de prestaties van SQL Database op schaal automatisch te bewaken en u te informeren over problemen met prestatievermindering. Hierbij wordt de hoofdoorzaak van het probleem aangegeven. Indien mogelijk worden er ook aanbevelingen voor prestatieverbetering geboden.

Het is vrijwel onmogelijk om het beheer van enorme hoeveelheden databases op een efficiënte manier uit te voeren, zelfs met de hulpprogramma’s en rapporten van SQL Database en de Azure-portal. In plaats van het handmatig bewaken en afstemmen van uw database, kunt u overwegen sommige van deze taken over te laten aan SQL Database, met de functie voor [automatisch afstemmen](sql-database-automatic-tuning.md). SQL Database voert automatisch aanbevelingen, tests en verifieert alle afstemmings acties uit om ervoor te zorgen dat de prestaties verbeteren. Op deze manier kunnen SQL Database automatisch worden aangepast aan uw werk belasting op een gecontroleerde en veilige manier. Automatische afstemming betekent dat de prestaties van uw database zorgvuldig worden bewaakt en vergeleken voor en na elke afstemactie. Als de prestaties niet zijn verbeterd, wordt de betreffende actie teruggedraaid.

Veel van onze partners die [multitenant SaaS-apps](sql-database-design-patterns-multi-tenancy-saas-applications.md) uitvoeren op SQL Database, vertrouwen al op deze automatische afstemming, zodat de prestaties van hun toepassingen altijd stabiel en voorspelbaar zijn. Dankzij deze functie wordt de kans op prestatieproblemen (op welk moment dan ook) aanzienlijk kleiner. En omdat een deel van hun klanten ook werkt met SQL Server, maken ze gebruik van dezelfde indexeringsaanbevelingen van SQL Database om hun klanten met SQL Server te helpen.

Er zijn twee automatisch afstemmingsmethoden [in SQL Database](sql-database-automatic-tuning.md):

- **Automatisch index beheer**: Identificeert indexen die moeten worden toegevoegd in uw data base en indexen die moeten worden verwijderd.
- **Automatische correctie plannen**: Identificeert problematische plannen en lost problemen met de prestaties van het SQL-plan op (binnenkort beschikbaar in SQL Server 2017).

### <a name="adaptive-query-processing"></a>Verwerking van adaptieve query’s

Er is ook een reeks functies voor de [verwerking van adaptieve query’s](/sql/relational-databases/performance/intelligent-query-processing) toegevoegd aan SQL Database, waaronder interleave-uitvoering van tabelfuncties met meerdere instructies, feedback over geheugentoekenning in batchmodus en adaptieve samenvoegingen in batchmodus. Elk van deze verwerkingsfuncties voor adaptieve query’s werkt met vergelijkbare technieken voor ‘leren en aanpassen’, voor het oplossen van prestatieproblemen die het gevolg zijn hardnekkige problemen met query-optimalisatie.

## <a name="advanced-security-and-compliance"></a>Geavanceerde beveiliging en naleving van voorschriften

SQL Database biedt een reeks [ingebouwde functies voor beveiliging en naleving](sql-database-security-overview.md) zodat uw toepassing voldoet aan diverse vereisten op het gebied van beveiliging en de naleving van voorschriften.

> [!IMPORTANT]
> Azure SQL Database (alle implementatie opties), is gecertificeerd op basis van een aantal nalevings standaarden. Zie het [vertrouwens centrum van Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) voor meer informatie over de meest recente lijst met SQL database nalevings certificeringen.

### <a name="advance-threat-protection"></a>Advanced Threat Protection

Geavanceerde gegevens beveiliging is een uniform pakket voor geavanceerde SQL-beveiligings mogelijkheden. Het bevat functionaliteit voor het detecteren en classificeren van gevoelige gegevens, het beheren van beveiligingsproblemen in uw database, en het detecteren van afwijkende activiteiten die kunnen duiden op een bedreiging van de database. Het is tevens een centraal punt voor het inschakelen en beheren van deze mogelijkheden.

- [Classificatie van gegevens detectie &](sql-database-data-discovery-and-classification.md):

  Deze functie (momenteel in Preview) biedt mogelijkheden die zijn ingebouwd in Azure SQL Database voor het detecteren, classificeren, labelen & beveiligen van gevoelige gegevens in uw data bases. Het kan worden gebruikt voor het zichtbaar maken van de classificatiestatus van gegevens in uw database, en het traceren van de toegang tot gevoelige gegevens binnen en buiten de database.
- [Evaluatie van beveiligings problemen](sql-vulnerability-assessment.md):

  Met deze service kunt u mogelijke beveiligings problemen met de data base detecteren, bijhouden en helpen oplossen. Deze service biedt u inzicht in de status van de beveiliging en bruikbare stappen om beveiligingsproblemen op te lossen en de beveiliging van uw database te verbeteren.
- [Detectie van bedreigingen](sql-database-threat-detection.md):

  Deze functie detecteert afwijkende activiteiten die een ongebruikelijke en potentieel schadelijke pogingen om toegang te krijgen tot uw data base of deze te exploiteren. Hiermee wordt uw database continu gecontroleerd op verdachte activiteiten en wordt u onmiddellijk gewaarschuwd bij mogelijke beveiligingsproblemen, SQL-injectieaanvallen en afwijkende databasetoegangspatronen. Waarschuwingen voor detectie van bedreigingen bieden Details van de verdachte activiteit en aanbevolen actie voor het onderzoeken en oplossen van de dreiging.

### <a name="auditing-for-compliance-and-security"></a>Controles voor naleving en beveiliging

Met [auditing](sql-database-auditing.md) worden database gebeurtenissen bijgehouden en naar een audit logboek in uw Azure Storage-account geschreven. Dankzij controles kunt u zorgen voor naleving van wet- en regelgeving, krijgt u inzicht in de activiteit in uw database en in de afwijkingen en discrepanties die kunnen wijzen op problemen voor het bedrijf of vermoedelijke schendingen van de beveiliging.

### <a name="data-encryption"></a>Gegevensversleuteling

SQL Database uw gegevens beveiligt door versleuteling te bieden voor gegevens in beweging met de [beveiliging van trans port Layer](https://support.microsoft.com/kb/3135244), voor gegevens in rust met [transparante gegevens versleuteling](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), en voor gegevens die in gebruik zijn met [Always encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integratie en meervoudige verificatie

Dankzij [Azure Active Directory-integratie](sql-database-aad-authentication.md) kunt u in SQL Database de identiteit van databasegebruikers en andere Microsoft-services centraal beheren. Deze mogelijkheid vereenvoudigt het beheer van machtigingen en verbetert de beveiliging. Azure Active Directory ondersteunt [Multi-Factor Authentication](sql-database-ssms-mfa-authentication.md) (MFA) voor betere beveiliging van gegevens en toepassingen, en ondersteunt ook een proces voor eenmalige aanmelding.

### <a name="compliance-certification"></a>Nalevingscertificering

SQL Database wordt regelmatig gecontroleerd en is gecertificeerd volgens diverse nalevingsstandaarden. Zie het [vertrouwens centrum van Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) voor meer informatie over de meest recente lijst met SQL database nalevings certificeringen.

## <a name="easy-to-use-tools"></a>Gebruiksvriendelijke hulpprogramma’s

SQL Database maakt het bouwen en onderhouden van toepassingen makkelijker en productiever. Met SQL Database kunt u zich concentreren op dat waar u het beste in bent: fantastische apps bouwen. In SQL Database kunt u beheren en ontwikkelen met de hulpprogramma’s en vaardigheden die u al hebt.

- **[De Azure Portal](https://portal.azure.com/)** :

  Een webtoepassing voor het beheren van alle Azure-Services
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)** :

  Een gratis, Download bare client toepassing voor het beheren van een SQL-infra structuur, van SQL Server tot SQL Database
- **[SQL Server Data tools in Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)** :

  Een gratis, Download bare client toepassing voor het ontwikkelen van SQL Server relationele data bases, Azure SQL-data bases, integratie Services-pakketten, Analysis Services gegevens modellen en rapporten van Reporting Services.
- **[Visual Studio code](https://code.visualstudio.com/docs)** :

  Een gratis, Download bare, open-source code-editor voor Windows, macOS en Linux die ondersteuning biedt voor extensies, waaronder de [MSSQL-extensie](https://aka.ms/mssql-marketplace) voor het uitvoeren van query's in Microsoft SQL Server, Azure SQL Database en SQL Data Warehouse.

SQL Database ondersteunt het maken van toepassingen met Python, Java, Node.js, PHP, Ruby en .NET op macOS, Linux en Windows. SQL Database ondersteunt dezelfde [verbindingsbibliotheken](sql-database-libraries.md) als SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions-faq"></a>Veelgestelde vragen over SQL Database

### <a name="what-is-the-current-version-of-sql-database"></a>Wat is de huidige versie van SQL Database

De huidige versie van SQL Database is V12. Versie V11 is buiten gebruik gesteld.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Kan ik bepalen wanneer uitval tijd van patches optreedt

Nee. De impact van patching is doorgaans niet merkbaar als u [probeert logica](sql-database-develop-overview.md#resiliency) in uw app te gebruiken. Voor meer informatie over het voorbereiden van geplande onderhouds gebeurtenissen in uw Azure SQL database raadpleegt u [Azure-onderhouds gebeurtenissen plannen in Azure SQL database](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Vragen over Azure Hybrid Benefit

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Zijn er twee gebruiks rechten met Azure Hybrid Benefit voor SQL Server

U hebt 180 dagen aan dubbele gebruiks rechten van de licentie om ervoor te zorgen dat migraties naadloos worden uitgevoerd. Na deze periode van 180 dagen kan de SQL Server-licentie alleen worden gebruikt in de cloud in SQL Database en heeft deze geen rechten voor dubbel gebruik op locatie en in de Cloud.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Hoe verschilt Azure Hybrid Benefit voor SQL Server van License Mobility

Vandaag bieden we de voor delen van License Mobility aan SQL Server klanten met Software Assurance, waarmee hun licenties opnieuw kunnen worden toegewezen aan gedeelde servers van derden. Dit voor deel kan worden gebruikt in azure IaaS en AWS EC2.
Azure Hybrid Benefit voor SQL Server wijkt af van de mobiliteit van licenties op twee belang rijke gebieden:

- Dit biedt economische voor delen voor het verplaatsen van uiterst gevirtualiseerde werk belastingen naar Azure. SQL EE-klanten kunnen vier kernen in azure verkrijgen in de Algemeen SKU voor elke kern waarover ze on-premises beschikken voor zeer gevirtualiseerde toepassingen. Licentie mobiliteit staat geen speciale kosten voordelen toe voor het verplaatsen van gevirtualiseerde werk belastingen naar de Cloud.
- Het biedt een PaaS-doel op Azure (SQL Database Managed instance) dat zeer compatibel is met SQL Server on-premises

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Wat zijn de specifieke rechten van de Azure Hybrid Benefit voor SQL Server

SQL Database klanten beschikken over de volgende rechten die zijn gekoppeld aan Azure Hybrid Benefit voor SQL Server:

|Licentie footprint|Wat doet Azure Hybrid Benefit voor SQL Server u doen?|
|---|---|
|SQL Server Enterprise Edition core-klanten met SA|<li>Kan het basis tarief betalen op Algemeen of Bedrijfskritiek SKU</li><br><li>1 kern on-premises = 4 kernen in Algemeen SKU</li><br><li>1 kern on-premises = 1 kern geheugen in Bedrijfskritiek SKU</li>|
|SQL Server Standard Edition core-klanten met SA|<li>Het basis tarief voor alleen Algemeen SKU kan worden betaald</li><br><li>1 kern on-premises = 1 kern geheugen in Algemeen SKU</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Contact met het technische team van SQL Server

- [DBA stack-uitwisseling](https://dba.stackexchange.com/questions/tagged/sql-server): Vragen over het beheer van data bases vragen
- [Stack overflow](https://stackoverflow.com/questions/tagged/sql-server): Ontwikkel vragen stellen
- [MSDN-Forums](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Technische vragen stellen
- [Feedback](https://aka.ms/sqlfeedback): Fouten en aanvraag functie melden
- [Reddit](https://www.reddit.com/r/SQLServer/): SQL Server bespreken

## <a name="next-steps"></a>Volgende stappen

- Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/sql-database/) voor hulpprogramma's voor het berekenen en vergelijken van de kosten voor individuele databases en elastische pools.
- Zie deze Quick Starts om snel aan de slag te gaan:

  - [Een SQL-database maken in Azure Portal](sql-database-single-database-get-started.md)  
  - [Een SQL-database maken met de Azure CLI](sql-database-get-started-cli.md)
  - [Een SQL-database maken met PowerShell](sql-database-get-started-powershell.md)

- Zie de volgende artikelen voor een reeks Azure CLI- en PowerShell-voorbeelden:
  - [Azure CLI-voorbeelden voor SQL Database](sql-database-cli-samples.md)
  - [Azure PowerShell-voorbeelden voor SQL Database](sql-database-powershell-samples.md)

 - Zie voor informatie over de nieuwe mogelijkheden die worden aangekondigd. 
   - **[Azure-route kaart voor SQL database](https://azure.microsoft.com/roadmap/?category=databases)** : een plek waar u kunt ontdekken wat er nieuw is en wat er op het volgende wordt weer te vinden.
  - **[Azure SQL database blog](https://azure.microsoft.com/blog/topics/database)** : een plek waar SQL Server product team leden blog over SQL database nieuws en functies.

