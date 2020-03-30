---
title: Algemeen doel en bedrijfskritisch
description: Het artikel bespreekt de algemene doel- en bedrijfskritieke servicelagen in het op vCore gebaseerde inkoopmodel.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 01/30/2020
ms.openlocfilehash: 09cc9e1475616700aa77cdf92fd7ca808cd4290c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937840"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database-servicelagen

Azure SQL Database is gebaseerd op SQL Server-databaseenginearchitectuur die is aangepast voor de cloudomgeving om 99,99 procent beschikbaarheid te garanderen, zelfs als er een infrastructuurfout optreedt. Er worden drie servicelagen gebruikt in Azure SQL Database, elk met een ander architectuurmodel. Deze servicelagen zijn:

- [Algemeen doel](sql-database-service-tier-general-purpose.md), dat is ontworpen voor budgetgerichte workloads.
- [Hyperscale](sql-database-service-tier-hyperscale.md), dat is ontworpen voor de meeste zakelijke workloads, biedt zeer schaalbare opslag, leesscale-out en snelle databaseherstelmogelijkheden.
- [Bedrijfskritisch](sql-database-service-tier-business-critical.md),die is ontworpen voor workloads met lage latentie met een hoge tolerantie voor storingen en snelle failovers.

In dit artikel worden verschillen besproken in de serviceniveaus, opslag- en back-upoverwegingen voor algemene doeleinden en bedrijfskritieke servicelagen in het op vCore gebaseerde inkoopmodel.

## <a name="service-tier-comparison"></a>Vergelijking van servicelagen

In de volgende tabel worden de belangrijkste verschillen tussen servicelagen voor de nieuwste generatie (Gen5) beschreven. Houd er rekening mee dat de kenmerken van de servicelaag kunnen verschillen in Één database en beheerde instantie.

