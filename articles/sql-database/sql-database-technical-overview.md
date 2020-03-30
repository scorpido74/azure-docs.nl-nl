---
title: Wat is de service Azure SQL Database?
description: 'Krijg een inleiding tot SQL Database: technische details en mogelijkheden van het Microsoft relationele database management systeem (RDBMS) in de cloud.'
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
ms.openlocfilehash: 209b4136678e6f04666b4a2b6180f4768bf6afc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79500824"
---
# <a name="what-is-the-azure-sql-database-service"></a>Wat is de service Azure SQL Database?

Azure SQL Database is een relationele database voor algemene doeleinden, die wordt geleverd als een beheerde service. Hiermee u een zeer beschikbare en krachtige gegevensopslaglaag maken voor de toepassingen en oplossingen in Azure. SQL Database kan de juiste keuze zijn voor een verscheidenheid aan moderne cloudtoepassingen, omdat u hiermee zowel relationele gegevens als [niet-relationele structuren](sql-database-multi-model-features.md)verwerken, zoals grafieken, JSON, ruimtelijk en XML.

Het is gebaseerd op de nieuwste stabiele versie van de [Microsoft SQL Server-databaseengine.](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) U geavanceerde functies voor queryverwerking gebruiken, zoals [krachtige in-memorytechnologieën](sql-database-in-memory.md) en [intelligente queryverwerking.](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json) In feite worden de nieuwste mogelijkheden van SQL Server eerst vrijgegeven aan SQL Database en vervolgens naar SQL Server zelf. U krijgt de nieuwste SQL Server-mogelijkheden zonder overhead voor het patchen of upgraden, getest in miljoenen databases. 

SQL Database stelt u in staat om eenvoudig prestaties te definiëren en te schalen binnen twee verschillende inkoopmodellen: een [vCore-gebaseerd inkoopmodel](sql-database-service-tiers-vcore.md) en een [DTU-gebaseerd inkoopmodel.](sql-database-service-tiers-dtu.md) SQL Database is een volledig beheerde service met ingebouwde hoge beschikbaarheid, back-ups en andere veelvoorkomende onderhoudsbewerkingen. Microsoft verwerkt alle patching en het bijwerken van de SQL- en besturingssysteemcode. U hoeft de onderliggende infrastructuur niet te beheren.

> [!NOTE]
> Zie de [woordenlijst voor SQL Database-termen](sql-database-glossary-terms.md)voor relevante termen en hun definities.

## <a name="deployment-models"></a>Implementatiemodellen

Azure SQL Database biedt de volgende implementatieopties voor een Azure SQL-database:

![Diagram met implementatieopties](./media/sql-database-technical-overview/deployment-options.png)

- [Eén database](sql-database-single-database.md) vertegenwoordigt een volledig beheerde, geïsoleerde database. U deze optie gebruiken als u moderne cloudtoepassingen en microservices hebt die één betrouwbare gegevensbron nodig hebben. Eén database is vergelijkbaar met een [database in](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) Microsoft SQL Server [Database Engine.](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json)
- [Beheerde instantie](sql-database-managed-instance.md) is een volledig beheerde instantie van de [Microsoft SQL Server Database Engine.](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) Het bevat een set databases die samen kunnen worden gebruikt. Gebruik deze optie voor eenvoudige migratie van on-premises SQL Server-databases naar de Azure-cloud en voor toepassingen die de databasefuncties moeten gebruiken die SQL Server Database Engine biedt.
- [Elastic pool](sql-database-elastic-pool.md) is een verzameling [van afzonderlijke databases](sql-database-single-database.md) met een gedeelde set resources, zoals CPU of geheugen. Afzonderlijke databases kunnen worden verplaatst naar en uit een elastische pool.

> [!IMPORTANT]
> Zie [SQL Database-functies](sql-database-features.md)voor inzicht in de functieverschillen tussen SQL Database en SQL Server en de verschillen tussen de verschillende azure SQL-database-implementatieopties.

SQL Database biedt voorspelbare prestaties met meerdere resourcetypen, servicelagen en rekengroottes. Het biedt dynamische schaalbaarheid zonder downtime, ingebouwde intelligente optimalisatie, wereldwijde schaalbaarheid en beschikbaarheid en geavanceerde beveiligingsopties. Met deze mogelijkheden u zich richten op snelle app-ontwikkeling en het versnellen van uw time-to-market, in plaats van op het beheren van virtuele machines en infrastructuur. De SQL Database-service bevindt zich momenteel in 38 datacenters over de hele wereld, zodat u uw database uitvoeren in een datacenter bij u in de buurt.

