---
title: Aankoopmodellen
description: Meer informatie over de inkoopmodellen die beschikbaar zijn voor Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 97ce402045cfd2c990b457c5d4d06888cda632d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255988"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Kies tussen de vCore en de DTU inkoopmodellen

Met Azure SQL Database u eenvoudig een volledig beheerde PaaS-databaseengine (Service As AS) aanschaffen die voldoet aan uw prestatie- en kostenbehoeften. Afhankelijk van het implementatiemodel dat u hebt gekozen voor Azure SQL Database, u het inkoopmodel selecteren dat voor u werkt:

- [Virtual core (vCore)-gebaseerd inkoopmodel](sql-database-service-tiers-vcore.md) (aanbevolen). Dit inkoopmodel biedt de keuze tussen een ingerichte compute-laag en een serverloze compute-laag. Met de ingerichte compute-laag kiest u de exacte hoeveelheid rekenresources die altijd zijn ingericht voor uw werkbelasting. Met de serverloze compute-laag geeft u de automatische schaling van de compute resources op over een configureerbaar besturingssysteem. Met deze rekenlaag u de database ook automatisch onderbreken en hervatten op basis van werkbelastingactiviteit. De vCore-eenheidsprijs per tijdseenheid is lager in de ingerichte compute-laag dan in de serverloze compute-laag.
- [Op databasetransactie-eenheid (DTU)gebaseerd inkoopmodel](sql-database-service-tiers-dtu.md). Dit inkoopmodel biedt gebundelde reken- en opslagpakketten die in evenwicht zijn voor veelvoorkomende workloads.

Er zijn verschillende inkoopmodellen beschikbaar voor verschillende Azure SQL Database-implementatiemodellen:

- De [opties voor één database](sql-database-single-databases-manage.md) en [elastische poolimplementatie](sql-database-elastic-pool.md) in [Azure SQL Database](sql-database-technical-overview.md) bieden zowel het op [DTU gebaseerde inkoopmodel](sql-database-service-tiers-dtu.md) als het [op vCore gebaseerde inkoopmodel.](sql-database-service-tiers-vcore.md)
- De [optie beheerde instantieimplementatie](sql-database-managed-instance.md) in Azure SQL Database biedt alleen het [op vCore gebaseerde inkoopmodel](sql-database-service-tiers-vcore.md).
- De [hyperscale-servicelaag](sql-database-service-tier-hyperscale.md) is beschikbaar voor afzonderlijke databases die het [op vCore gebaseerde inkoopmodel](sql-database-service-tiers-vcore.md)gebruiken.

De volgende tabel en grafiek vergelijken en contrasteren de vCore-gebaseerde en de Op DTU gebaseerde inkoopmodellen:

