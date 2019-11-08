---
title: Buiten gebruik stellen Premium RS service tier
description: De servicelaag van Premium RS wordt buiten gebruik gesteld en de ondersteuning wordt beëindigd. Zie migratie opties.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821050"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Azure SQL Database Premium RS servicelaag (preview) wordt buiten gebruik gesteld-opties voor migratie

In februari 2018 heeft micro soft aangekondigd dat de Premium RS servicelaag in Azure SQL Database niet wordt vrijgegeven voor algemene Beschik baarheid en niet meer na 31 januari 2019 niet meer wordt ondersteund. Dit einde van de ondersteunings deadline is verlengd tot 30 juni 2019. In dit artikel worden de opties beschreven voor het migreren van de Premium RS servicelaag naar een andere servicelaag. Na 30 juni 2019 worden uw Premium RS-data bases automatisch naar een algemeen beschik bare servicelaag gemigreerd die het meest overeenkomt met de prestatie vereisten van uw Premium RS-data base.

Hieronder vindt u de migratie doelen en de prijs opties die mogelijk geschikt zijn voor Premium RS klanten:

- vCore-service lagen

  De service lagen **Algemeen** en **bedrijfskritiek** in het [op vCore gebaseerde aankoop model](sql-database-service-tiers-vcore.md). Deze twee service lagen bevinden zich in de algemene Beschik baarheid. Het vCore-gebaseerde aankoop model biedt ook de **grootschalige** -servicelaag die op aanvraag aan de behoeften van uw werk belasting toepast met automatisch schalen tot 100 TB per data base. De grootschalige-service laag biedt i/o-prestaties die vergelijkbaar zijn met de Premium-servicelaag in het op [DTU gebaseerde aankoop model](sql-database-service-tiers-dtu.md) tegen een prijs dichter bij de Premium RS servicelaag.