## <a name="scalable-performance-and-pools"></a>Schaalbare prestaties en pools

U de toegewezen hoeveelheid toegewezen resources definiëren. 
- Met enkele databases is elke database geïsoleerd van anderen en draagbaar. Elk heeft zijn eigen gegarandeerde hoeveelheid reken-, geheugen- en opslagbronnen. Het bedrag van de resources die aan de database zijn toegewezen, is toegewezen aan die database en wordt niet gedeeld met andere databases in Azure. U [afzonderlijke databasebronnen](sql-database-single-database-scale.md) dynamisch op en neer schalen. De optie met één database biedt verschillende reken-, geheugen- en opslagbronnen voor verschillende behoeften. U bijvoorbeeld 1 tot 80 vCores of 32 GB tot 4 TB krijgen. De [hyperscale servicelaag](sql-database-service-tier-hyperscale.md) voor één database stelt u in staat om te schalen naar 100 TB, met snelle back-up- en herstelmogelijkheden.
- Met elastische groepen u resources toewijzen die worden gedeeld door alle databases in de groep. U een nieuwe database maken of de bestaande afzonderlijke databases verplaatsen naar een resourcegroep om het gebruik van resources te maximaliseren en geld te besparen. Deze optie geeft u ook de mogelijkheid om [elastische poolresources](sql-database-elastic-pool-scale.md) dynamisch op en neer te schalen.
- Bij beheerde instanties wordt elke beheerde instantie geïsoleerd van andere instanties met gegarandeerde resources. Binnen een beheerde instantie delen de instantiedatabases een reeks resources. U [beheerde instantieresources](sql-database-managed-instance-resource-limits.md) dynamisch op en neer schalen.

U uw eerste app bouwen op een kleine, enkele database tegen lage kosten per maand in de servicelaag voor algemene doeleinden. U de servicelaag vervolgens op elk gewenst moment handmatig of programmatisch wijzigen in de bedrijfskritieke servicelaag, om aan de behoeften van uw oplossing te voldoen. U kunt het prestatieniveau aanpassen zonder uitvaltijd voor uw app of voor uw klanten. Doordat de schaalbaarheid dynamisch is, kan uw database op een transparante manier reageren op snel veranderende resourcevereisten. U betaalt alleen voor de middelen die u nodig hebt wanneer u ze nodig hebt.

*Dynamische schaalbaarheid* is anders dan *automatisch schalen.* Automatisch schalen vindt plaats wanneer een service automatisch wordt geschaald op basis van criteria, terwijl u met dynamische schaalbaarheid handmatig kunt schalen zonder uitvaltijd. De optie met één database ondersteunt handmatige dynamische schaalbaarheid, maar niet automatisch schalen. Voor een meer automatische ervaring zou u elastische pools kunnen gebruiken. Hiermee kunnen databases resources in een pool delen op basis van afzonderlijke databasebehoeften. Een andere optie is het gebruik van scripts die kunnen helpen bij het automatiseren van schaalbaarheid voor een enkele database. Zie [PowerShell gebruiken om één database te controleren en te schalen](scripts/sql-database-monitor-and-scale-database-powershell.md)voor een voorbeeld.

### <a name="purchasing-models"></a>Aankoopmodellen

SQL Database biedt de volgende inkoopmodellen:
- Met [het op vCore gebaseerde inkoopmodel](sql-database-service-tiers-vcore.md) u het aantal vCores, de hoeveelheid geheugen en de hoeveelheid en de snelheid van de opslag kiezen. Met het op vCore gebaseerde inkoopmodel u azure [hybrid benefit voor SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) ook gebruiken om kostenbesparingen te behalen. Zie de sectie 'Veelgestelde vragen' later in dit artikel voor meer informatie over het Azure Hybrid Benefit.
- Het [op DTU gebaseerde inkoopmodel](sql-database-service-tiers-dtu.md) biedt een mix van compute-, geheugen- en I/O-resources in drie servicelagen, ter ondersteuning van lichte tot zware databaseworkloads. Rekengroottes binnen elke laag bieden een andere mix van deze resources, waaraan u extra opslagbronnen toevoegen.
- Het [serverless-model](sql-database-serverless.md) schaalt automatisch de berekening op basis van de workloadvraag en de facturen voor de hoeveelheid rekenkracht die per seconde wordt gebruikt. De serverless compute-laag pauzeert ook automatisch databases tijdens inactieve perioden wanneer alleen opslag wordt gefactureerd en hervat automatisch databases wanneer de activiteit terugkeert.

