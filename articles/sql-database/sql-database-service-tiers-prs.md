---
title: Premium RS service tier pensioen
description: De Premium RS-servicelaag wordt buiten gebruik gesteld en de ondersteuning hiervoor loopt af - zie migratieopties.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 02/07/2019
ms.openlocfilehash: f00ecd19877ba6236bde5de73d450967abc1fe15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821050"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Azure SQL Database Premium RS-servicelaag (preview) wordt buiten gebruik gesteld - opties voor migratie

In februari 2018 kondigde Microsoft aan dat de Premium RS-servicelaag in Azure SQL Database niet zou worden vrijgegeven voor algemene beschikbaarheid en na 31 januari 2019 niet meer zou worden ondersteund. Deze deadline voor de ondersteuning is verlengd tot 30 juni 2019. In dit artikel worden uw opties voor het migreren van de Premium RS-servicelaag naar een andere servicelaag uitgelegd. Na 30 juni 2019 migreert Microsoft uw Premium RS-databases automatisch naar een algemeen beschikbare servicelaag die het meest overeenkomt met de prestatievereisten van uw Premium RS-database.

Hieronder volgen de migratiebestemmingen en prijsopties die mogelijk geschikt zijn voor Premium RS-klanten:

- vCore-servicelagen

  De **servicelagen Algemeen Doel** en **Bedrijfskritieke** in het [op vCore gebaseerde aankoopmodel](sql-database-service-tiers-vcore.md). Deze twee servicelagen zijn algemeen beschikbaar. Het op vCore gebaseerde inkoopmodel biedt ook de **Hyperscale-servicelaag** die on-demand aanpast aan de behoeften van uw workload met automatisch opschalen tot 100 TB per database. De Hyperscale-servicelaag biedt IO-prestaties die vergelijkbaar zijn met de Premium-servicelaag in [het Op DTU gebaseerde inkoopmodel](sql-database-service-tiers-dtu.md) tegen een prijs die dichter bij de Premium RS-servicelaag ligt.
