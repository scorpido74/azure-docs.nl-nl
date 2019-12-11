---
title: Aankoopmodellen
description: Meer informatie over de inkoop modellen die beschikbaar zijn voor Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: 6dbe61c47a7323e2dec599d2f3c77453aa6f8d82
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973523"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Kiezen tussen de vCore en de DTU-aankoop modellen

Met Azure SQL Database kunt u eenvoudig een volledig beheerd platform as a service-data base-engine (PaaS) aanschaffen die aan uw prestatie-en kosten behoeften voldoet. Afhankelijk van het implementatie model dat u hebt gekozen voor Azure SQL Database, kunt u het inkoop model selecteren dat voor u geschikt is:

- [Op virtuele kern (vCore) gebaseerd inkoop model](sql-database-service-tiers-vcore.md) (aanbevolen). Dit aankoop model biedt een keuze tussen een ingerichte Compute-laag en een serverloze Compute-laag. Met de ingerichte Compute-laag kiest u de exacte hoeveelheid reken resources die altijd worden ingericht voor uw werk belasting. Met de compute-laag zonder server geeft u het automatisch schalen van de reken resources over een configureerbaar reken bereik op. Met deze Compute-laag kunt u de data base ook automatisch onderbreken en hervatten op basis van de activiteit van de werk belasting. De vCore eenheids prijs per tijds eenheid is lager in de ingerichte Compute-laag dan in de serverloze Compute-laag.
- [Op DTU (data base Trans Action Unit) gebaseerd aankoop model](sql-database-service-tiers-dtu.md). Dit aankoop model bevat gebundelde reken-en opslag pakketten die zijn afgebalanceerd voor algemene werk belastingen.

Er zijn verschillende aankoop modellen beschikbaar voor verschillende Azure SQL Database-implementatie modellen:

- De implementatie opties voor [één data base](sql-database-single-databases-manage.md) en [elastische pool](sql-database-elastic-pool.md) in [Azure SQL database](sql-database-technical-overview.md) bieden het [op DTU gebaseerde aankoop model](sql-database-service-tiers-dtu.md) en het [op vCore gebaseerde aankoop model](sql-database-service-tiers-vcore.md).
- De implementatie optie [Managed instance](sql-database-managed-instance.md) in Azure SQL database biedt alleen het [op vCore gebaseerde aankoop model](sql-database-service-tiers-vcore.md).
- De [grootschalige](sql-database-service-tier-hyperscale.md) is beschikbaar voor afzonderlijke data bases die gebruikmaken van het [op vCore gebaseerde aankoop model](sql-database-service-tiers-vcore.md).

In de volgende tabel en grafiek worden de vCore en de op DTU gebaseerde inkoop modellen vergeleken en tegengesteld:

|**Aankoop model**|**Beschrijving**|**Geschikt voor**|
|---|---|---|
|Model op basis van DTU|Dit model is gebaseerd op een gebundelde meting van compute-, opslag-en I/O-resources. Reken grootten worden uitgedrukt in Dtu's voor afzonderlijke data bases en in Elastic data base Trans Action units (Edtu's) voor elastische Pools. Zie [Wat zijn dtu's en edtu's?](sql-database-purchase-models.md#dtu-based-purchasing-model)voor meer informatie over Dtu's en edtu's.|Het beste voor klanten die eenvoudige, vooraf geconfigureerde resource opties willen.|
|Model op basis van vCore|Met dit model kunt u afzonderlijke reken-en opslag Resources kiezen. Met het op vCore gebaseerde aankoop model kunt u ook Azure Hybrid Benefit gebruiken om [SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) kosten besparingen te verkrijgen.|Het beste voor klanten die flexibiliteit, controle en transparantie hebben.|
||||  

![prijs model vergelijking](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Reken kosten

### <a name="provisioned-compute-costs"></a>Ingerichte reken kosten

In de ingerichte Compute-laag weerspiegelt de reken kosten de totale reken capaciteit die is ingericht voor de toepassing.

In de laag bedrijfskritische service worden automatisch ten minste drie replica's toegewezen. Om deze extra toewijzing van reken bronnen weer te geven, is de prijs in het op vCore gebaseerde aankoop model ongeveer 2,7 x hoger in de bedrijfskritische service tier dan in de servicelaag voor algemeen gebruik. De hogere opslag prijs per GB in de bedrijfskritische servicelaag weerspiegelt ook de hoge I/O-en lage latentie van de SSD-opslag.

De kosten voor back-upopslag zijn hetzelfde voor de tier Business Critical service en de servicelaag voor algemeen gebruik, omdat beide lagen standaard opslag gebruiken.

### <a name="serverless-compute-costs"></a>Reken kosten zonder server

Zie [SQL database serverloos](sql-database-serverless.md)voor een beschrijving van de manier waarop reken capaciteit is gedefinieerd en de kosten worden berekend voor de serverloze Compute-laag.

## <a name="storage-costs"></a>Opslagkosten

Verschillende soorten opslag worden anders gefactureerd. Voor gegevens opslag worden er kosten in rekening gebracht voor de ingerichte opslag op basis van de maximale grootte van de data base of groep die u selecteert. De kosten worden niet gewijzigd tenzij u het maximum vermindert of als u dit verhoogt. Back-upopslag is gekoppeld aan automatische back-ups van uw exemplaar en wordt dynamisch toegewezen. Als u uw back-upperiode verhoogt, wordt de back-upopslag die door uw exemplaar wordt gebruikt, verhoogd.

Standaard worden zeven dagen automatische back-ups van uw data bases gekopieerd naar een standaard Blob Storage-account met geografisch redundante opslag met lees toegang (RA-GRS). Deze opslag wordt gebruikt door wekelijkse volledige back-ups, dagelijkse differentiële back-ups en back-ups van transactie logboeken, die elke vijf minuten worden gekopieerd. De grootte van de transactie Logboeken is afhankelijk van de frequentie waarmee de data base wordt gewijzigd. Er wordt geen extra kosten in rekening gebracht voor een minimale opslag hoeveelheid die gelijk is aan 100 procent van de grootte van de data base. Extra verbruik van back-upopslag wordt in GB per maand in rekening gebracht.

Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/sql-database/single/) voor meer informatie over opslag prijzen.

## <a name="vcore-based-purchasing-model"></a>Aankoopmodel op basis van vCore

Een virtuele kern (vCore) vertegenwoordigt een logische CPU en biedt u de mogelijkheid om te kiezen tussen generaties van hardware en fysieke kenmerken van de hardware (bijvoorbeeld het aantal kernen, het geheugen en de opslag grootte). Het vCore-inkoop model biedt u flexibiliteit, controle, transparantie van het gebruik van afzonderlijke bronnen en een eenvoudige manier om on-premises werkbelasting vereisten te vertalen naar de Cloud. Met dit model kunt u reken-, geheugen-en opslag Resources kiezen op basis van de behoeften van uw werk belasting.

In het op vCore gebaseerde aankoop model kunt u kiezen tussen de [algemene doel](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) -en [bedrijfskritische](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) service lagen voor [individuele data bases](sql-database-single-database-scale.md), [elastische Pools](sql-database-elastic-pool.md)en [beheerde exemplaren](sql-database-managed-instance.md). Voor afzonderlijke data bases kunt u ook de [grootschalige-servicelaag](sql-database-service-tier-hyperscale.md)kiezen.

Met het op vCore gebaseerde aankoop model kunt u afzonderlijke reken-en opslag Resources kiezen, de on-premises prestaties afstemmen en de prijs optimaliseren. In het op vCore gebaseerde aankoop model betaalt u voor:

- Reken bronnen (de servicelaag + het aantal vCores en de hoeveelheid geheugen + de generatie van de hardware).
- Het type en de hoeveelheid gegevens-en logboek opslag.
- Back-upopslag (RA-GRS).

> [!IMPORTANT]
> Reken resources, I/O en gegevens-en logboek opslag worden in rekening gebracht per data base of elastische pool. Back-upopslag wordt per data base in rekening gebracht. Zie [Managed](sql-database-managed-instance.md)instances (Engelstalig) voor meer informatie over beheerde exemplaar kosten.
> **Beperkingen van de regio:** Zie voor de huidige lijst met ondersteunde regio's [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Als u een beheerd exemplaar wilt maken in een regio die momenteel niet wordt ondersteund, [verzendt u een ondersteunings aanvraag via de Azure Portal](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

Als uw afzonderlijke data base of elastische pool meer dan 300 Dtu's verbruikt, kan het converteren naar het op vCore gebaseerde aankoop model uw kosten verlagen. U kunt converteren met behulp van de API van Choice of met behulp van de Azure Portal, zonder uitval tijd. Conversie is echter niet vereist en wordt niet automatisch uitgevoerd. Als het op DTU gebaseerde aankoop model voldoet aan uw prestatie-en bedrijfs vereisten, moet u het blijven gebruiken.

Als u wilt omzetten van het op DTU gebaseerde aankoop model naar het op vCore gebaseerde aankoop model, selecteert u de reken grootte met behulp van de volgende vuist regels:

- Voor elke 100 Dtu's in de laag standaard is ten minste 1 vCore vereist in de servicelaag voor algemeen gebruik.
- Voor elke 125 Dtu's in de Premium-laag is ten minste 1 vCore vereist in de bedrijfskritische service-laag.

## <a name="dtu-based-purchasing-model"></a>Op DTU gebaseerd inkoop model

Een Data Base Trans Action Unit (DTU) vertegenwoordigt een overvloei meting van CPU, geheugen, lees bewerkingen en schrijf bewerkingen. Het op DTU gebaseerde aankoop model biedt een aantal vooraf geconfigureerde bundels van reken bronnen en opgenomen opslag om verschillende niveaus van toepassings prestaties te kunnen best Ellen. Als u de voor keur geeft aan de eenvoud van een vooraf geconfigureerde bundel en vaste betalingen per maand, is het op DTU gebaseerde model mogelijk beter geschikt voor uw behoeften.

In het op DTU gebaseerde aankoop model kunt u kiezen tussen de basis-, standaard-en Premium-Service lagen voor zowel de [afzonderlijke data bases](sql-database-single-database-scale.md) als [elastische Pools](sql-database-elastic-pool.md). Het op DTU gebaseerde aankoop model is niet beschikbaar voor [beheerde exemplaren](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Data base Trans Action units (Dtu's)

Voor één data base met een specifieke reken grootte binnen een [servicelaag, garandeert](sql-database-single-database-scale.md)micro soft een bepaald niveau van resources voor die data base (onafhankelijk van andere data bases in de Azure-Cloud). Deze garantie biedt een voorspelbaar prestatie niveau. De hoeveelheid resources die voor een Data Base wordt toegewezen, wordt berekend als een aantal Dtu's en is een gebundelde meting van compute, Storage en I/O-resources.

De verhouding tussen deze resources wordt oorspronkelijk bepaald door een [OLTP-werk belasting (online Trans Action processing)](sql-database-benchmark-overview.md) die is ontworpen om typische fysieke OLTP-workloads te zijn. Wanneer uw werk belasting de hoeveelheid van een van deze resources overschrijdt, wordt uw door Voer beperkt, wat resulteert in tragere prestaties en time-outs.

De resources die door uw werk belasting worden gebruikt, hebben geen invloed op de resources die beschikbaar zijn voor andere SQL-data bases in de Azure-Cloud. De resources die worden gebruikt door andere workloads, hebben ook geen invloed op de resources die beschikbaar zijn voor uw SQL database.

![selectie kader](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu's zijn het handigst om te weten wat de relatieve resources zijn die zijn toegewezen voor Azure SQL-data bases tegen verschillende reken grootten en service lagen. Bijvoorbeeld:

- Door de Dtu's te verdubbelen door de reken grootte van een Data Base te verg Roten, wordt de set beschik bare bronnen voor die data base verdubbeld.
- Een Premium service tier P11-data base met 1750 Dtu's biedt 350x meer DTU-reken kracht dan een eenvoudige data base van de servicelaag met 5 Dtu's.  

Als u meer inzicht wilt krijgen in het gebruik van uw workload door de resource (DTU), gebruikt u [query-prestatie inzichten](sql-database-query-performance.md) om:

- Identificeer de meest voorkomende query's op basis van het aantal CPU/duur/uitvoeringen dat mogelijk kan worden afgestemd op betere prestaties. Een I/O-intensieve query kan bijvoorbeeld profiteren van [optimalisatie technieken in het geheugen](sql-database-in-memory.md) om beter gebruik te maken van het beschik bare geheugen in een bepaalde servicelaag en de berekenings grootte.
- Zoom in op de details van een query om de tekst en de geschiedenis van het resource gebruik weer te geven.
- Aanbevelingen voor het afstemmen van prestaties voor het weer geven van acties die door [SQL database Advisor](sql-database-advisor.md)worden uitgevoerd.

### <a name="elastic-database-transaction-units-edtus"></a>Elastic data base Trans Action units (Edtu's)

Voor SQL-data bases die altijd beschikbaar zijn, in plaats van een speciale set resources (Dtu's) te bieden die niet altijd nodig zijn, kunt u deze data bases in een [elastische pool](sql-database-elastic-pool.md)plaatsen. De data bases in een elastische pool bevinden zich op één Azure SQL Database Server en delen een groep resources.

De gedeelde resources in een elastische pool worden gemeten door Elastic data base Trans Action units (Edtu's). Elastische Pools bieden een eenvoudige en kosteneffectieve oplossing voor het beheer van de prestatie doelen voor meerdere data bases met veel verschillende en onvoorspelbare gebruiks patronen. Een elastische groep garandeert dat alle resources niet kunnen worden gebruikt door één data base in de pool, terwijl ervoor wordt gezorgd dat elke data base in de groep altijd een minimale hoeveelheid beschik bare bronnen heeft.

Een pool krijgt een ingesteld aantal Edtu's voor een ingestelde prijs. In de elastische pool kunnen afzonderlijke data bases automatisch worden geschaald binnen de geconfigureerde grenzen. Een Data Base onder een zwaarere belasting neemt meer Edtu's in beslag om aan de vraag te voldoen. Data bases met een lichtere belasting nemen minder Edtu's in beslag. Data bases zonder belasting nemen geen Edtu's in beslag. Omdat resources zijn ingericht voor de hele pool in plaats van per data base, vereenvoudigen elastische Pools uw beheer taken en bieden ze een voorspelbaar budget voor de pool.

U kunt extra Edtu's toevoegen aan een bestaande pool zonder uitval tijd van de data base en zonder dat dit van invloed is op de data bases in de pool. Als u extra Edtu's niet meer nodig hebt, kunt u deze op elk gewenst moment verwijderen uit een bestaande groep. U kunt ook data bases toevoegen aan of verwijderen uit een pool op elk gewenst moment. Als u Edtu's voor andere data bases wilt reserveren, beperkt u het aantal Edtu's dat door een Data Base kan worden gebruikt onder een zware belasting. Als een Data Base consequent bronnen gebruikt, verplaatst u deze uit de pool en configureert u deze als één data base met een voorspel bare hoeveelheid vereiste resources.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Het aantal Dtu's bepalen dat nodig is voor een workload

Als u een bestaande on-premises of SQL Server werk belasting van virtuele machine naar Azure SQL Database wilt migreren, gebruikt u de [DTU-reken machine](https://dtucalculator.azurewebsites.net/) om het aantal benodigde dtu's te benaderen. Gebruik voor een bestaande Azure SQL Database workload [query-prestatie inzichten](sql-database-query-performance.md) om inzicht te krijgen in uw database verbruik (dtu's) en krijg meer inzicht in het optimaliseren van uw werk belasting. Met de weer gave [sys. dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) dynamische beheer weergave (DMV) kunt u het Resource verbruik voor het afgelopen uur weer geven. In de catalogus weergave [sys. resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) wordt het Resource verbruik voor de afgelopen 14 dagen weer gegeven, maar een lagere betrouw baarheid van gemiddelden van vijf minuten.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Workloads die profiteren van een elastische groep resources

Pools zijn zeer geschikt voor data bases met een gemiddelde en relatief onregelmatige piek gebruik. Zie [Wanneer moet u rekening houden met een SQL database elastische pool?](sql-database-elastic-pool.md)voor meer informatie.

## <a name="frequently-asked-questions-faqs"></a>Veelgestelde vragen (FAQ)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Moet ik mijn toepassing offline zetten om een service tier op basis van DTU te converteren naar een vCore-service tier?

Nee. U hoeft de toepassing niet offline te zetten. De nieuwe service lagen bieden een eenvoudige online conversie methode die vergelijkbaar is met het bestaande proces van het bijwerken van data bases van de standaard naar de Premium-servicelaag en de andere manier. U kunt deze conversie starten met behulp van de Azure Portal, Power shell, de Azure CLI, T-SQL of de REST API. Zie voor het [beheren van afzonderlijke data bases](sql-database-single-database-scale.md) en het [beheren van elastische Pools](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Kan ik een Data Base van een servicelaag in het op vCore gebaseerde aankoop model converteren naar een servicelaag in het op DTU gebaseerde aankoop model?

Ja, u kunt uw data base eenvoudig converteren naar een ondersteunde prestatie doelstelling door gebruik te maken van de Azure Portal, Power shell, de Azure CLI, T-SQL of de REST API. Zie voor het [beheren van afzonderlijke data bases](sql-database-single-database-scale.md) en het [beheren van elastische Pools](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Volgende stappen

- Zie [op vCore gebaseerd inkoop model](sql-database-service-tiers-vcore.md)voor meer informatie over het op vCore gebaseerde aankoop model.
- Zie het [op DTU gebaseerde aankoop model](sql-database-service-tiers-dtu.md)voor meer informatie over het op DTU gebaseerde aankoop model.