### <a name="service-tiers"></a>Servicelagen

Azure SQL Database biedt drie servicelagen die zijn ontworpen voor verschillende typen toepassingen:
- [Servicelaag algemeen gebruik/standaard](sql-database-service-tier-general-purpose.md) die is ontworpen voor veelvoorkomende workloads. Het biedt budgetgerichte gebalanceerde reken- en opslagopties.
- [Business Critical/Premium-servicelaag](sql-database-service-tier-business-critical.md) ontworpen voor OLTP-toepassingen met een hoge transactiesnelheid en i/o met de laagste latentie. Het biedt de hoogste veerkracht voor storingen met behulp van verschillende geïsoleerde replica's.
- [Hyperscale](sql-database-service-tier-hyperscale.md) service tier ontworpen voor zeer grote OLTP database en de mogelijkheid om automatisch schaal opslag en schaal rekenvloeistof.    

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Elastische pools voor optimaal resourcegebruik

Voor veel bedrijven en toepassingen is het kunnen maken van enkele databases en het naar wens omhoog of omlaag schalen van de prestaties al voldoende, vooral als de gebruikspatronen redelijk voorspelbaar zijn. Onvoorspelbare gebruikspatronen kunnen het moeilijk maken om kosten en uw bedrijfsmodel te beheren. [Elastische pools](sql-database-elastic-pool.md) zijn ontworpen om dit probleem te verhelpen. U wijst prestatieresources toe aan een groep in plaats van aan een afzonderlijke database. U betaalt voor de collectieve prestatiebronnen van de groep in plaats van voor afzonderlijke databaseprestaties.

   ![Afbeelding die elastische pools toont in basis-, standaard- en premiumedities](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Met elastische pools hoeft u zich niet te concentreren op het op en neer draaien van databaseprestaties, omdat de vraag naar resources fluctueert. De pooldatabases maken naar behoefte gebruik van de prestatieresources van de elastische pool. Gepoolde databases verbruiken, maar overschrijden de limieten van de groep niet, zodat uw kosten voorspelbaar blijven, zelfs als het individuele databasegebruik dat niet doet.

U [databases toevoegen en verwijderen aan de groep,](sql-database-elastic-pool-manage-portal.md)uw app schalen van een handvol databases naar duizenden, allemaal binnen een budget dat u beheert. U ook de minimale en maximale resources beheren die beschikbaar zijn voor databases in de groep, om ervoor te zorgen dat geen enkele database in de groep alle poolbronnen gebruikt en dat elke samengevoegde database een gegarandeerde minimumhoeveelheid resources heeft. Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenant saas-toepassingen met SQL Database voor](sql-database-design-patterns-multi-tenancy-saas-applications.md)meer informatie over ontwerppatronen voor software as a service -toepassingen (SaaS).

Scripts kunnen helpen bij het bewaken en schalen van elastische pools. Zie bijvoorbeeld [PowerShell gebruiken om een SQL-elastische groep in Azure SQL Database te controleren en te schalen.](scripts/sql-database-monitor-and-scale-pool-powershell.md)

> [!IMPORTANT]
> Een beheerde instantie ondersteunt geen elastische pools. Een beheerde instantie is eerder een verzameling instantiedatabases die beheerde instantiebronnen delen.

### <a name="blend-single-databases-with-pooled-databases"></a>Individuele databases combineren met pooldatabases

U afzonderlijke databases combineren met elastische pools en de servicelagen van afzonderlijke databases en elastische pools wijzigen om zich aan te passen aan uw situatie. U ook andere Azure-services combineren met SQL Database om te voldoen aan uw unieke ontwerpbehoeften van apps, kosten- en resourceefficiëntie te stimuleren en nieuwe zakelijke kansen te ontgrendelen.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Uitgebreide mogelijkheden voor bewaking en waarschuwingen

Azure SQL Database biedt geavanceerde functies voor monitoring en probleemoplossing waarmee u meer inzicht krijgt in de workloadkenmerken. Deze functies en tools omvatten:
 - De ingebouwde bewakingsmogelijkheden van de nieuwste versie van SQL Server Database Engine. Ze stellen u in staat om real-time prestatie-inzichten te vinden. 
 - PaaS-bewakingsmogelijkheden van Azure waarmee u een groot aantal database-exemplaren controleren en oplossen.

