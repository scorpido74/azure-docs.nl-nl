---
title: Bedrijfskritieke servicelaag
description: Meer informatie over de azure SQL Database Business Critical-laag
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268676"
---
# <a name="business-critical-tier---azure-sql-database"></a>Business Critical-laag - Azure SQL-database

> [!NOTE]
> Business Critical tier heet Premium in DTU inkoopmodel. Zie [Azure SQL Database-inkoopmodellen en -bronnen](sql-database-purchase-models.md)voor een vergelijking van het op vCore gebaseerde inkoopmodel met het op DTU gebaseerde inkoopmodel.

Azure SQL Database is gebaseerd op SQL Server Database Engine-architectuur die is aangepast voor de cloudomgeving om 99,99% beschikbaarheid te garanderen, zelfs in het geval van infrastructuurfouten. Er zijn drie architectuurmodellen die worden gebruikt in Azure SQL Database:
- Algemeen doel/standaard 
- Bedrijfskritisch/Premium
- Hyperscale

Het Premium/Business Critical service tier-model is gebaseerd op een cluster van databaseengineprocessen. Dit architecturale model is gebaseerd op het feit dat er altijd een quorum van beschikbare database engine nodes en heeft minimale impact op de prestaties van uw werklast, zelfs tijdens onderhoudsactiviteiten.

Azure-upgrades en patches onderliggend besturingssysteem, stuurprogramma's en SQL Server Database Engine transparant met de minimale down-time voor eindgebruikers. 

Premium beschikbaarheid is ingeschakeld in Premium en Business Critical service lagen van Azure SQL Database en het is ontworpen voor intensieve workloads die geen impact op de prestaties kunnen verdragen als gevolg van de lopende onderhoudsbewerkingen.

In het premiummodel integreert Azure SQL-database rekenkracht en opslag op het ene knooppunt. Hoge beschikbaarheid in dit architecturale model wordt bereikt door replicatie van compute (SQL Server Database Engine-proces) en opslag (lokaal gekoppelde SSD) geïmplementeerd in vier node cluster, met behulp van technologie vergelijkbaar met SQL Server [Always On Availability Groepen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Cluster van database-engineknooppunten](media/sql-database-managed-instance/business-critical-service-tier.png)

Zowel het SQL-databaseengineproces als de onderliggende mdf/ldf-bestanden worden op hetzelfde knooppunt geplaatst met lokaal gekoppelde SSD-opslag die een lage latentie biedt aan uw werkbelasting. Hoge beschikbaarheid wordt geïmplementeerd met behulp van technologie die vergelijkbaar is met SQL Server [Always On Availability Groups.](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server) Elke database is een cluster van databaseknooppunten met één primaire database die toegankelijk is voor de werkbelasting van klanten en een drie secundaire processen met kopieën van gegevens. Het primaire knooppunt duwt voortdurend de wijzigingen in secundaire knooppunten om ervoor te zorgen dat de gegevens beschikbaar zijn op secundaire replica's als het primaire knooppunt om welke reden dan ook vastloopt. Failover wordt afgehandeld door de SQL Server Database Engine : één secundaire replica wordt het primaire knooppunt en er wordt een nieuwe secundaire replica gemaakt om voldoende knooppunten in het cluster te garanderen. De werkbelasting wordt automatisch doorgestuurd naar het nieuwe primaire knooppunt.

Bovendien heeft het Business Critical-cluster een ingebouwde [Leesschaal-out-mogelijkheid](sql-database-read-scale-out.md) die gratis ingebouwde lees-only-node biedt die kan worden gebruikt om alleen-lezen query's uit te voeren (bijvoorbeeld rapporten) die geen invloed mogen hebben op de prestaties van uw primaire werkbelasting.

## <a name="when-to-choose-this-service-tier"></a>Wanneer kies je deze servicelaag?

Business Critical-servicelaag is ontworpen voor de toepassingen waarvoor antwoorden met lage latentie van de onderliggende SSD-opslag nodig zijn (gemiddeld 1-2 ms), snel herstel als de onderliggende infrastructuur uitvalt of rapporten, analyses en alleen-lezen moeten worden verwijderd query's naar de gratis leesbare secundaire replica van de primaire database.