| | Resourcetype | Algemeen gebruik |  Hyperscale | Bedrijfskritisch |
|:---:|:---:|:---:|:---:|:---:|
| **Het beste voor** | |  Biedt budgetgeoriënteerde gebalanceerde reken- en opslagopties. | De meeste zakelijke workloads. Automatisch schalen van de opslaggrootte tot 100 TB, vloeiende verticale en horizontale compute scaling, snelle database herstel. | OLTP-toepassingen met een hoge transactiesnelheid en lage IO-latentie. Biedt de hoogste veerkracht voor storingen en snelle failovers met meerdere synchroon bijgewerkte replica's.|
|  **Beschikbaar in resourcetype:** ||Enkele database / elastische pool / beheerde instantie | Individuele database | Enkele database / elastische pool / beheerde instantie |
| **Rekengrootte**|Enkele database / elastische pool | 1 tot 80 vCores | 1 tot 80 vCores | 1 tot 80 vCores |
| | Beheerd exemplaar | 4, 8, 16, 24, 32, 40, 64, 80 vCores | N.v.t. | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| | Beheerde instantiegroepen | 2, 4, 8, 16, 24, 32, 40, 64, 80 vCores | N.v.t. | N.v.t. |
| **Opslagtype** | Alle | Premium externe opslag (per exemplaar) | Gekoppelde opslag met lokale SSD-cache (per instantie) | Supersnelle lokale SSD-opslag (per exemplaar) |
| **Databasegrootte** | Enkele database / elastische pool | 5 GB – 4 TB | Tot 100 TB | 5 GB – 4 TB |
| | Beheerd exemplaar  | 32 GB – 8 TB | N.v.t. | 32 GB – 4 TB |
| **Opslaggrootte** | Enkele database / elastische pool | 5 GB – 4 TB | Tot 100 TB | 5 GB – 4 TB |
| | Beheerd exemplaar  | 32 GB – 8 TB | N.v.t. | 32 GB – 4 TB |
| **TempDB grootte** | Enkele database / elastische pool | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Beheerd exemplaar  | [24 GB per vCore](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N.v.t. | Tot 4 TB - [beperkt door de opslagcapaciteit](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Doorvoer van logboekschrijftekst** | Individuele database | [1,875 MB/s per vCore (max. 30 MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s per vCore (max.](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Beheerd exemplaar | [3 MB/s per vCore (max.](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N.v.t. | [4 MB/s per vcore (max. 48 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Beschikbaarheid**|Alle| 99,99% |  [99,95% met één secundaire replica, 99,99% met meer replica's](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99% <br/> [99,995% met zone redundante enkele database](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Back-ups**|Alle|RA-GRS, 7-35 dagen (standaard 7 dagen)| RA-GRS, 7 dagen, constant time point-in-time recovery (PITR) | RA-GRS, 7-35 dagen (standaard 7 dagen) |
|**In-memory OLTP** | | N.v.t. | N.v.t. | Beschikbaar |
|**Alleen-lezen replica's**| | 0 ingebouwd <br> 0 - 4 met [geo-replicatie](sql-database-active-geo-replication.md) | 0 - 4 ingebouwde | 1 ingebouwd, inbegrepen in prijs <br> 0 - 4 met [geo-replicatie](sql-database-active-geo-replication.md) |
|**Prijzen/facturering** | Individuele database | [vCore, gereserveerde opslag en back-upopslag](https://azure.microsoft.com/pricing/details/sql-database/single/) worden in rekening gebracht. <br/>IOPS wordt niet in rekening gebracht. | [vCore voor elke replica en gebruikte opslag](https://azure.microsoft.com/pricing/details/sql-database/single/) worden in rekening gebracht. <br/>IOPS nog niet in rekening gebracht. | [vCore, gereserveerde opslag en back-upopslag](https://azure.microsoft.com/pricing/details/sql-database/single/) worden in rekening gebracht. <br/>IOPS wordt niet in rekening gebracht. |
|| Beheerd exemplaar | [vCore, gereserveerde opslag en back-upopslag](https://azure.microsoft.com/pricing/details/sql-database/managed/) worden opgeladen. <br/>IOPS wordt niet in rekening gebracht| N.v.t. | [vCore, gereserveerde opslag en back-upopslag](https://azure.microsoft.com/pricing/details/sql-database/managed/) worden opgeladen. <br/>IOPS wordt niet in rekening gebracht.| 
|**Kortingsmodellen**| | [Gereserveerde instanties](sql-database-reserved-capacity.md)<br/>[Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (niet beschikbaar op dev/testabonnementen)<br/>[Enterprise-](https://azure.microsoft.com/offers/ms-azr-0148p/) en [Pay-As-You-Go-abonnementen](https://azure.microsoft.com/offers/ms-azr-0023p/) voor dev/Test| [Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (niet beschikbaar op dev/testabonnementen)<br/>[Enterprise-](https://azure.microsoft.com/offers/ms-azr-0148p/) en [Pay-As-You-Go-abonnementen](https://azure.microsoft.com/offers/ms-azr-0023p/) voor dev/Test| [Gereserveerde instanties](sql-database-reserved-capacity.md)<br/>[Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (niet beschikbaar op dev/testabonnementen)<br/>[Enterprise-](https://azure.microsoft.com/offers/ms-azr-0148p/) en [Pay-As-You-Go-abonnementen](https://azure.microsoft.com/offers/ms-azr-0023p/) voor dev/Test|

Zie voor meer informatie de gedetailleerde verschillen tussen de servicelagen in [Single database (vCore),](sql-database-vcore-resource-limits-single-databases.md) [Single database pools (vCore),](sql-database-dtu-resource-limits-single-databases.md) [Single database (DTU),](sql-database-dtu-resource-limits-single-databases.md) [Single database pools (DTU)](sql-database-dtu-resource-limits-single-databases.md)en [Managed Instance](sql-database-managed-instance-resource-limits.md) pages.

> [!NOTE]
> Zie [hyperscale service tier voor](sql-database-service-tier-hyperscale.md)informatie over de hyperscale service-laag in het op vCore gebaseerde inkoopmodel. Zie [Azure SQL Database-inkoopmodellen en -bronnen](sql-database-purchase-models.md)voor een vergelijking van het op vCore gebaseerde inkoopmodel met het op DTU gebaseerde inkoopmodel.

## <a name="data-and-log-storage"></a>Gegevens- en logboekopslag

De volgende factoren zijn van invloed op de hoeveelheid opslagruimte die wordt gebruikt voor gegevens en logboekbestanden en zijn van toepassing op Algemeen Doel en Bedrijfskritiek. Zie [Hyperscale-servicelaag](sql-database-service-tier-hyperscale.md)voor meer informatie over gegevens en logboekopslag in Hyperscale.

- De toegewezen opslag wordt gebruikt door gegevensbestanden (MDF) en logbestanden (LDF).
- Elke rekengrootte van elke database ondersteunt een maximale databasegrootte, met een standaard maximale grootte van 32 GB.
- Wanneer u de vereiste enkele databasegrootte (de grootte van het MDF-bestand) configureert, wordt automatisch 30 procent meer extra opslagruimte toegevoegd om LDF-bestanden te ondersteunen.
- De opslaggrootte voor een SQL Database-beheerde instantie moet worden opgegeven in veelvouden van 32 GB.
- U elke databasegrootte tussen 10 GB en het ondersteunde maximum selecteren.
  - Voor opslag in de servicelagen voor standaard of algemeen gebruik u de grootte in stappen van 10 GB vergroten of verkleinen.
  - Voor opslag in de premium- of bedrijfskritieke servicelagen u de grootte in stappen van 250 GB vergroten of verkleinen.
- In de servicelaag `tempdb` voor algemeen gebruik gebruikt u een gekoppelde SSD en deze opslagkosten zijn inbegrepen in de vCore-prijs.
- In de bedrijfskritieke `tempdb` servicelaag deelt u de gekoppelde SSD met de `tempdb` MDF- en LDF-bestanden en zijn de opslagkosten inbegrepen in de vCore-prijs.

> [!IMPORTANT]
> Er worden kosten in rekening gebracht voor de totale opslag die is toegewezen aan MDF- en LDF-bestanden.

Als u de huidige totale grootte van uw MDF- en LDF-bestanden wilt controleren, gebruikt u [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Als u de huidige grootte van de afzonderlijke MDF- en LDF-bestanden wilt controleren, gebruikt u [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte terug te winnen. Zie [Bestandsruimte beheren in Azure SQL Database](sql-database-file-space-management.md)voor meer informatie.

## <a name="backups-and-storage"></a>Back-ups en opslag

Opslag voor databaseback-ups wordt toegewezen ter ondersteuning van de Point-in-time restore (PITR) en [lange termijn retentie (LTR)](sql-database-long-term-retention.md) mogelijkheden van SQL Database. Deze opslag wordt afzonderlijk toegewezen voor elke database en gefactureerd als twee afzonderlijke kosten per database.

- **PITR**: Individuele databaseback-ups worden automatisch gekopieerd naar [georedundante opslag (READ-Access) (RA-GRS).](../storage/common/storage-designing-ha-apps-with-ragrs.md) De opslaggrootte neemt dynamisch toe naarmate er nieuwe back-ups worden gemaakt. De opslag wordt gebruikt door wekelijkse volledige back-ups, dagelijkse differentiële back-ups en transactielogboekback-ups, die elke 5 minuten worden gekopieerd. Het opslagverbruik is afhankelijk van de mate van wijziging van de database en de bewaarperiode voor back-ups. U voor elke database tussen 7 en 35 dagen een afzonderlijke bewaartermijn configureren. Een minimale opslaghoeveelheid gelijk aan 100 procent (1x) van de databasegrootte wordt zonder extra kosten verstrekt. Voor de meeste databases is dit bedrag voldoende om 7 dagen back-ups op te slaan.
- **LTR**: SQL Database biedt u de mogelijkheid om volledige back-ups op lange termijn te configureren voor maximaal 10 jaar. Als u een LTR-beleid instelt, worden deze back-ups automatisch opgeslagen in RA-GRS-opslag, maar u bepalen hoe vaak de back-ups worden gekopieerd. Als u aan verschillende nalevingsvereisten wilt voldoen, u verschillende bewaartermijnen selecteren voor wekelijkse, maandelijkse en/of jaarlijkse back-ups. De configuratie die u kiest, bepaalt hoeveel opslagruimte wordt gebruikt voor LTR-back-ups. Als u de kosten van LTR-opslag wilt schatten, u de LTR-prijscalculator gebruiken. Zie [SQL Database langetermijnretentie](sql-database-long-term-retention.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL Database vCore-gebaseerde resourcelimieten voor afzonderlijke databases](sql-database-vcore-resource-limits-single-databases.md)voor meer informatie over de specifieke rekengroottes en opslagformaten die beschikbaar zijn voor één database in de algemene doel- en bedrijfskritieke servicelagen.
- Zie [SQL Database vCore-gebaseerde resourcelimieten voor elastische pools voor](sql-database-vcore-resource-limits-elastic-pools.md)meer informatie over de specifieke rekengroottes en opslaggroottes die beschikbaar zijn voor elastische pools in het algemeen doel en bedrijfskritieke servicelagen.