|**Inkoopmodel**|**Beschrijving**|**Het beste voor**|
|---|---|---|
|Model op basis van DTU|Dit model is gebaseerd op een gebundelde meting van compute-, opslag- en I/O-resources. Rekengroottes worden uitgedrukt in DTU's voor afzonderlijke databases en in elastische databasetransactie-eenheden (eDTU's) voor elastische pools. Zie [Wat zijn DTU's en eDTU's voor meer informatie over DTU's en eDTU's?](sql-database-purchase-models.md#dtu-based-purchasing-model)|Het beste voor klanten die eenvoudige, vooraf geconfigureerde resourceopties willen.|
|Model op basis van vCore|Met dit model u onafhankelijk berekenende en opslagbronnen kiezen. Met het op vCore gebaseerde inkoopmodel u azure [hybrid benefit voor SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) ook gebruiken om kostenbesparingen te behalen.|Het beste voor klanten die waarde hechten aan flexibiliteit, controle en transparantie.|
||||  

![prijsmodelvergelijking](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Rekenkosten

### <a name="provisioned-compute-costs"></a>Ingerichte rekenkosten

In de ingerichte compute-laag weerspiegelen de rekenkosten de totale rekencapaciteit die voor de toepassing is ingericht.

In de servicelaag Bedrijfskritiek wijzen we automatisch ten minste 3 replica's toe. Om deze extra toewijzing van rekenresources weer te geven, is de prijs in het op vCore gebaseerde inkoopmodel ongeveer 2,7 x hoger in de servicelaag Bedrijfskritieke service dan in de servicelaag voor algemeen gebruik. Ook de hogere opslagprijs per GB in de business critical servicelaag weerspiegelt de hogere IO-limieten en de lagere latentie van de SSD-opslag.

De kosten van back-upopslag zijn hetzelfde voor de servicelaag Bedrijfskritieke service en de servicelaag voor algemeen gebruik, omdat beide lagen standaardopslag gebruiken voor back-ups.

### <a name="serverless-compute-costs"></a>Serverloze rekenkosten

Zie [SQL Database serverless](sql-database-serverless.md)voor een beschrijving van de manier waarop rekencapaciteit wordt gedefinieerd en kosten worden berekend voor de serverloze compute-laag.

## <a name="storage-costs"></a>Opslagkosten

Verschillende soorten opslag worden anders gefactureerd. Voor gegevensopslag worden kosten in rekening gebracht voor de ingerichte opslag op basis van de maximale database- of poolgrootte die u selecteert. De kosten veranderen niet, tenzij u dat maximum verlaagt of verhoogt. Back-upopslag is gekoppeld aan geautomatiseerde back-ups van uw instantie en wordt dynamisch toegewezen. Als u uw back-upbewaarperiode verhoogt, wordt de back-upopslag verhoogd die door uw instantie wordt verbruikt.

Standaard worden 7 dagen geautomatiseerde back-ups van uw databases gekopieerd naar een ra-grs-standaard Blob-opslagaccount (read-access geo-redundantstorage). Deze opslag wordt gebruikt door wekelijkse volledige back-ups, dagelijkse differentiële back-ups en transactielogboekback-ups, die elke 5 minuten worden gekopieerd. De grootte van de transactielogboeken is afhankelijk van de wijzigingssnelheid van de database. Een minimale opslag hoeveelheid gelijk aan 100 procent van de database grootte wordt verstrekt zonder extra kosten. Extra verbruik van back-upopslag wordt in GB per maand in rekening gebracht.

Zie de [prijspagina](https://azure.microsoft.com/pricing/details/sql-database/single/) voor meer informatie over opslagprijzen.

## <a name="vcore-based-purchasing-model"></a>Aankoopmodel op basis van vCore

Een virtuele kern (vCore) vertegenwoordigt een logische CPU en biedt u de mogelijkheid om te kiezen tussen generaties hardware en de fysieke kenmerken van de hardware (bijvoorbeeld het aantal cores, het geheugen en de opslaggrootte). Het op vCore gebaseerde inkoopmodel biedt u flexibiliteit, controle, transparantie van het individuele resourceverbruik en een eenvoudige manier om on-premises workloadvereisten naar de cloud te vertalen. Met dit model u reken-, geheugen- en opslagbronnen kiezen op basis van uw werkbelastingbehoeften.

In het op vCore gebaseerde inkoopmodel u kiezen tussen de servicelagen [Algemeen doel](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) en [Bedrijfskritieke](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) voor [afzonderlijke databases,](sql-database-single-database-scale.md) [elastische pools](sql-database-elastic-pool.md)en [beheerde exemplaren](sql-database-managed-instance.md). Voor afzonderlijke databases u ook de [hyperscale-servicelaag](sql-database-service-tier-hyperscale.md)kiezen.

Met het op vCore gebaseerde inkoopmodel u onafhankelijk berekenenen en opslagbronnen kiezen, on-premises prestaties afstemmen en de prijs optimaliseren. In het vCore-gebaseerde inkoopmodel betaalt u voor:

- Compute resources (de servicelaag + het aantal vCores en de hoeveelheid geheugen + het genereren van hardware).
- Het type en de hoeveelheid gegevens en logboekopslag.
- Back-upopslag (RA-GRS).

> [!IMPORTANT]
> Rekenbronnen, I/O en gegevens- en logboekopslag worden in rekening gebracht per database of elastische groep. Per database wordt back-upopslag in rekening gebracht. Zie [beheerde exemplaren](sql-database-managed-instance.md)voor meer informatie over beheerde instantiekosten .
> **Regiobeperkingen:** Zie producten die beschikbaar zijn [per regio voor](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)de huidige lijst met ondersteunde regio's. Als u een beheerde instantie wilt maken in een regio die momenteel niet wordt ondersteund, [verzendt u een ondersteuningsaanvraag via de Azure-portal.](quota-increase-request.md)

Als uw enkele database of elastische groep meer dan 300 DTU's verbruikt, kan het converteren naar het vCore-gebaseerde inkoopmodel uw kosten verlagen. U converteren met behulp van uw API naar keuze of met behulp van de Azure-portal, zonder downtime. Conversie is echter niet vereist en wordt niet automatisch uitgevoerd. Als het Op DTU gebaseerde inkoopmodel voldoet aan uw prestatie- en bedrijfsvereisten, moet u het blijven gebruiken.

Als u wilt converteren van het op DTU gebaseerde inkoopmodel naar het op vCore gebaseerde inkoopmodel, selecteert u de rekengrootte met behulp van de volgende vuistregels:

- Elke 100 DTU's in de standaardlaag vereisen ten minste 1 vCore in de servicelaag voor algemeen gebruik.
- Elke 125 DTU's in de premiumlaag vereisen ten minste 1 vCore in de business critical servicelaag.

> [!NOTE]
> De richtlijnen voor de dtu-vCore-dimensionering zijn bij benadering en worden verstrekt om te helpen bij de eerste schatting van de doelstelling van de doeldatabaseservice. De optimale configuratie van de doeldatabase is workload-afhankelijk. 
> 
> Om de optimale prijs-prestatieverhouding te bereiken, kan het nodig zijn gebruik te maken van de flexibiliteit van het vCore-model om het aantal vCores, de [hardwaregeneratie,](sql-database-service-tiers-vcore.md#hardware-generations)de [service-](sql-database-service-tiers-vcore.md#service-tiers) en [rekenniveaus](sql-database-service-tiers-vcore.md#compute-tiers) aan te passen, evenals het afstemmen van andere databaseconfiguratieparameters, zoals [maximale mate van parallellisme.](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing)

## <a name="dtu-based-purchasing-model"></a>DTU-gebaseerd inkoopmodel

Een database transactie-eenheid (DTU) vertegenwoordigt een gemengde maat van CPU, geheugen, leest en schrijft. Het Op DTU gebaseerde inkoopmodel biedt een reeks vooraf geconfigureerde bundels rekenbronnen en inclusief opslag om verschillende niveaus van toepassingsprestaties te stimuleren. Als u elke maand de voorkeur geeft aan de eenvoud van een vooraf geconfigureerde bundel en vaste betalingen, is het DTU-model mogelijk beter geschikt voor uw behoeften.

In het op DTU gebaseerde inkoopmodel u kiezen tussen de basis-, standaard- en premiumservicelagen voor zowel [afzonderlijke databases](sql-database-single-database-scale.md) als [elastische pools.](sql-database-elastic-pool.md) Het op DTU gebaseerde inkoopmodel is niet beschikbaar voor [beheerde exemplaren.](sql-database-managed-instance.md)

### <a name="database-transaction-units-dtus"></a>Databasetransactie-eenheden (DTU's)

Voor één database op een specifieke rekengrootte binnen een [servicelaag](sql-database-single-database-scale.md)garandeert Microsoft een bepaald niveau van resources voor die database (onafhankelijk van elke andere database in de Azure-cloud). Deze garantie zorgt voor een voorspelbaar prestatieniveau. De hoeveelheid resources die voor een database is toegewezen, wordt berekend als een aantal DTU's en is een gebundelde meting van compute-, opslag- en I/O-resources.

De verhouding tussen deze resources wordt oorspronkelijk bepaald door een [oltp-benchmarkworkload (Online Transaction Processing)](sql-database-benchmark-overview.md) die is ontworpen om typisch te zijn voor real-world OLTP-workloads. Wanneer uw werklast de hoeveelheid van een van deze resources overschrijdt, wordt de doorvoer beperkt, wat resulteert in tragere prestaties en time-outs.

De resources die door uw werkbelasting worden gebruikt, hebben geen invloed op de resources die beschikbaar zijn voor andere SQL-databases in de Azure-cloud. Ook de resources die door andere workloads worden gebruikt, hebben geen invloed op de resources die beschikbaar zijn voor uw SQL-database.

![Selectiekader](./media/sql-database-what-is-a-dtu/bounding-box.png)

DT's zijn het handigst voor het begrijpen van de relatieve resources die zijn toegewezen voor Azure SQL-databases op verschillende rekengroottes en servicelagen. Bijvoorbeeld:

- Een verdubbeling van de DTU's door de rekengrootte van een database te vergroten, komt overeen met een verdubbeling van de set resources die beschikbaar zijn voor die database.
- Een premium service tier P11 database met 1750 DTU's biedt 350x meer DTU rekenkracht dan een basisservice tier database met 5 DTU's.  

Als u meer inzicht wilt krijgen in het resourceverbruik van uw werkbelasting (DTU), gebruikt u inzichten in [queryprestaties](sql-database-query-performance.md) om:

- Identificeer de belangrijkste query's op CPU/duur/uitvoering telling die mogelijk kunnen worden afgestemd op verbeterde prestaties. Een I/O-intensieve query kan bijvoorbeeld profiteren van [optimalisatietechnieken in het geheugen](sql-database-in-memory.md) om beter gebruik te maken van het beschikbare geheugen op een bepaalde servicelaag en rekengrootte.
- Inzoomen op de details van een query om de tekst en de geschiedenis van het gebruik van resources weer te geven.
- Toegang tot prestatieafstemmingsaanbevelingen die acties van [SQL Database Advisor](sql-database-advisor.md)weergeven.

### <a name="elastic-database-transaction-units-edtus"></a>Elastische databasetransactie-eenheden (eDTU's)

Voor SQL-databases die altijd beschikbaar zijn, in plaats van een speciale set resources (DTU's) te bieden die mogelijk niet altijd nodig zijn, u deze databases in een [elastische groep plaatsen.](sql-database-elastic-pool.md) De databases in een elastische groep bevinden zich op één Azure SQL Database-server en delen een groep resources.

De gedeelde resources in een elastische groep worden gemeten door elastische databasetransactie-eenheden (eDTU's). Elastische pools bieden een eenvoudige, kosteneffectieve oplossing voor het beheren van prestatiedoelen voor meerdere databases met sterk uiteenlopende en onvoorspelbare gebruikspatronen. Een elastische groep garandeert dat niet alle resources door één database in de groep kunnen worden verbruikt, terwijl elke database in de groep altijd een minimum aan benodigde resources beschikbaar heeft.

Een pool krijgt een vast aantal eDTU's voor een vaste prijs. In de elastische groep kunnen afzonderlijke databases automatisch schalen binnen de geconfigureerde grenzen. Een database onder een zwaardere belasting verbruikt meer eDTU's om aan de vraag te voldoen. Databases onder lichtere belastingen verbruiken minder eDTU's. Databases zonder belasting verbruiken geen eDTU's. Omdat resources zijn ingericht voor de hele groep, in plaats van per database, vereenvoudigen elastische pools uw beheertaken en bieden u een voorspelbaar budget voor de groep.

U extra eDTU's toevoegen aan een bestaande groep zonder databasedowntime en zonder gevolgen voor de databases in de groep. Als u geen extra eDTU's meer nodig hebt, verwijdert u deze op elk gewenst moment uit een bestaande groep. U ook op elk gewenst moment databases toevoegen aan of aftrekken uit een groep. Als u eDTU's wilt reserveren voor andere databases, beperkt u het aantal eDTU's dat een database onder een zware belasting kan gebruiken. Als een database consistent resources ondergebruikt, verplaatst u deze uit de groep en configureert u deze als één database met een voorspelbare hoeveelheid vereiste resources.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Het aantal DTO's bepalen dat nodig is voor een werkbelasting

Als u een bestaande on-premises of SQL Server virtual machine workload wilt migreren naar Azure SQL Database, gebruikt u de [DTU-calculator](https://dtucalculator.azurewebsites.net/) om het aantal benodigde DTU's te benaderen. Voor een bestaande Azure SQL [Database-workload](sql-database-query-performance.md) gebruikt u inzichten in queryprestaties om inzicht te krijgen in uw databaseresourceverbruik (DTU's) en diepere inzichten te verkrijgen voor het optimaliseren van uw werkbelasting. Met [de sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dynamic management view (DMV) u het resourceverbruik van het laatste uur bekijken. De [catalogusweergave sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) geeft het resourceverbruik van de afgelopen 14 dagen weer, maar met een lagere getrouwheid van gemiddelden van vijf minuten.

### <a name="determine-dtu-utilization"></a>DTU-gebruik bepalen

Als u het gemiddelde percentage DTU/eDTU-gebruik wilt bepalen ten opzichte van de DTU/eDTU-limiet van een database of een elastische groep, gebruikt u de volgende formule:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

De invoerwaarden voor deze formule zijn verkrijgbaar bij [sys.dm_db_resource_stats,](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)en [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMVs. Met andere woorden, als u het percentage DTU/eDTU-gebruik wilt bepalen in de richting van de DTU/eDTU-limiet van een database of een elastische groep, kiest u de grootste procentuele waarde uit het volgende: `avg_cpu_percent`, `avg_data_io_percent`en `avg_log_write_percent` op een bepaald tijdstip.

> [!NOTE]
> De DTU-limiet van een database wordt bepaald door CPU, leest, schrijft en geheugen beschikbaar is voor de database. Omdat de SQL Server-databaseengine echter doorgaans al het beschikbare geheugen `avg_memory_usage_percent` voor de gegevenscache gebruikt om de prestaties te verbeteren, is de waarde meestal bijna 100% ongeacht de huidige databasebelasting. Daarom, hoewel het geheugen indirect invloed heeft op de DTU-limiet, wordt het niet gebruikt in de DTU-gebruiksformule.
>

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Workloads die profiteren van een elastische pool van resources

Pools zijn zeer geschikt voor databases met een laag resourcegebruikgemiddelde en relatief zelden gebruikspieken. Zie [Wanneer moet u een sql-database-elastische pool overwegen voor](sql-database-elastic-pool.md)meer informatie?

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>Hardwaregeneraties in het DTU-gebaseerde inkoopmodel

In het op DTU gebaseerde inkoopmodel kunnen klanten niet kiezen voor de hardwaregeneratie die voor hun databases wordt gebruikt. Hoewel een bepaalde database meestal lang op een specifieke hardwaregeneratie blijft staan (vaak meerdere maanden), zijn er bepaalde gebeurtenissen die ervoor kunnen zorgen dat een database wordt verplaatst naar een andere hardwaregeneratie.

Een database kan bijvoorbeeld worden verplaatst naar een andere hardwaregeneratie als deze wordt opgeschaald of omlaag wordt geschaald naar een andere servicedoelstelling, of als de huidige infrastructuur in een datacenter de capaciteitslimieten nadert, of als de momenteel gebruikte hardware wordt buiten bedrijf gesteld vanwege het einde van het leven.

Als een database wordt verplaatst naar verschillende hardware, kunnen de prestaties van de werkbelasting veranderen. Het DTU-model garandeert dat de doorvoer- en responstijd van de [DTU-benchmarkworkload](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-dtu#dtu-benchmark) nagenoeg identiek zal blijven als de database overgaat op een andere hardwaregeneratie, zolang de servicedoelstelling (het aantal DTU's) gelijk blijft. 

Over het brede spectrum van klantworkloads die in Azure SQL Database worden uitgevoerd, kan de impact van het gebruik van verschillende hardware voor dezelfde servicedoelstelling echter groter zijn. Verschillende workloads profiteren van verschillende hardwareconfiguratie en functies. Daarom is het voor andere workloads dan de DTU-benchmark mogelijk om prestatieverschillen te zien als de database van de ene hardwaregeneratie naar de andere gaat.

Een toepassing die gevoelig is voor netwerklatentie kan bijvoorbeeld betere prestaties zien op Gen5-hardware vs. Gen4 als gevolg van het gebruik van Accelerated Networking in Gen5, maar een toepassing met intensief leesIO kan betere prestaties zien op Gen4-hardware vs. Gen5 als gevolg van hogere geheugen-per-core ratio op Gen4.

Klanten met workloads die gevoelig zijn voor hardwarewijzigingen of klanten die de keuze van de hardwaregeneratie voor hun database willen beheren, kunnen het [vCore-model](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore) gebruiken om hun favoriete hardwaregeneratie te kiezen tijdens het maken en schalen van de database. In het vCore-model worden resourcelimieten van elke servicedoelstelling voor elke hardwaregeneratie gedocumenteerd, voor zowel [afzonderlijke databases](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases) als [elastische pools.](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) Zie [Hardwaregeneraties](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore#hardware-generations)voor meer informatie over hardwaregeneraties in het vCore-model.

## <a name="frequently-asked-questions-faqs"></a>Veelgestelde vragen (veelgestelde vragen)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Moet ik mijn toepassing offline halen om te converteren van een Servicetier op Basis van DTU naar een vCore-gebaseerde servicelaag?

Nee. U hoeft de toepassing niet offline te halen. De nieuwe servicelagen bieden een eenvoudige onlineconversiemethode die vergelijkbaar is met het bestaande proces van het upgraden van databases van de standaard naar de premium servicelaag en andersom. U deze conversie starten met de Azure-portal, PowerShell, Azure CLI, T-SQL of de REST API. Zie [Afzonderlijke databases beheren](sql-database-single-database-scale.md) en [Elastische pools beheren](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Kan ik een database converteren van een servicelaag in het vCore-gebaseerde inkoopmodel naar een servicelaag in het op DTU gebaseerde inkoopmodel?

Ja, u uw database eenvoudig converteren naar een ondersteunde prestatiedoelstelling met behulp van de Azure-portal, PowerShell, de Azure CLI, T-SQL of de REST API. Zie [Afzonderlijke databases beheren](sql-database-single-database-scale.md) en [Elastische pools beheren](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het op vCore gebaseerde inkoopmodel [het op vCore gebaseerde inkoopmodel.](sql-database-service-tiers-vcore.md)
- Zie [DTU-gebaseerd inkoopmodel](sql-database-service-tiers-dtu.md)voor meer informatie over het op DTU gebaseerde inkoopmodel.
