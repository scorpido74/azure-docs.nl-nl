---
title: Bedrijfskritiek servicelaag
description: Meer informatie over de Azure SQL Database Bedrijfskritiek-laag
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: fc328c34c1543a75fdc885087d44b28e24c0850a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380091"
---
# <a name="business-critical-tier---azure-sql-database"></a>Bedrijfskritiek laag-Azure SQL Database

> [!NOTE]
> Bedrijfskritiek laag wordt Premium genoemd in het DTU-aankoop model. Zie [Azure SQL database-inkoop modellen en-resources](sql-database-purchase-models.md)voor een vergelijking van het op vCore gebaseerde aankoop model met het DTU-gebaseerde aankoop model.

Azure SQL Database is gebaseerd op SQL Server data base engine-architectuur die is aangepast voor de cloud omgeving om 99,99% Beschik baarheid te garanderen, zelfs in het geval van infrastructuur fouten. Er zijn drie architectuur modellen die worden gebruikt in Azure SQL Database:
- Algemeen/standaard 
- Business Critical/Premium
- Hyperscale

Premium/Bedrijfskritiek service tier model is gebaseerd op een cluster met data base-engine processen. Dit architectuur model is afhankelijk van een feit dat er altijd een quorum van de beschik bare data base-engine knooppunten is en een minimale invloed heeft op de prestaties van uw werk belasting, zelfs tijdens onderhouds activiteiten.

Azure upgradet en patches onderliggend besturings systeem, stuur Programma's en SQL Server data base-engine transparant met de minimale verwerkings tijd voor eind gebruikers. 

Premium-Beschik baarheid is ingeschakeld in Premium-en Bedrijfskritiek-service lagen van Azure SQL Database en is ontworpen voor intensieve werk belastingen die geen invloed kunnen hebben op de prestaties vanwege de continue onderhouds werkzaamheden.

In het Premium-model integreert Azure SQL database Compute en opslag op het ene knoop punt. Hoge Beschik baarheid in dit architectuur model wordt bereikt door de replicatie van Compute (SQL Server data base engine process) en opslag (lokaal gekoppelde SSD) die is geïmplementeerd in een cluster met vier knoop punten en gebruikmaakt van technologie die vergelijkbaar is met SQL Server AlwaysOn- [beschikbaarheids groepen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Cluster van data base-engine knooppunten](media/sql-database-managed-instance/business-critical-service-tier.png)

