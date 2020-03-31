---
title: DTU-resource beperkt afzonderlijke databases
description: Op deze pagina worden enkele veelvoorkomende DTU-bronlimieten beschreven voor afzonderlijke databases in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: a4c435b4874301fe6fb804085c5b265954cd4f5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256313"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model"></a>Resourcelimieten voor afzonderlijke databases met behulp van het DTU-aankoopmodel

In dit artikel vindt u de gedetailleerde resourcelimieten voor afzonderlijke databases van Azure SQL Database met behulp van het DTU-inkoopmodel.

Zie [DTU-resourcelimieten voor DTU-inkoopbronnen](sql-database-dtu-resource-limits-elastic-pools.md)voor elastische pools - elastische pools . Zie [vCore-resourcelimieten voor vCore-resourcelimieten - afzonderlijke databases](sql-database-vcore-resource-limits-single-databases.md) en [vCore-resourcelimieten - elastische pools](sql-database-vcore-resource-limits-elastic-pools.md). Zie [Inkoopmodellen en servicelagen voor](sql-database-purchase-models.md)meer informatie over de verschillende inkoopmodellen.

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Enkele database: opslaggroottes en rekengroottes

In de volgende tabellen worden de resources weergegeven die beschikbaar zijn voor één database op elke servicelaag en rekengrootte. U de servicelaag, de rekengrootte en het opslagbedrag voor één database instellen met behulp van de [Azure-portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL,](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases) [PowerShell,](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases)de [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases)of de [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Zie [Eén database schalen](sql-database-single-database-scale.md) voor schaalrichtlijnen en overwegingen

### <a name="basic-service-tier"></a>Servicelaag Basic

| **Rekengrootte** | **Basic** |
| :--- | --: |
| Maximaal aantal DTU's | 5 |
| Inbegrepen opslag (GB) | 2 |
| Maximale opslagopties (GB) | 2 |
| Maximale OLTP-opslag in het geheugen (GB) |N.v.t. |
| Max gelijktijdige werknemers (aanvragen) | 30 |
| Maximaal aantal gelijktijdige sessies | 300 |
|||

> [!IMPORTANT]
> De Basic-servicelaag biedt minder dan één vCore (CPU).  Voor CPU-intensieve workloads wordt een servicelaag van S3 of hoger aanbevolen. 
>
>Wat gegevensopslag betreft, wordt de servicelaag Basic geplaatst op Standaardpaginablobs. Standaard paginablobs gebruiken opslagmedia op basis van harde schijven (HDD) en zijn het meest geschikt voor ontwikkeling, testen en andere zelden geopende workloads die minder gevoelig zijn voor prestatievariabiliteit.
>

### <a name="standard-service-tier"></a>Servicelaag Standard

| **Rekengrootte** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| Maximaal aantal DTU's | 10 | 20 | 50 | 100 |
| Inbegrepen opslag (GB) | 250 | 250 | 250 | 250 |
| Maximale opslagopties (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Maximale OLTP-opslag in het geheugen (GB) | N.v.t. | N.v.t. | N.v.t. | N.v.t. |
| Max gelijktijdige werknemers (aanvragen)| 60 | 90 | 120 | 200 |
| Maximaal aantal gelijktijdige sessies |600 | 900 | 1200 | 2400 |
||||||

> [!IMPORTANT]
> De Standard S0- S1- en S2-lagen bieden minder dan één vCore (CPU).  Voor CPU-intensieve workloads wordt een servicelaag van S3 of hoger aanbevolen. 
>
>Wat gegevensopslag betreft, worden de standaardservicelagen S0 en S1 op Standaardpaginablobs geplaatst. Standaard paginablobs gebruiken opslagmedia op basis van harde schijven (HDD) en zijn het meest geschikt voor ontwikkeling, testen en andere zelden geopende workloads die minder gevoelig zijn voor prestatievariabiliteit.
>

### <a name="standard-service-tier-continued"></a>Standaardservicelaag (vervolg)

| **Rekengrootte** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| Maximaal aantal DTU's | 200 | 400 | 800 | 1600 | 3000 |
| Inbegrepen opslag (GB) | 250 | 250 | 250 | 250 | 250 |
| Maximale opslagopties (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Maximale OLTP-opslag in het geheugen (GB) | N.v.t. | N.v.t. | N.v.t. | N.v.t. |N.v.t. |
| Max gelijktijdige werknemers (aanvragen)| 400 | 800 | 1600 | 3200 |6000 |
| Maximaal aantal gelijktijdige sessies |4800 | 9600 | 19200 | 30.000 |30.000 |
|||||||

### <a name="premium-service-tier"></a>Premium servicelaag

| **Rekengrootte** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| Maximaal aantal DTU's | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Inbegrepen opslag (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| Maximale opslagopties (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| Maximale OLTP-opslag in het geheugen (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Max gelijktijdige werknemers (aanvragen)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| Maximaal aantal gelijktijdige sessies | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
|||||||

\*Van 1024 GB tot 4096 GB in stappen van 256 GB

> [!IMPORTANT]
> Meer dan 1 TB aan opslag in de Premium-laag is momenteel beschikbaar in alle regio's behalve: China East, China North, Germany Central, Germany Northeast, West Central US, US DoD regions, and US Government Central. In deze regio’s is de maximale opslagruimte in de Premium-laag beperkt tot 1 TB.  Raadpleeg [P11-P15 huidige beperkingen](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb) voor meer informatie.  
> [!NOTE]
> Zie `tempdb` [tempdb-limieten](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)voor limieten .

## <a name="next-steps"></a>Volgende stappen

- Zie [resourcelimieten voor afzonderlijke databases met het vCore-inkoopmodel](sql-database-vcore-resource-limits-single-databases.md) voor vCore-resourcelimieten voor één database
- Zie resourcelimieten voor elastische pools voor vCore-resourcelimieten [met behulp van het vCore-inkoopmodel](sql-database-vcore-resource-limits-elastic-pools.md)
- Zie resourcelimieten voor elastische pools voor DTU-resourcelimieten [met behulp van het DTU-inkoopmodel](sql-database-dtu-resource-limits-elastic-pools.md)
- Zie [Beheerde instantieresourcelimieten](sql-database-managed-instance-resource-limits.md)voor resourcelimieten voor beheerde instanties .
- Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor informatie over algemene Azure-limieten.
- Zie [overzicht van resourcelimieten op een SQL Database-server](sql-database-resource-limits-database-server.md) voor informatie over limieten op de server en het abonnement voor informatie over resourcelimieten op een databaseserver.