- Dev/Test prijzen

  [Dev/testprijzen](https://azure.microsoft.com/pricing/dev-test/) bieden besparingen tot 55% ten opzichte van licentie-inbegrepen tarieven met uw Visual Studio-abonnement.
- Azure Hybrid Benefit en gereserveerde capaciteitsprijzen

  [Azure Hybrid Benefit en gereserveerde capaciteitsprijzen](https://azure.microsoft.com/pricing/details/sql-database/) bieden besparingen tot 80% ten opzichte van door de licentie opgenomen tarieven. Zie Azure Hybrid Benefit [voor SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) en Azure SQL [Database gereserveerde capaciteit](sql-database-reserved-capacity.md)voor meer informatie over deze opties.

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Handel nu om uw Premium RS-databases te migreren naar alternatieve SQL Database-servicelagen

Bekijk de richtlijnen in dit artikel samen met onze prijzen en documentatie om de juiste migratiebestemming(s) voor uw Premium RS-workloads te bepalen.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Werkbelastingen migreren en opslaan

Voor uw rekenintensieve Premium RS-workloads raden we u aan te migreren naar onze algemeen beschikbare vCore-gebaseerde General Purpose-servicelaag en meer te besparen ten opzichte van licentie-inbegrepen tarieven met behulp van het Azure Hybrid Benefit voor SQL Server en gereserveerde capaciteitsaanbiedingen. Als u liever op een op DTU gebaseerde inkoopoptie wilt blijven, u uw computerintensieve Premium RS-databases migreren naar een standaardservicelaag en toch besparen ten opzichte van de algemene beschikbaarheidsprijzen van Premium RS (als deze in algemene beschikbaarheid was gegaan).

> [!WARNING]
> Als u uw Premium RS-workloads migreert naar Premium-servicelagen op basis van DTU, kan de maandelijkse kosten toenemen ten opzichte van de huidige Premium RS-prijzen. We raden u aan de Hyperscale- of Business Critical-lagen met Azure Hybrid Benefit en de gereserveerde capaciteitsprijzen te overwegen om vergelijkbare of lagere kosten te behouden dan Premium RS.

### <a name="premium-rs-databases"></a>Premium RS-databases

|**Als u momenteel op ...**|**Migreren naar vergelijkbare vCore-gebaseerde...**|**Migreren naar vergelijkbare DTU-gebaseerde...**|
|---|---|---|
|Premium RS 1|Algemeen doel 1 vCore (Gen4)|Standaard 3|
|Premium RS 2|Algemeen doel 2 vCores (Gen4)|Standaard 4|
|Premium RS 4|Algemeen doel 4 vCores (Gen4)|Standaard 6|
|Premium RS 6|Algemeen doel 6 vCores (Gen4)|Standaard 7|

### <a name="premium-rs-pools"></a>Premium RS-pools

|**Als u momenteel op ...**|**Migreren naar vergelijkbare vCore-gebaseerde...**|**Migreren naar vergelijkbare DTU-gebaseerde...**|
|---|---|---|
|Premium RS pool 125 DTU|Algemeen doel 1 vCore (Gen4)|Standaard pool 100 eDTUs|
|Premium RS pool 250 DTU|Algemeen doel 2 vCores (Gen4)|Standaard pool 250 eDTUs|
|Premium RS pool 500 DTU|Algemeen doel 4 vCores (Gen4)|Standaard pool 500 eDTUs|
|Premium RS pool 1000 DTU|Algemeen doel 8 vCores (Gen4)|Standaard pool 1000 eDTUs|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Bespaar en prestaties optimaliseren voor uw IO-intensieve workloads

We raden u aan uw IO-intensieve single databases te migreren naar onze op vCore gebaseerde Hyperscale-laag, momenteel in preview, en uw IO-intensieve databasepools naar onze algemeen beschikbare Business Critical-laag, voor de optimale combinatie van prestaties en kosten.  De volgende op vCore gebaseerde opties behouden of verbeteren uw huidige prestaties en kunnen u geld besparen in combinatie met het Azure Hybrid Benefit en de gereserveerde capaciteitsprijzen.

|**Als u momenteel op ...**|**Migreren naar vergelijkbare vCore-gebaseerde...**|**Migreren naar vergelijkbare DTU-gebaseerde...**|
|---|---|---|
|Premium RS 1| Hyperscale 1 vCore (Gen4) of Business Critical 1 vCore (Gen4)|Premie 1|
|Premium RS 2| Hyperscale 2 vCores (Gen4) of Business Critical 2 vCores (Gen4|Premium 2|
|Premium RS 4| Hyperscale 4 vCores (Gen4) of Business Critical 4 vCores (Gen4)|Premie 4
|Premium RS 6| Hyperscale 6 vCores (Gen4) of Business Critical 6 vCores (Gen4)|Premium 6|

|**Als u momenteel op ...**|**Migreren naar vergelijkbare vCore-gebaseerde...**|**Migreren naar vergelijkbare DTU-gebaseerde...**|
|---|---|---|
|Premium RS pool 125 DTU|Business Critical 2 vCores (Gen4)|Premium pool 125 eDTUs|
|Premium RS pool 250 DTU|Business Critical 2 vCores (Gen4)|Premium pool 250 eDTUs|
|Premium RS pool 500 DTU|Business Critical 4 vCores (Gen4)|Premium pool 500 eDTUs|
|Premium RS pool 1000 DTU|Business Critical 8 vCores (Gen4)|Premium pool 1000 eDTUs|

## <a name="take-advantage-of-our-new-offers"></a>Profiteer van onze nieuwe aanbiedingen

Onze servicelagen in het vCore-gebaseerde inkoopmodel komen in aanmerking voor speciale aanbiedingen die u tot 80% kunnen besparen ten opzichte van licentie-inbegrepen prijzen. Gebruik uw SQL Server Standard- of Enterprise Edition-licenties met actieve Software Assurance om tot 55% te besparen ten opzichte van licentiekosten met het [Azure Hybrid Benefit voor SQL Server.](https://azure.microsoft.com/pricing/hybrid-benefit/) U het hybride voordeel combineren met [de gereserveerde capaciteitsprijzen](sql-database-reserved-capacity.md) van Azure SQL Database en tot 80% besparen wanneer u vooraf vastlegt aan een termijn van één of drie jaar.  Activeer beide voordelen vandaag nog van Azure portal.

Neem contact op met [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)als u vragen of zorgen hebt over deze wijziging of als u migratiehulp nodig hebt.

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migratie van een Premium RS-servicelaag naar een servicelaag in het DTU- of vCore-model

### <a name="migration-of-a-database"></a>Migratie van een database

Het migreren van een database van een Premium RS-servicelaag naar een servicelaag in het DTU- of het vCore-model is vergelijkbaar met het upgraden of verlagen tussen servicelagen in de Premium RS-servicelaag.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Databasekopiëren gebruiken om een Premium RS-database om te zetten naar een DTU- of vCore-database

U elke database met een Premium RS-rekengrootte kopiëren naar een database met een op DTU gebaseerde of vCore-gebaseerde rekengrootte zonder beperkingen of speciale sequencing, zolang de doelgrootte de maximale databasegrootte van de brondatabase ondersteunt. De databasekopie maakt een momentopname van gegevens vanaf de begintijd van de kopieerbewerking en voert geen gegevenssynchronisatie uit tussen de bron en het doel.

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL Database vCore-gebaseerde resourcelimieten voor afzonderlijke databases voor](sql-database-vcore-resource-limits-single-databases.md) meer informatie over specifieke rekenformaten en opties voor opslaggroottes die beschikbaar zijn voor één database.
- Zie [SQL Database vCore-gebaseerde resourcelimieten voor elastische pools voor](sql-database-vcore-resource-limits-elastic-pools.md)meer informatie over specifieke rekenformaten en opties voor opslaggroottes die beschikbaar zijn voor elastische pools.
