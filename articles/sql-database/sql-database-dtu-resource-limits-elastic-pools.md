---
title: DTU-resource beperkt elastische Pools
description: Op deze pagina worden enkele algemene DTU-resource limieten voor elastische Pools in Azure SQL Database beschreven.
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
ms.openlocfilehash: 3f6bbef8f274c5efb99d5b98961450aa7b001527
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264862"
---
# <a name="resources-limits-for-elastic-pools-using-the-dtu-purchasing-model"></a>Bronnen limieten voor elastische Pools met behulp van het DTU-aankoop model

In dit artikel vindt u gedetailleerde resource limieten voor het Azure SQL Database van elastische Pools en gepoolde data bases met behulp van het DTU-aankoop model.

Zie [DTU-resource limieten-afzonderlijke data bases](sql-database-vcore-resource-limits-elastic-pools.md)voor DTU-inkoop model resource limieten voor afzonderlijke data bases. Zie voor vCore resource limieten [vCore resource limieten: individuele data bases](sql-database-vcore-resource-limits-single-databases.md) en [vCore resource limieten: elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md).

## <a name="elastic-pool-storage-sizes-and-compute-sizes"></a>Elastische pool: opslag grootten en reken grootten

Voor SQL Database elastische Pools tonen de volgende tabellen de resources die beschikbaar zijn in elke servicelaag en de berekenings grootte. U kunt de servicelaag, de reken grootte en de opslag hoeveelheid instellen met behulp van de [Azure Portal](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [Power shell](sql-database-elastic-pool-manage.md#powershell-manage-elastic-pools-and-pooled-databases), de [Azure cli](sql-database-elastic-pool-manage.md#azure-cli-manage-elastic-pools-and-pooled-databases)of de [rest API](sql-database-elastic-pool-manage.md#rest-api-manage-elastic-pools-and-pooled-databases).

> [!IMPORTANT]
> Zie [een elastische pool schalen](sql-database-elastic-pool-scale.md) voor schaal baarheid en overwegingen
> [!NOTE]
> De resource limieten van afzonderlijke data bases in elastische Pools zijn in het algemeen hetzelfde als voor afzonderlijke data bases buiten Pools op basis van Dtu's en de servicelaag. Zo is het maximum aantal gelijktijdige werk rollen voor een S2-data base 120 werk nemers. Het maximum aantal gelijktijdige werk rollen voor een data base in een Standard-pool is dus ook 120 werk nemers als het maximale DTU per data base in de pool 50 Dtu's is (wat gelijk is aan S2).

### <a name="basic-elastic-pool-limits"></a>Basisbeperkingen voor elastische groepen

| eDTU's per groep | **50** | **100** | **200** | **300** | **400** | **800** | **1200** | **1600** |
|:---|---:|---:|---:| ---: | ---: | ---: | ---: | ---: |
| Inbegrepen opslag per pool (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximum aantal opslag opties per groep (GB) | 5 | 10 | 20 | 29 | 39 | 78 | 117 | 156 |
| Maximale OLTP-opslag in het geheugen per pool (GB) | N/A | N/A | N/A | N/A | N/A | N/A | N/A | N/A |
| Maximumaantal databases per pool | 100 | 200 | 500 | 500 | 500 | 500 | 500 | 500 |
| Maximaal aantal gelijktijdige werknemers (aanvragen) per pool | 100 | 200 | 400 | 600 | 800 | 1600 | 2400 | 3200 |
| Maximaal aantal gelijktijdige sessies per pool | 30.000 | 30.000 | 30.000 | 30.000 |30.000 | 30.000 | 30.000 | 30.000 |
| Min. aantal Edtu's-keuzes per data base | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 | 0, 5 |
| Maximum aantal Edtu's-keuzes per data base | 5 | 5 | 5 | 5 | 5 | 5 | 5 | 5 |
| Maximale opslag per database (GB) | 2 | 2 | 2 | 2 | 2 | 2 | 2 | 2 |
||||||||

### <a name="standard-elastic-pool-limits"></a>Standaardbeperkingen voor elastische pools

| eDTU's per groep | **50** | **100** | **200** | **300** | **400** | **800**|
|:---|---:|---:|---:| ---: | ---: | ---: |
| Inbegrepen opslag per pool (GB) | 50 | 100 | 200 | 300 | 400 | 800 |
| Maximum aantal opslag opties per groep (GB) | 50, 250, 500 | 100, 250, 500, 750 | 200, 250, 500, 750, 1024 | 300, 500, 750, 1024, 1280 | 400, 500, 750, 1024, 1280, 1536 | 800, 1024, 1280, 1536, 1792, 2048 |
| Maximale OLTP-opslag in het geheugen per pool (GB) | N/A | N/A | N/A | N/A | N/A | N/A |
| Maximumaantal databases per pool | 100 | 200 | 500 | 500 | 500 | 500 |
| Maximaal aantal gelijktijdige werknemers (aanvragen) per pool | 100 | 200 | 400 | 600 | 800 | 1600 |
| Maximaal aantal gelijktijdige sessies per pool | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
| Min. aantal Edtu's-keuzes per data base | 0, 10, 20, 50 | 0, 10, 20, 50, 100 | 0, 10, 20, 50, 100, 200 | 0, 10, 20, 50, 100, 200, 300 | 0, 10, 20, 50, 100, 200, 300, 400 | 0, 10, 20, 50, 100, 200, 300, 400, 800 |
| Maximum aantal Edtu's-keuzes per data base | 10, 20, 50 | 10, 20, 50, 100 | 10, 20, 50, 100, 200 | 10, 20, 50, 100, 200, 300 | 10, 20, 50, 100, 200, 300, 400 | 10, 20, 50, 100, 200, 300, 400, 800 |
| Maximale opslag per database (GB) | 500 | 750 | 1024 | 1024 | 1024 | 1024 |
||||||||

### <a name="standard-elastic-pool-limits-continued"></a>Limieten voor Standard elastische pools (vervolg)

| eDTU's per groep | **1200** | **1600** | **2000** | **2500** | **3000** |
|:---|---:|---:|---:| ---: | ---: |
| Inbegrepen opslag per pool (GB) | 1200 | 1600 | 2000 | 2500 | 3000 |
| Maximum aantal opslag opties per groep (GB) | 1200, 1280, 1536, 1792, 2048, 2304, 2560 | 1600, 1792, 2048, 2304, 2560, 2816, 3072 | 2000, 2048, 2304, 2560, 2816, 3072, 3328, 3584 | 2500, 2560, 2816, 3072, 3328, 3584, 3840, 4096 | 3000, 3072, 3328, 3584, 3840, 4096 |
| Maximale OLTP-opslag in het geheugen per pool (GB) | N/A | N/A | N/A | N/A | N/A |
| Maximumaantal databases per pool | 500 | 500 | 500 | 500 | 500 |
| Maximaal aantal gelijktijdige werknemers (aanvragen) per pool | 2400 | 3200 | 4000 | 5000 | 6000 |
| Maximaal aantal gelijktijdige sessies per pool | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
| Min. aantal Edtu's-keuzes per data base | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 0, 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Maximum aantal Edtu's-keuzes per data base | 10, 20, 50, 100, 200, 300, 400, 800, 1200 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500 | 10, 20, 50, 100, 200, 300, 400, 800, 1200, 1600, 2000, 2500, 3000 |
| Maximum aantal opslag opties per data base (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

### <a name="premium-elastic-pool-limits"></a>Limieten voor Premium elastische pools

| eDTU's per groep | **125** | **250** | **500** | **1000** | **1500**|
|:---|---:|---:|---:| ---: | ---: |
| Inbegrepen opslag per pool (GB) | 250 | 500 | 750 | 1024 | 1536 |
| Maximum aantal opslag opties per groep (GB) | 250, 500, 750, 1024 | 500, 750, 1024 | 750, 1024 | 1024 | 1536 |
| Maximale OLTP-opslag in het geheugen per pool (GB) | 1 | 2 | 4 | 10 | 12 |
| Maximumaantal databases per pool | 50 | 100 | 100 | 100 | 100 |
| Maximaal aantal gelijktijdige werknemers per pool (aanvragen) | 200 | 400 | 800 | 1600 | 2400 |
| Maximaal aantal gelijktijdige sessies per pool | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
| Minimaal aantal eDTU’s per database | 0, 25, 50, 75, 125 | 0, 25, 50, 75, 125, 250 | 0, 25, 50, 75, 125, 250, 500 | 0, 25, 50, 75, 125, 250, 500, 1000 | 0, 25, 50, 75, 125, 250, 500, 1000|
| Maximaal aantal eDTU’s per database | 25, 50, 75, 125 | 25, 50, 75, 125, 250 | 25, 50, 75, 125, 250, 500 | 25, 50, 75, 125, 250, 500, 1000 | 25, 50, 75, 125, 250, 500, 1000|
| Maximale opslag per database (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

### <a name="premium-elastic-pool-limits-continued"></a>Limieten voor Premium elastische pools (vervolg)

| eDTU's per groep | **2000** | **2500** | **3000** | **3500** | **4000**|
|:---|---:|---:|---:| ---: | ---: |
| Inbegrepen opslag per pool (GB) | 2048 | 2560 | 3072 | 3548 | 4096 |
| Maximum aantal opslag opties per groep (GB) | 2048 | 2560 | 3072 | 3548 | 4096|
| Maximale OLTP-opslag in het geheugen per pool (GB) | 16 | 20 | 24 | 28 | 32 |
| Maximumaantal databases per pool | 100 | 100 | 100 | 100 | 100 |
| Maximaal aantal gelijktijdige werknemers (aanvragen) per pool | 3200 | 4000 | 4800 | 5600 | 6400 |
| Maximaal aantal gelijktijdige sessies per pool | 30.000 | 30.000 | 30.000 | 30.000 | 30.000 |
| Min. aantal Edtu's-keuzes per data base | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750 | 0, 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Maximum aantal Edtu's-keuzes per data base | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750 | 25, 50, 75, 125, 250, 500, 1000, 1750, 4000 |
| Maximale opslag per database (GB) | 1024 | 1024 | 1024 | 1024 | 1024 |
|||||||

> [!IMPORTANT]
> Meer dan 1 TB aan opslag ruimte in de Premium-laag is momenteel beschikbaar in alle regio's behalve: China-oost, China-noord, Duitsland-centraal, Duitsland-noordoost, VS-West-Centraal, US DoD regio's en Amerikaanse overheid centraal. In deze regio’s is de maximale opslagruimte in de Premium-laag beperkt tot 1 TB.  Raadpleeg [P11-P15 huidige beperkingen](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb) voor meer informatie.

Als alle DTU's van een elastische groep zijn gebruikt, ontvangt elke database in de groep een gelijke hoeveelheid resources om query's te verwerken. De service SQL Database verdeelt resources eerlijk over databases door gelijke hoeveelheden rekentijd te garanderen. Gelijke verdeling van resources in een elastische groep vindt plaats naast een bepaalde hoeveelheid resources die sowieso voor elke database wordt gegarandeerd wanneer het aantal DTU's per database is ingesteld op een andere waarde dan nul.

> [!NOTE]
> Zie voor `tempdb` limieten [tempdb-limieten](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

### <a name="database-properties-for-pooled-databases"></a>Data base-eigenschappen voor gegroepeerde Data bases

De volgende tabel beschrijft de eigenschappen voor gepoolde data bases.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Maximaal aantal eDTU’s per database |Het maximum aantal eDTU's dat elke database in de groep mag gebruiken, is afhankelijk van het gebruik door andere databases in de groep. Het maximum aantal eDTU's per database is geen resourcegarantie voor een database. Het is een algemene instelling voor alle databases in de groep. Stel het maximum aantal eDTU's per database hoog genoeg zijn om pieken in databasegebruik te kunnen afhandelen. Enige mate van overtoewijzing is normaal, omdat de groep in het algemeen uitgaat van pieken en dalen in gebruikspatronen voor databases, waarbij alle databases niet tegelijkertijd pieken. Stel, het piekgebruik per database is twintig eDTU's en slechts 20% van de 100 databases in de groep pieken op hetzelfde moment. Als het maximum aantal eDTU's per database is ingesteld op twintig eDTU's, kan de groep redelijkerwijs vijf keer worden overtoegewezen en kan het aantal eDTU's per groep worden ingesteld op 400. |
| Minimaal aantal eDTU’s per database |Het minimum aantal eDTU's dat voor elke database in de groep wordt gegarandeerd. Het is een algemene instelling voor alle databases in de groep. Het minimum aantal eDTU’s per database kan worden ingesteld op 0. Dat is ook de standaardwaarde. Deze eigenschap wordt ingesteld op een waarde tussen 0 en het gemiddelde eDTU-gebruik per database. Het product van het aantal databases in de groep en het minimum aantal eDTU's per database mag niet groter zijn dan het aantal eDTU's per groep. Als een groep bijvoorbeeld twintig databases heeft en het minimum aantal eDTU's per database is ingesteld op tien eDTU's, dan moet het aantal eDTU's per groep minimaal 200 eDTU's zijn. |
| Maximale opslag per data base |De maximale database grootte die is ingesteld door de gebruiker voor een data base in een pool. Gegroepeerde Data bases delen echter toegewezen pool opslag. Zelfs als de totale maximum opslag *per data base* is ingesteld op groter dan de totale beschik bare opslag *ruimte van de groep*, kan de totale ruimte die daad werkelijk door alle data bases wordt gebruikt niet groter zijn dan de beschik bare groeps limiet. De maximale database grootte verwijst naar de maximale grootte van de gegevens bestanden en bevat niet de ruimte die wordt gebruikt door de logboek bestanden. |
|||

## <a name="next-steps"></a>Volgende stappen

- Zie [resource limieten voor afzonderlijke data bases met behulp van het vCore-aankoop model](sql-database-vcore-resource-limits-single-databases.md) voor vCore resource limieten voor één data base
- Zie [resource limieten voor afzonderlijke data bases met behulp van het DTU-aankoop model](sql-database-dtu-resource-limits-single-databases.md) voor DTU-resource limieten voor één data base
- Zie [resource limieten voor elastische Pools met behulp van het vCore-aankoop model](sql-database-vcore-resource-limits-elastic-pools.md) voor vCore resource limieten voor elastische Pools
- Zie [resource limieten voor beheerde](sql-database-managed-instance-resource-limits.md)exemplaren voor resource limieten voor beheerde instanties.
- Zie [Azure-abonnement en service limieten, quota's en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over algemene Azure-limieten.
- Zie [overzicht van resource limieten op een SQL database server](sql-database-resource-limits-database-server.md) voor informatie over limieten op het niveau van de server en het abonnement voor meer informatie over de limieten voor bronnen op een database server.