Zowel het proces van de SQL database-engine als de onderliggende MDF/LDF-bestanden worden geplaatst op hetzelfde knoop punt met lokaal gekoppelde SSD-opslag met een lage latentie voor uw werk belasting. Hoge Beschik baarheid wordt geïmplementeerd met technologie die vergelijkbaar is met SQL Server AlwaysOn- [beschikbaarheids groepen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Elke Data Base is een cluster met database knooppunten met één primaire data base die toegankelijk is voor de werk belasting van de klant en een drie secundaire processen met kopieën van gegevens. Het primaire knoop punt pusht voortdurend de wijzigingen naar secundaire knoop punten om ervoor te zorgen dat de gegevens beschikbaar zijn op secundaire replica's als het primaire knoop punt om een of andere reden vastloopt. Failover wordt verwerkt door de SQL Server data base-engine – één secundaire replica wordt het primaire knoop punt en er wordt een nieuwe secundaire replica gemaakt om ervoor te zorgen dat er voldoende knoop punten in het cluster zijn. De werk belasting wordt automatisch omgeleid naar het nieuwe primaire knoop punt.

Daarnaast heeft Bedrijfskritiek cluster ingebouwde [Read-out](sql-database-read-scale-out.md) voor uitgebreide Lees bewerkingen, waarmee een alleen-lezen knoop punt met een vaste hoeveelheid kosten kan worden gebruikt om alleen-lezen query's uit te voeren (bijvoorbeeld rapporten) die de prestaties van uw primaire werk belasting niet mogen beïnvloeden.

## <a name="when-to-choose-this-service-tier"></a>Wanneer moet deze servicelaag worden gekozen?

Bedrijfskritiek servicelaag is ontworpen voor de toepassingen die antwoorden met een lage latentie vereisen van de onderliggende SSD-opslag (gemiddeld 1-2 MS), snel herstel als de onderliggende infra structuur mislukt of rapporten, analyses en alleen-lezen moet worden geladen query's naar de gratis, lees bare secundaire replica van de primaire data base.

De belangrijkste redenen waarom u Bedrijfskritiek servicelaag moet kiezen in plaats van Algemeen laag zijn:
-   Minimale IO-latentie vereisten: werk belasting waarvoor het snelle antwoord van de opslaglaag nodig is (1-2 milliseconden in gemiddeld) moet Bedrijfskritiek-laag gebruiken. 
-   Veelvuldige communicatie tussen toepassing en data base. Toepassing die geen gebruik kan maken van de caching van de toepassings laag of het [aanvragen van batches](sql-database-use-batching-to-improve-performance.md) en moet veel SQL-query's verzenden die snel moeten worden verwerkt zijn goede kandidaten voor bedrijfskritiek-laag.
-   Groot aantal updates: insert-, update-en delete-bewerkingen wijzigen de gegevens pagina's in het geheugen (Dirty pagina) die moeten worden opgeslagen in gegevens bestanden met `CHECKPOINT` bewerking. Mogelijk is het proces van een data base-engine vastgelopen of kan een failover van de data base met een groot aantal vuile pagina's de herstel tijd in Algemeen laag verg Roten. Gebruik Bedrijfskritiek laag als u een werk belasting hebt die veel in-Memory wijzigingen veroorzaakt. 
-   Langlopende trans acties waarmee gegevens worden gewijzigd. Trans acties die gedurende een langere periode worden geopend, verhinderen het afkappen van het logboek bestand dat de logboek grootte en het aantal [virtuele logboek bestanden (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch)kan verg Roten. Een groot aantal VLF kan het herstel van de data base na een failover vertragen.
-   Workload met rapportage-en analyse query's die kunnen worden omgeleid naar de gratis secundaire alleen-lezen replica.
- Hogere tolerantie en sneller herstel van de fouten. In het geval van een systeem fout wordt de Data Base op het primaire exemplaar uitgeschakeld en wordt een van de secundaire replica's onmiddellijk een nieuwe lezen-schrijven primaire data base gemaakt die gereed is voor het verwerken van de query's. Data base-engine hoeft geen trans acties uit het logboek bestand te analyseren en opnieuw in te dienen en alle gegevens in de geheugen buffer te laden.
- Geavanceerde beveiliging tegen beschadiging van gegevens-Bedrijfskritiek-laag maakt gebruik van database replica's achter-de schermen voor bedrijfs continuïteit. de service maakt dus ook gebruik van automatische pagina reparatie. Dit is dezelfde technologie die wordt gebruikt voor SQL Server database [spiegeling en beschikbaarheids groepen](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring). In het geval dat een replica een pagina kan lezen vanwege een probleem met de gegevens integriteit, wordt een nieuwe kopie van de pagina opgehaald uit een andere replica, waardoor de onleesbare pagina wordt vervangen zonder gegevens verlies of uitval tijd van de klant. Deze functionaliteit is van toepassing op Algemeen laag als de data base geo-secundaire replica heeft.
- Hogere Beschik baarheid-Bedrijfskritiek laag in configuratie met meerdere AZ-configuraties garandeert 99,995% Beschik baarheid, vergeleken met 99,99% van de Algemeen-laag.
- Snelle geo-Recovery-Bedrijfskritiek laag die is geconfigureerd met geo-replicatie, heeft een gegarandeerd herstel punt (RPO) van 5 sec en de beoogde herstel tijd (RTO) van 30 sec voor 100% van de geïmplementeerde uren.

## <a name="next-steps"></a>Volgende stappen

- Zoek bron kenmerken (aantal kernen, i/o, geheugen) van Bedrijfskritiek-laag in [beheerde instantie](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), afzonderlijke data base in [VCore model](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) of [DTU-model](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier), of elastische pool in [vCore model](sql-database-vcore-resource-limits-elastic-pools.md#business-critical---provisioned-compute---gen4) en [DTU-model](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Meer informatie over [Algemeen](sql-database-service-tier-general-purpose.md) -en [grootschalige](sql-database-service-tier-hyperscale.md) -lagen.
- Meer informatie over [service Fabric](../service-fabric/service-fabric-overview.md).
- Zie [bedrijfs continuïteit](sql-database-business-continuity.md)voor meer opties voor hoge Beschik baarheid en herstel na nood gevallen.
