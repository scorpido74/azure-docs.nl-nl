---
title: Servicelaag voor algemene doeleinden
description: Meer informatie over de algemene doellaag van Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: 7c57755ae63f8af5a2a4faa4764bc6a9597e8c2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255884"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Servicelaag voor algemene doeleinden - Azure SQL-database

> [!NOTE]
> De servicelaag voor algemene doeleinden in het vCore-gebaseerde inkoopmodel wordt de standaardservicelaag in het Op DTU gebaseerde inkoopmodel genoemd. Zie [Azure SQL Database-inkoopmodellen en -bronnen](sql-database-purchase-models.md)voor een vergelijking van het op vCore gebaseerde inkoopmodel met het op DTU gebaseerde inkoopmodel.

Azure SQL Database is gebaseerd op SQL Server database engine architecture aangepast voor de cloud omgeving om 99,99% beschikbaarheid te garanderen, zelfs in het geval van infrastructuurfouten. Er zijn drie servicelagen die worden gebruikt in Azure SQL Database, elk met verschillende architectuurmodellen. Deze servicelagen zijn:

- Algemeen doel
- Bedrijfskritisch
- Hyperscale

Het architecturale model voor de servicelaag voor algemene doeleinden is gebaseerd op een scheiding van rekenkracht en opslag. Dit architecturale model is afhankelijk van een hoge beschikbaarheid en betrouwbaarheid van Azure Blob-opslag die op transparante wijze databasebestanden repliceert en geen gegevensverlies garandeert als onderliggende infrastructuurfouten optreedt.

De volgende afbeelding toont vier knooppunten in standaard architectuurmodel met de gescheiden reken- en opslaglagen.

![Scheiding van rekenkracht en opslag](media/sql-database-managed-instance/general-purpose-service-tier.png)

In het architecturale model voor de servicelaag voor algemene doeleinden zijn er twee lagen:

- Een stateloze rekenlaag die `sqlservr.exe` het proces uitvoert en alleen tijdelijke en cachegegevens bevat (bijvoorbeeld : plan cache, buffergroep, kolomopslaggroep). Dit stateloze SQL Server-knooppunt wordt beheerd door Azure Service Fabric dat het proces initialiseert, de status van het knooppunt regelt en indien nodig fail-over uitvoert naar een andere plaats.
- Een stateful gegevenslaag met databasebestanden (.mdf/.ldf) die zijn opgeslagen in Azure Blob-opslag. Azure Blob-opslag garandeert dat er geen gegevensverlies is van een record dat in een databasebestand wordt geplaatst. Azure Storage heeft ingebouwde beschikbaarheid/redundantie van gegevens die ervoor zorgt dat elke record in logboekbestand of -pagina in het gegevensbestand behouden blijft, zelfs als het SQL Server-proces vastloopt.

Wanneer databaseengine of besturingssysteem wordt geüpgraded, een deel van de onderliggende infrastructuur uitvalt of als er een kritiek probleem wordt gedetecteerd in sql server-proces, verplaatst Azure Service Fabric het statusloze SQL Server-proces naar een ander stateless compute-knooppunt. Er is een set reserveknooppunten die wacht om een nieuwe compute-service uit te voeren als er een failover van het primaire knooppunt plaatsvindt om failovertijd te minimaliseren. Gegevens in de Azure-opslaglaag worden niet beïnvloed en gegevens-/logboekbestanden worden gekoppeld aan het nieuw geïnitialiseerde SQL Server-proces. Dit proces garandeert 99,99% beschikbaarheid, maar het kan enige gevolgen hebben voor de prestaties van de zware werkbelasting die wordt uitgevoerd vanwege de overgangstijd en het feit dat het nieuwe SQL Server-knooppunt begint met koude cache.

## <a name="when-to-choose-this-service-tier"></a>Wanneer deze servicelaag kiezen

De servicelaag voor algemeen gebruik is een standaardservicelaag in Azure SQL Database die is ontworpen voor de meeste algemene workloads. Als u een volledig beheerde databaseengine met 99,99% SLA met opslaglatentie tussen 5 en 10 ms nodig hebt die in de meeste gevallen overeenkomt met Azure SQL IaaS, is de categorie General Purpose de optie voor u.

## <a name="next-steps"></a>Volgende stappen

- Zoek resourcekenmerken (aantal cores, IO, geheugen) van de laag Algemeen doel/Standaard in [Beheerde instantie,](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)Enkele database in [vCore-model](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) of [DTU-model](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)of Elastic pool in [vCore-model](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose---provisioned-compute---gen4) en [DTU-model](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits).
- Meer informatie over [businesskritische](sql-database-service-tier-business-critical.md) en [hyperscale-lagen.](sql-database-service-tier-hyperscale.md)
- Meer informatie over [Service Fabric](../service-fabric/service-fabric-overview.md).
- Zie [Business Continuity](sql-database-business-continuity.md)voor meer opties voor hoge beschikbaarheid en disaster recovery.