De belangrijkste redenen waarom u de servicelaag Voor Bedrijfskritieke service moet kiezen in plaats van de laag Algemeen Doel, zijn:
-   Vereisten voor lage IO-latentie – workload die de snelle respons van de opslaglaag (gemiddeld 1-2 milliseconden) nodig heeft, moet de business critical-laag gebruiken. 
-   Frequente communicatie tussen applicatie en database. Toepassing die geen gebruik kan maken van toepassingslaagcaches of [batching](sql-database-use-batching-to-improve-performance.md) aanvragen en veel SQL-query's moet verzenden die snel moeten worden verwerkt, zijn goede kandidaten voor de laag Bedrijfskritiek.
-   Groot aantal updates : bewerkingen invoegen, bijwerken en verwijderen, wijzigen de gegevenspagina's `CHECKPOINT` in het geheugen (vuile pagina) die moeten worden opgeslagen in gegevensbestanden met bewerking. Potentiële database engine proces crash of een failover van de database met een groot aantal vuile pagina's kan verhogen hersteltijd in General Purpose tier. Gebruik de laag Bedrijfskritiek als u een werkbelasting hebt die veel geheugenwijzigingen veroorzaakt. 
-   Langlopende transacties die gegevens wijzigen. Transacties die langer worden geopend, voorkomen dat logboekbestanden worden afgekapt waardoor de logboekgrootte en het aantal [virtuele logboekbestanden (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch)kunnen toenemen. Een hoog aantal VLF kan het herstel van de database vertragen na een failover.
-   Werkbelasting met rapportage- en analytische query's die kunnen worden doorgestuurd naar de gratis secundaire rep-only replica.
- Hogere tolerantie en sneller herstel van de storingen. In een geval van systeemfout wordt de database op primaire instantie uitgeschakeld en wordt een van de secundaire replica's onmiddellijk nieuwe lees-schrijf primaire database die klaar is om de query's te verwerken. Database engine hoeft geen transacties uit het logboekbestand te analyseren en opnieuw uit te voeren en alle gegevens in de geheugenbuffer te laden.
- Geavanceerde bescherming voor gegevenscorruptie - Business Critical tier maakt gebruik van databasereplica's achter de schermen voor bedrijfscontinuïteitsdoeleinden, en dus maakt de service ook gebruik van automatische paginareparatie, dezelfde technologie die wordt gebruikt voor SQL [Server-databasemirroring- en beschikbaarheidsgroepen.](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring) In het geval dat een replica een pagina niet kan lezen vanwege een probleem met de gegevensintegriteit, wordt een nieuwe kopie van de pagina opgehaald uit een andere replica, ter vervanging van de onleesbare pagina zonder gegevensverlies of uitvaltijd van de klant. Deze functionaliteit is van toepassing in de laag Algemeen doel als de database geo-secundaire replica heeft.
- Hogere beschikbaarheid - Business Critical-niveau in multi-AZ-configuratie garandeert 99,995% beschikbaarheid, vergeleken met 99,99% van de categorie Algemeen Doel.
- Snel geoherstel - Business Critical-laag geconfigureerd met geo-replicatie heeft een gegarandeerde Recovery Point-doelstelling (RPO) van 5 sec en Hersteltijddoelstelling (RTO) van 30 sec voor 100% van de geïmplementeerde uren.

## <a name="next-steps"></a>Volgende stappen

- Zoek resourcekenmerken (aantal cores, IO, geheugen) van business kritieke laag in [Beheerde instantie,](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)Enkele database in [vCore-model](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) of [DTU-model](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)of Elastic pool in [vCore-model](sql-database-vcore-resource-limits-elastic-pools.md#business-critical---provisioned-compute---gen4) en [DTU-model](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Meer informatie over de lagen [Algemeen Doel](sql-database-service-tier-general-purpose.md) en [Hyperscale.](sql-database-service-tier-hyperscale.md)
- Meer informatie over [Service Fabric](../service-fabric/service-fabric-overview.md).
- Zie [Business Continuity](sql-database-business-continuity.md)voor meer opties voor hoge beschikbaarheid en disaster recovery.
