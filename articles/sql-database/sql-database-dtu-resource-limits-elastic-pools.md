---
title: DTU-resource beperkt elastische pools
description: Op deze pagina worden enkele veelvoorkomende DTU-bronlimieten beschreven voor elastische groepen in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
ms.date: 03/14/2019
ms.openlocfilehash: 7b7ef3b6f2d400dafb28cfb7a15cf95cbbe2c457
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351013"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Resourceslimieten voor elastische pools met behulp van het DTU-inkoopmodel

In dit artikel vindt u de gedetailleerde resourcelimieten voor elastische pools van Azure SQL Database en gepoolde databases met behulp van het DTU-inkoopmodel.

Zie [DTU-resourcelimieten voor DTU-bronlimieten voor DTU-inkoopmodellen :](sql-database-vcore-resource-limits-elastic-pools.md) Zie [vCore-resourcelimieten voor vCore-resourcelimieten - afzonderlijke databases](sql-database-vcore-resource-limits-single-databases.md) en [vCore-resourcelimieten - elastische pools](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Elastische pool: opslaggroottes en rekengroottes

Voor elastische groepen van SQL Database worden in de volgende tabellen de resources weergegeven die beschikbaar zijn op elke servicelaag en rekengrootte. U de servicelaag, de rekengrootte en het opslagbedrag instellen met behulp van de [Azure-portal,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [PowerShell,](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases)de [Azure CLI](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)of de [REST-API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Zie [Een elastische pool schalen](sql-database-elastic-pool-scale.md) voor schaalrichtlijnen en overwegingen
> [!NOTE]
> De resourcelimieten van afzonderlijke databases in elastische pools zijn over het algemeen hetzelfde als voor afzonderlijke databases buiten groepen op basis van DT's en de servicelaag. De maximale gelijktijdige werknemers voor een S2-database zijn bijvoorbeeld 120 werknemers. Dus, de maximale gelijktijdige werknemers voor een database in een standaard groep is ook 120 werknemers als de maximale DTU per database in de groep is 50 DTU's (wat overeenkomt met S2).

### <a name="basic-elastic-pool-limits"></a>Basisbeperkingen voor elastische groepen

| eDTU's per groep | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Inbegrepen opslag per groep (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximale opslagopties per groep (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Max. | N.v.t. | N.v.t. | N.v.t. | N.v.t. | N.v.t. | N.v.t. | N.v.t. | N.v.t. |
| Maximum aantal DBs per poule <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Max gelijktijdige werknemers (aanvragen) per pool <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maximale gelijktijdige sessies per poule <sup>2</sup> | 30.000 | 30.000 | 30.000 | 30.000 |30.000 | 30.000 | 30.000 | 30.000 |
| Min eDTUs-keuzes per database | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Max eDTU-keuzes per database | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Maximale opslag per database (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

<sup>1</sup> Zie [Resourcebeheer in dichte elastische pools](sql-database-elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>2</sup> Zie Limieten voor afzonderlijke [databasebronnen](sql-database-vcore-resource-limits-single-databases.md)voor de max gelijktijdige werknemers (aanvragen) voor een afzonderlijke database. Als de elastische groep bijvoorbeeld Gen5 gebruikt en de maximale vCore per database is ingesteld op 2, is de maximale waarde voor gelijktijdige werknemers 200.  Als max vCore per database is ingesteld op 0,5, is de maximale waarde voor gelijktijdige werknemers 50, omdat er op Gen5 maximaal 100 gelijktijdige werknemers per vCore zijn. Voor andere max vCore-instellingen per database die minder 1 vCore of minder zijn, wordt het aantal max gelijktijdige werknemers op dezelfde manier opnieuw geschaald.

### <a name="standard-elastic-pool-limits"></a>Standaardbeperkingen voor elastische pools

| eDTU's per groep | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Inbegrepen opslag per groep (GB) | 50 | 100 | 200 | 300 | 400 | 800 |
| Maximale opslagopties per groep (GB) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 |
| Max. | N.v.t. | N.v.t. | N.v.t. | N.v.t. | N.v.t. | N.v.t. |
| Maximum aantal DBs per poule <sup>1</sup> | 100 | 200 | 500 | 500 | 500 | 500 |
| Max gelijktijdige werknemers (aanvragen) per pool <sup>2</sup> | 100 | 200 | 400 | 600 | 800 | 1600 |
| Maximale gelijktijdige sessies per poule <sup>2</sup> | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
| Min eDTUs-keuzes per database | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Max eDTU-keuzes per database | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Maximale opslag per database (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

<sup>1</sup> Zie [Resourcebeheer in dichte elastische pools](sql-database-elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>2</sup> Zie Limieten voor afzonderlijke [databasebronnen](sql-database-vcore-resource-limits-single-databases.md)voor de max gelijktijdige werknemers (aanvragen) voor een afzonderlijke database. Als de elastische groep bijvoorbeeld Gen5 gebruikt en de maximale vCore per database is ingesteld op 2, is de maximale waarde voor gelijktijdige werknemers 200.  Als max vCore per database is ingesteld op 0,5, is de maximale waarde voor gelijktijdige werknemers 50, omdat er op Gen5 maximaal 100 gelijktijdige werknemers per vCore zijn. Voor andere max vCore-instellingen per database die minder 1 vCore of minder zijn, wordt het aantal max gelijktijdige werknemers op dezelfde manier opnieuw geschaald.

### <a name="standard-elastic-pool-limits-continued"></a>Limieten voor Standard elastische pools (vervolg)

| eDTU's per groep | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Inbegrepen opslag per groep (GB) | 1200 | 1600 | 2000 | 2500 | 3000 |
| Maximale opslagopties per groep (GB) | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Max. | N.v.t. | N.v.t. | N.v.t. | N.v.t. | N.v.t. |
| Maximum aantal DBs per poule <sup>1</sup> | 500 | 500 | 500 | 500 | 500 |
| Max gelijktijdige werknemers (aanvragen) per pool <sup>2</sup> | 2400 | 3200 | 4000 | 5000 | 6000 |
| Maximale gelijktijdige sessies per poule <sup>2</sup> | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
| Min eDTUs-keuzes per database | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Max eDTU-keuzes per database | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Maximale opslagopties per database (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Zie [Resourcebeheer in dichte elastische pools](sql-database-elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>2</sup> Zie Limieten voor afzonderlijke [databasebronnen](sql-database-vcore-resource-limits-single-databases.md)voor de max gelijktijdige werknemers (aanvragen) voor een afzonderlijke database. Als de elastische groep bijvoorbeeld Gen5 gebruikt en de maximale vCore per database is ingesteld op 2, is de maximale waarde voor gelijktijdige werknemers 200.  Als max vCore per database is ingesteld op 0,5, is de maximale waarde voor gelijktijdige werknemers 50, omdat er op Gen5 maximaal 100 gelijktijdige werknemers per vCore zijn. Voor andere max vCore-instellingen per database die minder 1 vCore of minder zijn, wordt het aantal max gelijktijdige werknemers op dezelfde manier opnieuw geschaald.

### <a name="premium-elastic-pool-limits"></a>Limieten voor Premium elastische pools

| eDTU's per groep | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Inbegrepen opslag per groep (GB) | 250 | 500 | 750 | 1024 | 1536 |
| Maximale opslagopties per groep (GB) | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Max. | 1 | 2 | 4 | 10 | 12 |
| Maximum aantal DBs per poule <sup>1</sup> | 50 | 100 | 100 | 100 | 100 |
| Max gelijktijdige werknemers per groep (aanvragen) <sup>2</sup> | 200 | 400 | 800 | 1600 | 2400 |
| Maximale gelijktijdige sessies per poule <sup>2</sup> | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
| Minimaal aantal eDTU’s per database | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| Maximaal aantal eDTU’s per database | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Maximale opslag per database (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Zie [Resourcebeheer in dichte elastische pools](sql-database-elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>2</sup> Zie Limieten voor afzonderlijke [databasebronnen](sql-database-vcore-resource-limits-single-databases.md)voor de max gelijktijdige werknemers (aanvragen) voor een afzonderlijke database. Als de elastische groep bijvoorbeeld Gen5 gebruikt en de maximale vCore per database is ingesteld op 2, is de maximale waarde voor gelijktijdige werknemers 200.  Als max vCore per database is ingesteld op 0,5, is de maximale waarde voor gelijktijdige werknemers 50, omdat er op Gen5 maximaal 100 gelijktijdige werknemers per vCore zijn. Voor andere max vCore-instellingen per database die minder 1 vCore of minder zijn, wordt het aantal max gelijktijdige werknemers op dezelfde manier opnieuw geschaald.

### <a name="premium-elastic-pool-limits-continued"></a>Limieten voor Premium elastische pools (vervolg)

| eDTU's per groep | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Inbegrepen opslag per groep (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Maximale opslagopties per groep (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Max. | 16 | 20 | 24 | 28 | 32 |
| Maximum aantal DBs per poule <sup>1</sup> | 100 | 100 | 100 | 100 | 100 |
| Max gelijktijdige werknemers (aanvragen) per pool <sup>2</sup> | 3200 | 4000 | 4800 | 5600 | 6400 |
| Maximale gelijktijdige sessies per poule <sup>2</sup> | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
| Min eDTUs-keuzes per database | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Max eDTU-keuzes per database | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Maximale opslag per database (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

<sup>1</sup> Zie [Resourcebeheer in dichte elastische pools](sql-database-elastic-pool-resource-management.md) voor aanvullende overwegingen.

<sup>2</sup> Zie Limieten voor afzonderlijke [databasebronnen](sql-database-vcore-resource-limits-single-databases.md)voor de max gelijktijdige werknemers (aanvragen) voor een afzonderlijke database. Als de elastische groep bijvoorbeeld Gen5 gebruikt en de maximale vCore per database is ingesteld op 2, is de maximale waarde voor gelijktijdige werknemers 200.  Als max vCore per database is ingesteld op 0,5, is de maximale waarde voor gelijktijdige werknemers 50, omdat er op Gen5 maximaal 100 gelijktijdige werknemers per vCore zijn. Voor andere max vCore-instellingen per database die minder 1 vCore of minder zijn, wordt het aantal max gelijktijdige werknemers op dezelfde manier opnieuw geschaald.

> [!IMPORTANT]
> Meer dan 1 TB aan opslag in de Premium-laag is momenteel beschikbaar in alle regio's behalve: China East, China North, Germany Central, Germany Northeast, West Central US, US DoD regions, and US Government Central. In deze regio’s is de maximale opslagruimte in de Premium-laag beperkt tot 1 TB.  Raadpleeg [P11-P15 huidige beperkingen](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb) voor meer informatie.

Als alle DTU's van een elastische groep zijn gebruikt, ontvangt elke database in de groep een gelijke hoeveelheid resources om query's te verwerken. De service SQL Database verdeelt resources eerlijk over databases door gelijke hoeveelheden rekentijd te garanderen. Gelijke verdeling van resources in een elastische groep vindt plaats naast een bepaalde hoeveelheid resources die sowieso voor elke database wordt gegarandeerd wanneer het aantal DTU's per database is ingesteld op een andere waarde dan nul.

> [!NOTE]
> Zie `tempdb` [tempdb-limieten](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)voor limieten .

### <a name="database-properties-for-pooled-databases"></a>Database-eigenschappen voor gepoolde databases

In de volgende tabel worden de eigenschappen voor samengevoegde databases beschreven.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Maximaal aantal eDTU’s per database |Het maximum aantal eDTU's dat elke database in de groep mag gebruiken, is afhankelijk van het gebruik door andere databases in de groep. Het maximum aantal eDTU's per database is geen resourcegarantie voor een database. Het is een algemene instelling voor alle databases in de groep. Stel het maximum aantal eDTU's per database hoog genoeg zijn om pieken in databasegebruik te kunnen afhandelen. Enige mate van overtoewijzing is normaal, omdat de groep in het algemeen uitgaat van pieken en dalen in gebruikspatronen voor databases, waarbij alle databases niet tegelijkertijd pieken. Stel, het piekgebruik per database is twintig eDTU's en slechts 20% van de 100 databases in de groep pieken op hetzelfde moment. Als het maximum aantal eDTU's per database is ingesteld op twintig eDTU's, kan de groep redelijkerwijs vijf keer worden overtoegewezen en kan het aantal eDTU's per groep worden ingesteld op 400. |
| Minimaal aantal eDTU’s per database |Het minimum aantal eDTU's dat voor elke database in de groep wordt gegarandeerd. Het is een algemene instelling voor alle databases in de groep. Het minimum aantal eDTU’s per database kan worden ingesteld op 0. Dat is ook de standaardwaarde. Deze eigenschap wordt ingesteld op een waarde tussen 0 en het gemiddelde eDTU-gebruik per database. Het product van het aantal databases in de groep en het minimum aantal eDTU's per database mag niet groter zijn dan het aantal eDTU's per groep. Als een groep bijvoorbeeld twintig databases heeft en het minimum aantal eDTU's per database is ingesteld op tien eDTU's, dan moet het aantal eDTU's per groep minimaal 200 eDTU's zijn. |
| Maximale opslag per database |De maximale databasegrootte die door de gebruiker is ingesteld voor een database in een groep. Gepoolde databases delen echter toegewezen poolopslag. Zelfs als de totale maximale opslag *per database* groter is dan de totale beschikbare opslagruimte van *de groep,* kan de totale ruimte die daadwerkelijk door alle databases wordt gebruikt, de beschikbare poollimiet niet overschrijden. Maximale databasegrootte verwijst naar de maximale grootte van de gegevensbestanden en omvat niet de ruimte die wordt gebruikt door logbestanden. |
|||

## <a name="next-steps"></a>Volgende stappen

- Zie [resourcelimieten voor afzonderlijke databases met het vCore-inkoopmodel](sql-database-vcore-resource-limits-single-databases.md) voor vCore-resourcelimieten voor één database
- Zie [resourcelimieten voor afzonderlijke databases met behulp van het DTU-inkoopmodel](sql-database-dtu-resource-limits-single-databases.md) voor DTU-resourcelimieten voor één database
- Zie resourcelimieten voor elastische pools voor vCore-resourcelimieten [met behulp van het vCore-inkoopmodel](sql-database-vcore-resource-limits-elastic-pools.md)
- Zie [Beheerde instantieresourcelimieten](sql-database-managed-instance-resource-limits.md)voor resourcelimieten voor beheerde instanties .
- Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor informatie over algemene Azure-limieten.
- Zie [overzicht van resourcelimieten op een SQL Database-server](sql-database-resource-limits-database-server.md) voor informatie over limieten op de server en het abonnement voor informatie over resourcelimieten op een databaseserver.