- Prijsopties voor Dev/Test

  [Prijzen voor ontwikkelen en testen](https://azure.microsoft.com/pricing/dev-test/) bieden een besparing van maxi maal 55% ten opzichte van de licentie kosten inbegrepen bij uw Visual Studio-abonnement.
- Prijs Azure Hybrid Benefit en gereserveerde capaciteit

  [Prijzen voor Azure Hybrid Benefit en gereserveerde capaciteit](https://azure.microsoft.com/pricing/details/sql-database/) bieden een besparing van maxi maal 80% tegenover licentie tarieven. Zie [Azure Hybrid Benefit voor SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) en [Azure SQL database gereserveerde capaciteit](sql-database-reserved-capacity.md)voor meer informatie over deze opties.

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>Nu handelen om uw Premium RS-data bases te migreren naar alternatieve SQL Database-Service lagen

Lees de richt lijnen in dit artikel samen met onze prijzen en documentatie om de juiste migratie bestemming (en) voor uw Premium RS workloads te bepalen.

## <a name="migrate-compute-intensive-workloads-and-save"></a>Migreer computerintensieve werk belastingen en bespaar

Voor uw computerintensieve Premium RS werk belastingen wordt u aangeraden naar onze algemeen beschik bare vCore Algemeen service tier te migreren en meer versus licentie-inbegrepen tarieven op te slaan met behulp van de Azure Hybrid Benefit voor SQL Server en gereserveerde capaciteits aanbiedingen. Als u liever op een op DTU gebaseerde aankoop optie zou blijven, kunt u uw computerintensieve Premium RS data bases migreren naar een Standard-servicelaag en nog steeds besparen met de Premium RS algemene beschikbaarheids prijzen (als de algemene Beschik baarheid is verstreken).

> [!WARNING]
> Het migreren van uw Premium RS-workloads naar DTU-gebaseerde Premium-Service lagen kan de maandelijkse kosten voor de huidige Premium RS prijzen verhogen. We raden aan om de grootschalige-of Bedrijfskritiek-lagen met Azure Hybrid Benefit en gereserveerde capaciteits prijzen te overwegen om Vergelijk bare of lagere kosten te onderhouden dan Premium RS.

### <a name="premium-rs-databases"></a>Premium RS-data bases

|**Als u zich momenteel bevindt...**|**Migreren naar vergelijk bare vCore...**|**Migreren naar vergelijk bare DTU-gebaseerd...**|
|---|---|---|
|Premium RS 1|Algemeen 1 vCore (Gen4)|Standaard 3|
|Premium RS 2|Algemeen 2 vCores (Gen4)|Standaard 4|
|Premium RS 4|Algemeen 4 vCores (Gen4)|Standard 6|
|Premium RS 6|Algemeen 6 vCores (Gen4)|Standaard 7|

### <a name="premium-rs-pools"></a>Premium RS groepen

|**Als u zich momenteel bevindt...**|**Migreren naar vergelijk bare vCore...**|**Migreren naar vergelijk bare DTU-gebaseerd...**|
|---|---|---|
|Premium RS-groep 125 DTU|Algemeen 1 vCore (Gen4)|Standard-groep 100 Edtu's|
|Premium RS-groep 250 DTU|Algemeen 2 vCores (Gen4)|Standard-groep 250 Edtu's|
|Premium RS-groep 500 DTU|Algemeen 4 vCores (Gen4)|Standard-groep 500 Edtu's|
|Premium RS-groep 1000 DTU|Algemeen 8 vCores (Gen4)|Standard-groep 1000 Edtu's|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>Optimaliseer de besparingen en prestaties voor uw IO-intensieve workloads

We raden u aan om uw i/o-intensieve afzonderlijke data bases te migreren naar onze op vCore gebaseerde grootschalige-laag, momenteel als preview-versie en uw i/o-intensieve database groepen naar onze algemeen beschik bare Bedrijfskritiek-laag, voor de optimale combi natie van prestaties en kosten.  Met de volgende vCore-opties kunt u uw huidige prestaties behouden of verbeteren en bespaart u geld in combi natie met de prijzen voor de Azure Hybrid Benefit en de gereserveerde capaciteit.

|**Als u zich momenteel bevindt...**|**Migreren naar vergelijk bare vCore...**|**Migreren naar vergelijk bare DTU-gebaseerd...**|
|---|---|---|
|Premium RS 1| Grootschalige 1 vCore (Gen4) of Bedrijfskritiek 1 vCore (Gen4)|Premium 1|
|Premium RS 2| Grootschalige 2 vCores (Gen4) of Bedrijfskritiek 2 vCores (Gen4|Premium 2|
|Premium RS 4| Grootschalige 4 vCores (Gen4) of Bedrijfskritiek 4 vCores (Gen4)|Premium 4
|Premium RS 6| Grootschalige 6 vCores (Gen4) of Bedrijfskritiek 6 vCores (Gen4)|Premium 6|

|**Als u zich momenteel bevindt...**|**Migreren naar vergelijk bare vCore...**|**Migreren naar vergelijk bare DTU-gebaseerd...**|
|---|---|---|
|Premium RS-groep 125 DTU|Bedrijfskritiek 2 vCores (Gen4)|Premium pool 125 Edtu's|
|Premium RS-groep 250 DTU|Bedrijfskritiek 2 vCores (Gen4)|Premium pool 250 Edtu's|
|Premium RS-groep 500 DTU|Bedrijfskritiek 4 vCores (Gen4)|Premium pool 500 Edtu's|
|Premium RS-groep 1000 DTU|Bedrijfskritiek 8 vCores (Gen4)|Premium pool 1000 Edtu's|

## <a name="take-advantage-of-our-new-offers"></a>Profiteer van onze nieuwe aanbiedingen

Onze service lagen in het op vCore gebaseerde aankoop model komen in aanmerking voor speciale aanbiedingen waarmee u Maxi maal 80% in vergelijking met licentie prijzen kunt besparen. Gebruik uw SQL Server Standard-of ENTER prise Edition-licenties met actieve Software Assurance om Maxi maal 55% te besparen op prijzen inclusief licenties met de [Azure Hybrid Benefit voor SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). U kunt het hybride voor deel combi neren met [Azure SQL database gereserveerde capaciteits](sql-database-reserved-capacity.md) prijs en maxi maal 80% besparen wanneer u een voor loop van een of drie jaar voorlegt.  Activeer beide voor delen vandaag van Azure Portal.

Neem contact op met [micro soft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)als u vragen of problemen hebt met betrekking tot deze wijziging of als u hulp nodig hebt bij de migratie.

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>Migratie van een Premium RS servicelaag naar een servicelaag in de DTU of het vCore-model

### <a name="migration-of-a-database"></a>Migratie van een Data Base

Het migreren van een Data Base vanuit een Premium RS servicelaag naar een servicelaag in de DTU of het vCore-model is vergelijkbaar met het bijwerken of Down graden tussen service lagen in de servicelaag Premium RS.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>Database kopie gebruiken om een Premium RS-data base te converteren naar een op DTU of vCore gebaseerde data base

U kunt elke Data Base met een Premium RS reken grootte kopiëren naar een Data Base met een DTU-of op vCore gebaseerde reken grootte zonder beperkingen of speciale sequentiëren, zolang de doel berekenings grootte de maximale database grootte van de bron database ondersteunt. De database kopie maakt een moment opname van gegevens vanaf de begin tijd van de Kopieer bewerking en voert geen gegevens synchronisatie uit tussen de bron en het doel.

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL database resource limieten op basis van vCore voor afzonderlijke](sql-database-vcore-resource-limits-single-databases.md) data bases voor meer informatie over de beschik bare reken grootten en opties voor opslag grootte die beschikbaar zijn
- Zie [SQL database op vCore gebaseerde resource limieten voor elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md)voor meer informatie over de beschik bare reken grootten en opties voor opslag grootte die beschikbaar zijn voor elastische Pools.
