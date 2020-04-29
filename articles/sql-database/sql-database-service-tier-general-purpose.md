---
title: Servicelaag voor algemeen gebruik
description: Meer informatie over de Azure SQL Database-laag voor algemeen gebruik
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79255884"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>Laag voor algemeen gebruik-Azure SQL Database

> [!NOTE]
> De servicelaag voor algemeen gebruik in het op vCore gebaseerde aankoop model wordt de standaard servicelaag genoemd in het op DTU gebaseerde aankoop model. Zie [Azure SQL database-inkoop modellen en-resources](sql-database-purchase-models.md)voor een vergelijking van het op vCore gebaseerde aankoop model met het DTU-gebaseerde aankoop model.

Azure SQL Database is gebaseerd op SQL Server data base engine-architectuur die is aangepast voor de cloud omgeving om 99,99% Beschik baarheid te garanderen, zelfs in het geval van infrastructuur fouten. Er zijn drie service lagen die worden gebruikt in Azure SQL Database, elk met verschillende architectuur modellen. Deze service lagen zijn:

- Algemeen doel
- Bedrijfs kritiek
- Hyperscale

Het architectuur model voor de servicelaag voor algemeen gebruik is gebaseerd op een schei ding van Compute en opslag. Dit architectuur model is afhankelijk van hoge Beschik baarheid en betrouw baarheid van Azure Blob-opslag waarmee database bestanden op transparante wijze worden gerepliceerd en er geen gegevens verloren gaan als er een onderliggende infrastructuur fout optreedt.

In de volgende afbeelding ziet u de vier knoop punten in het standaard model architectuur met de gescheiden reken-en opslag lagen.

![Schei ding van Compute en opslag](media/sql-database-managed-instance/general-purpose-service-tier.png)

In het architectuur model voor de servicelaag voor algemeen gebruik zijn er twee lagen:

- Een stateless Compute-laag die het `sqlservr.exe` proces uitvoert en alleen tijdelijke en in de cache opgeslagen gegevens bevat (bijvoorbeeld: plan cache, buffer groep, Column Store-groep). Dit stateless SQL Server knoop punt wordt uitgevoerd door de Azure-Service Fabric die het proces initialiseert, de status van het knoop punt beheert en een failover naar een andere locatie uitvoert, indien nodig.
- Een stateful gegevenslaag met database bestanden (MDF/. ldf) die zijn opgeslagen in Azure Blob Storage. Azure Blob-opslag garandeert dat er geen gegevens verloren gaan van alle records die in een database bestand worden geplaatst. Azure Storage heeft ingebouwde Beschik baarheid/redundantie van gegevens die ervoor zorgt dat elke record in het logboek bestand of de pagina in het gegevens bestand blijft behouden, zelfs als SQL Server proces vastloopt.

Wanneer de data base-engine of het besturings systeem wordt geüpgraded, mislukt een deel van de onderliggende infra structuur, of als er een kritiek probleem wordt gedetecteerd in SQL Server proces, wordt het stateless SQL Server proces door Azure Service Fabric verplaatst naar een ander stateless Compute-knoop punt. Er is een set reserve knoop punten die wachten op het uitvoeren van een nieuwe compute-service als er een failover van het primaire knoop punt wordt uitgevoerd om de failover-tijd te minimaliseren. Gegevens in azure Storage-laag worden niet beïnvloed en gegevens/logboek bestanden zijn gekoppeld aan nieuw geïnitialiseerd SQL Server proces. Dit proces garandeert een Beschik baarheid van 99,99%, maar het kan enkele prestatie gevolgen hebben voor een zware werk belasting die wordt uitgevoerd als gevolg van de overgangs tijd en het feit dat het nieuwe SQL Server knoop punt begint met koude cache.

## <a name="when-to-choose-this-service-tier"></a>Wanneer u deze servicelaag kiest

Algemeen servicelaag is een standaardservicelaag in Azure SQL Database die is ontworpen voor de meeste algemene werk belastingen. Als u een volledig beheerde data base-engine nodig hebt met een SLA met 99,99% en een opslag latentie tussen 5 en 10 MS die overeenkomt met Azure SQL IaaS in de meeste gevallen, is Algemeen tier de optie voor u.

## <a name="next-steps"></a>Volgende stappen

- Zoek bron kenmerken (aantal kernen, IO, geheugen) van Algemeen/Standard-laag in een [beheerd exemplaar](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), afzonderlijke data base in [VCore model](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) of [DTU-model](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes), of elastische pool in vCore- [model](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose---provisioned-compute---gen4) en DTU- [model](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits).
- Meer informatie over [bedrijfskritiek](sql-database-service-tier-business-critical.md) -en [grootschalige](sql-database-service-tier-hyperscale.md) -lagen.
- Meer informatie over [service Fabric](../service-fabric/service-fabric-overview.md).
- Zie [bedrijfs continuïteit](sql-database-business-continuity.md)voor meer opties voor hoge Beschik baarheid en herstel na nood gevallen.