[Query Store](https://docs.microsoft.com/sql/relational-databases/performance/best-practice-with-the-query-store), een ingebouwde SQL Server-bewakingsfunctie, registreert de prestaties van uw query's in realtime en stelt u in staat om de potentiële prestatieproblemen en de beste bronconsumenten te identificeren. Automatische afstemming en aanbevelingen geven advies met betrekking tot de query's met de regressieprestaties en ontbrekende of gedupliceerde indexen. Met automatische afstemming in SQL Database u de scripts handmatig toepassen die de problemen kunnen oplossen of SQL Database de oplossing laten toepassen. SQL Database kan ook testen en controleren of de oplossing enig voordeel biedt en de wijziging behouden of terugdraaien, afhankelijk van de uitkomst. Naast querywinkel- en automatische afstemmingsmogelijkheden u standaard [DMVs en XEvent](sql-database-monitoring-with-dmvs.md) gebruiken om de werkbelastingprestaties te controleren.

Azure biedt [ingebouwde prestatiebewaking-](sql-database-performance-guidance.md) en [waarschuwingstools,](sql-database-insights-alerts-portal.md) gecombineerd met prestatieclassificaties, waarmee u de status van duizenden databases controleren. Met behulp van deze tools u snel de impact van op- of afschalen beoordelen op basis van uw huidige of verwachte prestatiebehoeften. Daarnaast kan SQL Database [metrische gegevens en diagnostische logboeken verzenden](sql-database-metrics-diag-logging.md) die de bewaking vergemakkelijken. U kunt SQL Database configureren voor het opslaan van resourcegebruik, werkrollen en sessies, en connectiviteit in een van deze Azure-resources:

- **Azure Storage:** voor het archiveren van grote hoeveelheden telemetrie voor een kleine prijs.
- **Azure Event Hubs:** voor het integreren van SQL Database telemetrie met uw aangepaste bewakingsoplossing of hot pipelines.
- **Azure Monitor-logboeken:** voor een ingebouwde bewakingsoplossing met rapportage-, waarschuwings- en mitigerende mogelijkheden.

![Diagram met Azure-bewakingsarchitectuur](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Beschikbaarheid

In een traditionele SQL Server-omgeving hebt u over het algemeen ten minste twee machines lokaal ingesteld. Deze machines hebben exacte, synchroon onderhouden kopieën van de gegevens om te beschermen tegen een storing van een enkele machine of component. Deze omgeving biedt een hoge beschikbaarheid, maar beschermt niet tegen een natuurramp die uw datacenter vernietigt.

Disaster recovery gaat ervan uit dat een catastrofale gebeurtenis geografisch gelokaliseerd genoeg is om een andere machine of set machines te hebben met een kopie van uw gegevens ver weg. In SQL Server u Always On Availability Groups gebruiken die in de async-modus worden uitgevoerd om deze mogelijkheid te krijgen. Mensen willen vaak niet wachten tot replicatie zo ver weg gebeurt voordat ze een transactie plegen, dus er is potentieel voor gegevensverlies wanneer u ongeplande failovers uitvoert.

Databases in de premium- en bedrijfskritieke servicelagen doen al [iets vergelijkbaars met](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) de synchronisatie van een beschikbaarheidsgroep. Databases in lagere servicelagen bieden redundantie door middel van opslag met behulp van een [ander, maar gelijkwaardig mechanisme](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability). Ingebouwde logica helpt beschermen tegen een enkele machine storing. De actieve geo-replicatie functie geeft u de mogelijkheid om te beschermen tegen een ramp waar een hele regio wordt vernietigd.

Azure Availability Zones probeert te beschermen tegen de uitval van één datacentergebouw binnen één regio. Het helpt u te beschermen tegen het verlies van stroom of netwerk aan een gebouw. In SQL Database plaatst u de verschillende replica's in verschillende beschikbaarheidszones (verschillende gebouwen, effectief).

In feite helpt de service level [agreement (SLA)](https://azure.microsoft.com/support/legal/sla/) van Azure, aangedreven door een wereldwijd netwerk van door Microsoft beheerde datacenters, uw app 24/7 draaiende te houden. Het Azure-platform beheert elke database volledig en garandeert geen gegevensverlies en een hoog percentage van de beschikbaarheid van gegevens. Azure verwerkt automatisch patching, back-ups, replicatie, foutdetectie, onderliggende potentiële hardware-, software- of netwerkfouten, het implementeren van bugfixes, failovers, database-upgrades en andere onderhoudstaken. Standaard-beschikbaarheid wordt bereikt door de lagen voor berekeningen en opslag te scheiden. Premium beschikbaarheid wordt bereikt door compute en storage te integreren op één knooppunt voor prestaties en vervolgens technologie te implementeren die vergelijkbaar is met Always On Availability Groups. Zie [SQL Database beschikbaarheid](sql-database-high-availability.md)voor een volledige bespreking van de mogelijkheden met hoge beschikbaarheid van Azure SQL Database. 

Daarnaast biedt SQL Database ingebouwde [functies voor bedrijfscontinuïteit en wereldwijde schaalbaarheid.](sql-database-business-continuity.md) Deze omvatten:

- [Automatische back-ups:](sql-database-automated-backups.md)

  SQL Database voert automatisch volledige, differentiële en transactielogboekbacks van SQL-databases uit, zodat u herstellen naar elk moment. Voor afzonderlijke databases en gepoolde databases u SQL Database configureren om volledige databaseback-ups op te slaan in Azure Storage voor het bewaren van back-ups op lange termijn. Voor beheerde exemplaren u ook alleen-kopiëren back-ups uitvoeren voor het bewaren van back-ups op lange termijn.

- [Point-in-time herstelt:](sql-database-recovery-using-backups.md)

  Alle SQL Database-implementatieopties ondersteunen herstel tot elk moment binnen de automatische bewaartermijn voor elke SQL-database.
- [Actieve georeplicatie:](sql-database-active-geo-replication.md)

  Met de opties voor één database en samengevoegde databases u maximaal vier leesbare secundaire databases configureren in dezelfde of wereldwijd gedistribueerde Azure-datacenters. Als u bijvoorbeeld een SaaS-toepassing hebt met een catalogusdatabase met een hoog volume van gelijktijdige alleen-lezen transacties, gebruikt u actieve georeplicatie om globale leesschaal in te schakelen. Hiermee worden knelpunten op de primaire primaire die te wijten zijn aan leeswerkbelastingen verwijderd. Voor beheerde instanties gebruikt u groepen voor automatische failover.
- [Auto-failover groepen:](sql-database-auto-failover-group.md)

  Met alle SQL Database-implementatieopties u failovergroepen gebruiken om hoge beschikbaarheid en taakverdeling op wereldwijde schaal mogelijk te maken. Dit omvat transparante geo-replicatie en failover van grote sets databases, elastische pools en beheerde exemplaren. Failovergroepen maken het mogelijk om wereldwijd gedistribueerde SaaS-toepassingen te maken, met minimale beheeroverhead. Hierdoor blijft alle complexe monitoring, routing en failoverorchestration over in SQL Database.
- [Zoneredundante databases](sql-database-high-availability.md):

  SQL Database stelt u in staat om premium of bedrijfskritische databases of elastische pools in te richten in meerdere beschikbaarheidszones. Omdat deze databases en elastische pools meerdere redundante replica's hebben voor hoge beschikbaarheid, biedt het plaatsen van deze replica's in meerdere beschikbaarheidszones een hogere veerkracht. Dit omvat de mogelijkheid om automatisch te herstellen van de fouten in de datacenterschaal, zonder gegevensverlies.

## <a name="built-in-intelligence"></a>Ingebouwde intelligentie

Met SQL Database krijgt u ingebouwde informatie die u helpt de kosten van het uitvoeren en beheren van databases drastisch te verlagen en die zowel de prestaties als de beveiliging van uw toepassing maximaliseert. SQL Database, dat de klok rond miljoenen workloads van klanten uitvoert, verzamelt en verwerkt een enorme hoeveelheid telemetriegegevens, terwijl het ook de privacy van klanten volledig respecteert. Verschillende algoritmen evalueren continu de telemetriegegevens, zodat de service kan leren en aanpassen aan uw toepassing.

### <a name="automatic-performance-monitoring-and-tuning"></a>Automatisch bewaking en afstemming van prestaties

SQL Database biedt gedetailleerde informatie over de query’s die u wilt bewaken. SQL Database leert over uw databasepatronen en stelt u in staat om uw databaseschema aan te passen aan uw werkbelasting. SQL Database geeft [aanbevelingen voor het afstemmen van de prestaties](sql-database-advisor.md), waarmee u de aanbevolen acties kunt weergeven en uitvoeren.

Echter, voortdurend toezicht op een database is een harde en vervelende taak, vooral bij het omgaan met veel databases. [Intelligent Insights](sql-database-intelligent-insights.md) doet dit werk voor u door de sql-databaseprestaties automatisch op schaal te monitoren. Het informeert u over prestatiedegradatieproblemen, identificeert de hoofdoorzaak van elk probleem en biedt waar mogelijk aanbevelingen voor prestatieverbetering.

Het beheren van een groot aantal databases is mogelijk onmogelijk om efficiënt te doen, zelfs met alle beschikbare hulpprogramma's en rapporten die SQL Database en Azure bieden. In plaats van uw database handmatig te controleren en af te stemmen, u overwegen om een aantal van de controle- en tuningacties te delegeren naar SQL Database met behulp van [automatische afstemming.](sql-database-automatic-tuning.md) SQL Database past automatisch aanbevelingen, tests en verifieert elk van haar tuningacties om ervoor te zorgen dat de prestaties blijven verbeteren. Op deze manier past SQL Database zich automatisch op een gecontroleerde en veilige manier aan uw workload aan. Automatische afstemming betekent dat de prestaties van uw database zorgvuldig worden gecontroleerd en vergeleken voor en na elke tuningactie. Als de prestaties niet verbeteren, wordt de tuningactie teruggedraaid.

Veel van onze partners die [SaaS multi-tenant apps](sql-database-design-patterns-multi-tenancy-saas-applications.md) bovenop SQL Database uitvoeren, vertrouwen op automatische prestatieafstemming om ervoor te zorgen dat hun toepassingen altijd stabiele en voorspelbare prestaties hebben. Dankzij deze functie wordt de kans op prestatieproblemen (op welk moment dan ook) aanzienlijk kleiner. Omdat een deel van hun klantenbestand ook SQL Server gebruikt, gebruiken ze bovendien dezelfde indexeringsaanbevelingen van SQL Database om hun SQL Server-klanten te helpen.

Er zijn twee automatische tuningaspecten [beschikbaar in SQL Database:](sql-database-automatic-tuning.md)

- **Automatisch indexbeheer**: hiermee worden indexen geïdentificeerd die moeten worden toegevoegd aan of verwijderd uit uw database.
- **Automatische plancorrectie**: identificeert problematische plannen en lost SQL-planprestatieproblemen op.

### <a name="adaptive-query-processing"></a>Verwerking van adaptieve query’s

U [adaptieve queryverwerking](/sql/relational-databases/performance/intelligent-query-processing)gebruiken, inclusief interleaved-uitvoering voor functies met een tabel met meerdere overzichten, feedback voor batchmodusgeheugenverlening en adaptieve joins in de batchmodus. Elk van deze adaptieve queryverwerkingsfuncties past vergelijkbare "learn and adapt"-technieken toe, waardoor prestatieproblemen in verband met historisch hardnekkige queryoptimalisatieproblemen verder worden aangepakt.

## <a name="advanced-security-and-compliance"></a>Geavanceerde beveiliging en naleving van voorschriften

SQL Database biedt een reeks [ingebouwde functies voor beveiliging en naleving](sql-database-security-overview.md) zodat uw toepassing voldoet aan diverse vereisten op het gebied van beveiliging en de naleving van voorschriften.

> [!IMPORTANT]
> Microsoft heeft Azure SQL Database (alle implementatieopties) gecertificeerd op basis van een aantal nalevingsstandaarden. Zie het Microsoft [Azure Trust Center](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), waar u de meest recente lijst met SQL Database-nalevingscertificeringen vinden voor meer informatie.

### <a name="advance-threat-protection"></a>Bescherming tegen bedreigingen vooraf

Geavanceerde gegevensbeveiliging is een uniform pakket voor geavanceerde SQL-beveiligingsmogelijkheden. Het bevat functionaliteit voor het ontdekken en classificeren van gevoelige gegevens, het beheren van uw database kwetsbaarheden, en het detecteren van afwijkende activiteiten die kunnen wijzen op een bedreiging voor uw database. Het biedt één locatie voor het inschakelen en beheren van deze mogelijkheden.

- [Gegevensdetectie en -classificatie](sql-database-data-discovery-and-classification.md):

  Deze functie biedt mogelijkheden die zijn ingebouwd in Azure SQL Database voor het ontdekken, classificeren, labelen en beschermen van de gevoelige gegevens in uw databases. Het biedt inzicht in de status van uw databaseclassificatie en houdt de toegang tot gevoelige gegevens binnen de database en buiten de randen bij.
- [Kwetsbaarheidsbeoordeling](sql-vulnerability-assessment.md):

  Deze service kan potentiële databasekwetsbaarheden opsporen, bijhouden en u helpen deze te verhelpen. Deze service biedt u inzicht in de status van de beveiliging en bruikbare stappen om beveiligingsproblemen op te lossen en de beveiliging van uw database te verbeteren.
- [Detectie van bedreigingen:](sql-database-threat-detection.md)

  Deze functie detecteert afwijkende activiteiten die duiden op ongebruikelijke en mogelijk schadelijke pogingen om toegang te krijgen tot uw database of deze te exploiteren. Hiermee wordt uw database continu gecontroleerd op verdachte activiteiten en wordt u onmiddellijk gewaarschuwd bij mogelijke beveiligingsproblemen, SQL-injectieaanvallen en afwijkende databasetoegangspatronen. Waarschuwingen voor bedreigingsdetectie geven details over de verdachte activiteit en bevelen actie aan voor het onderzoeken en beperken van de dreiging.

### <a name="auditing-for-compliance-and-security"></a>Controles voor naleving en beveiliging

[Controle](sql-database-auditing.md) houdt databasegebeurtenissen bij en schrijft deze naar een controlelogboek in uw Azure-opslagaccount. Controle kan u helpen de naleving van de regelgeving te handhaven, de databaseactiviteit te begrijpen en inzicht te krijgen in verschillen en afwijkingen die kunnen duiden op zakelijke problemen of vermoedelijke beveiligingsschendingen.

### <a name="data-encryption"></a>Gegevensversleuteling

SQL Database helpt uw gegevens te beveiligen door versleuteling te bieden. Voor gegevens in beweging wordt gebruikgenomen van [transportlaagbeveiliging.](https://support.microsoft.com/kb/3135244) Voor gegevens in rust, maakt het gebruik van [transparante gegevensversleuteling](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). Voor gegevens in gebruik, het maakt gebruik [van altijd versleuteld](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integratie en meervoudige verificatie

Dankzij [Azure Active Directory-integratie](sql-database-aad-authentication.md) kunt u in SQL Database de identiteit van databasegebruikers en andere Microsoft-services centraal beheren. Deze mogelijkheid vereenvoudigt het beheer van machtigingen en verbetert de beveiliging. Azure Active Directory ondersteunt [meervoudige verificatie](sql-database-ssms-mfa-authentication.md) om de gegevens- en toepassingsbeveiliging te verhogen en tegelijkertijd één aanmeldingsproces te ondersteunen.

## <a name="easy-to-use-tools"></a>Gebruiksvriendelijke hulpprogramma’s

SQL Database maakt het bouwen en onderhouden van toepassingen makkelijker en productiever. Met SQL Database kunt u zich concentreren op dat waar u het beste in bent: fantastische apps bouwen. U SQL Database beheren en ontwikkelen met behulp van tools en vaardigheden die u al hebt.

- [De Azure-portal:](https://portal.azure.com/)

  Een webgebaseerde toepassing voor het beheren van alle Azure-services.
- [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms):

  Een gratis, downloadbare clienttoepassing voor het beheren van elke SQL-infrastructuur, van SQL Server tot SQL Database.
- [SQL Server-gegevenshulpprogramma's in Visual Studio:](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)

  Een gratis, downloadbare clienttoepassing voor het ontwikkelen van SQL Server relationele databases, SQL-databases, Integration Services-pakketten, Analysis Services-gegevensmodellen en Rapportageservices-rapporten.
- [Visual Studio Code](https://code.visualstudio.com/docs):

  Een gratis, downloadbare, open-source code-editor voor Windows, macOS en Linux. Het ondersteunt extensies, waaronder de [mssql-extensie](https://aka.ms/mssql-marketplace) voor het opvragen van Microsoft SQL Server, Azure SQL Database en Azure SQL Data Warehouse.

SQL Database ondersteunt het bouwen van applicaties met Python, Java, Node.js, PHP, Ruby en .NET op macOS, Linux en Windows. SQL Database ondersteunt dezelfde [verbindingsbibliotheken](sql-database-libraries.md) als SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions"></a>SQL Database veelgestelde vragen

### <a name="what-is-the-current-version-of-sql-database"></a>Wat is de huidige versie van SQL Database?

De huidige versie van SQL Database is V12. Versie V11 is met pensioen.

### <a name="can-i-control-when-patching-downtime-occurs"></a>Kan ik bepalen wanneer downtime wordt gepatcht?

Nee. De impact van patchen is over het algemeen niet merkbaar als u [logica opnieuw probeert](sql-database-develop-overview.md#resiliency) in uw app. Zie [Planning voor Azure-onderhoudsgebeurtenissen in Azure SQL Database voor](sql-database-planned-maintenance.md)meer informatie.

### <a name="azure-hybrid-benefit-questions"></a>Azure Hybrid Benefit-vragen

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Zijn er twee-gebruiksrechten met Azure Hybrid Benefit voor SQL Server?

U hebt 180 dagen rechten voor tweeërlei gebruik van de licentie om ervoor te zorgen dat migraties naadloos verlopen. Na die periode van 180 dagen u de SQL Server-licentie alleen gebruiken in de cloud in SQL Database. U hebt geen rechten voor tweeërlei gebruik meer on-premises en in de cloud.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Hoe verschilt Azure Hybrid Benefit voor SQL Server van licentiemobiliteit?

We bieden sql server-klanten voordelen voor licentiemobiliteit met Software Assurance. Hierdoor kunnen hun licenties worden toegewezen aan de gedeelde servers van een partner. U dit voordeel gebruiken op Azure IaaS en AWS EC2.

Azure Hybrid Benefit voor SQL Server verschilt van licentiemobiliteit op twee belangrijke gebieden:

- Het biedt economische voordelen voor het verplaatsen van zeer gevirtualiseerde workloads naar Azure. SQL Server Enterprise Edition-klanten kunnen vier cores in Azure krijgen in de General Purpose SKU voor elke kern die ze on-premises bezitten voor zeer gevirtualiseerde toepassingen. Licentiemobiliteit biedt geen speciale kostenvoordelen voor het verplaatsen van gevirtualiseerde workloads naar de cloud.
- Het voorziet in een PaaS-bestemming op Azure (SQL Database managed instance) die zeer compatibel is met SQL Server on-premises.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Wat zijn de specifieke rechten van het Azure Hybrid Benefit voor SQL Server?

SQL Database-klanten hebben de volgende rechten die zijn gekoppeld aan Azure Hybrid Benefit voor SQL Server:

|Licentievoetafdruk|Wat levert Azure Hybrid Benefit voor SQL Server u op?|
|---|---|
|SQL Server Enterprise Edition-kernklanten met SA|<li>Kan basistarief betalen op algemeen doel of bedrijfskritische SKU</li><br><li>1 kern on-premises = 4 kernen in General Purpose SKU</li><br><li>1 core on-premises = 1 core in Business Critical SKU</li>|
|SQL Server Standard Edition-kernklanten met SA|<li>Kan alleen basistarief betalen op SKU voor algemeen gebruik</li><br><li>1 kern on-premises = 1 kern in General Purpose SKU</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Contact met het technische team van SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): Stel vragen over databasebeheer.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): Stel ontwikkelingsvragen.
- [MSDN Forums](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): Stel technische vragen.
- [Feedback:](https://aka.ms/sqlfeedback)Meld bugs en aanvraagfunctie.
- [Reddit](https://www.reddit.com/r/SQLServer/): Bespreek SQL Server.

## <a name="next-steps"></a>Volgende stappen

- Zie de [prijspagina](https://azure.microsoft.com/pricing/details/sql-database/) voor kostenvergelijkingen en rekenmachines met betrekking tot afzonderlijke databases en elastische pools.
- Bekijk deze snel aan de slag:

  - [Een SQL-database maken in Azure Portal](sql-database-single-database-get-started.md)  
  - [Een SQL-database maken met de Azure CLI](sql-database-get-started-cli.md)
  - [Een SQL-database maken met PowerShell](sql-database-get-started-powershell.md)

- Zie de volgende artikelen voor een reeks Azure CLI- en PowerShell-voorbeelden:
  - [Azure CLI-voorbeelden voor SQL Database](sql-database-cli-samples.md)
  - [Azure PowerShell-voorbeelden voor SQL Database](sql-database-powershell-samples.md)

- Zie [Azure Roadmap voor SQL Database voor](https://azure.microsoft.com/roadmap/?category=databases)informatie over nieuwe mogelijkheden wanneer deze zijn aangekondigd.
- Zie de [Azure SQL Database-blog](https://azure.microsoft.com/blog/topics/database), waar SQL Server-productteamleden blogover SQL Database-nieuws en -functies.

