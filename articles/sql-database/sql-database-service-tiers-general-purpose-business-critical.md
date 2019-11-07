---
title: 'Azure SQL Database-algemeen gebruik en bedrijfs kritiek '
description: In het artikel worden de algemene doel stellingen en bedrijfskritische service lagen in het op vCore gebaseerde inkoop model besproken.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 10/01/2019
ms.openlocfilehash: c6af083c4b3d16fa695da0ccc968c90b5480b6c4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687265"
---
# <a name="azure-sql-database-service-tiers"></a>Service lagen Azure SQL Database

Azure SQL Database is gebaseerd op SQL Server data base engine-architectuur die is aangepast voor de cloud omgeving om 99,99 procent Beschik baarheid te garanderen, zelfs als er een infrastructuur fout is opgetreden. Er worden drie service lagen gebruikt in Azure SQL Database, elk met een ander architectuur model. Deze service lagen zijn:

- [Algemeen gebruik](sql-database-service-tier-general-purpose.md), dat is ontworpen voor budget gerichte workloads.
- [Grootschalige](sql-database-service-tier-hyperscale.md), dat is ontworpen voor de meeste bedrijfs werkbelastingen, met uiterst schaal bare opslag, lees mogelijkheden voor uitschalen en snelle database herstel.
- [Bedrijfs kritiek](sql-database-service-tier-business-critical.md), die is ontworpen voor workloads met lage latentie met hoge tolerantie voor fouten en snelle failovers.

In dit artikel worden de verschillen tussen de service lagen en de opslag-en back-upfuncties voor de algemene doel stellingen en bedrijfskritische service lagen in het op vCore gebaseerde inkoop model beschreven.

## <a name="service-tier-comparison"></a>Service tier-vergelijking

In de volgende tabel worden de belangrijkste verschillen tussen service lagen voor de nieuwste generatie (GEN5) beschreven. Houd er rekening mee dat de kenmerken van de servicelaag kunnen verschillen in Individuele database en een beheerd exemplaar.

| | Resourcetype | Algemeen gebruik |  Hyperscale | Bedrijfskritiek |
|:---:|:---:|:---:|:---:|:---:|
| **Geschikt voor** | |  Biedt berekenings-en opslag opties voor budget gericht evenwicht. | De meeste zakelijke workloads. Opslag grootte automatisch schalen tot 100 TB, vloeiend verticaal en horizon taal berekenen, snel terugzetten van de data base. | OLTP-toepassingen met hoge transactie snelheden en lage IO-latentie. Biedt de hoogste flexibiliteit voor fouten en snelle failovers met behulp van meerdere synchroon bijgewerkte replica's.|
|  **Beschikbaar in resource type:** ||Eén data base/elastische pool/beheerd exemplaar | Individuele database | Eén data base/elastische pool/beheerd exemplaar |
| **Reken grootte**|Eén data base/elastische pool | 1 tot 80 vCores | 1 tot 80 vCores | 1 tot 80 vCores |
| | Beheerd exemplaar | 4, 8, 16, 24, 32, 40, 64, 80 vCores | N.v.t. | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| | Beheerde exemplaar groepen | 2, 4, 8, 16, 24, 32, 40, 64, 80 vCores | N.v.t. | N.v.t. |
| **Opslagtype** | Alle | Premium externe opslag (per instantie) | Niet-gekoppelde opslag met lokale SSD-cache (per instantie) | Super snelle lokale SSD-opslag (per instantie) |
| **Database grootte** | Eén data base/elastische pool | 5 GB – 4 TB | Tot 100 TB | 5 GB – 4 TB |
| | Beheerd exemplaar  | 32 GB – 8 TB | N.v.t. | 32 GB – 4 TB |
| **Opslag grootte** | Eén data base/elastische pool | 5 GB – 4 TB | Tot 100 TB | 5 GB – 4 TB |
| | Beheerd exemplaar  | 32 GB – 8 TB | N.v.t. | 32 GB – 4 TB |
| **TempDB-grootte** | Eén data base/elastische pool | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB per vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Beheerd exemplaar  | [24 GB per vCore](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N.v.t. | Tot 4 TB, [beperkt door de opslag grootte](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Schrijf doorvoer vastleggen in logboek** | Individuele database | [1,875 MB/s per vCore (Maxi maal 30 MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s per vCore (max. 96 MB/s)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Beheerd exemplaar | [3 MB/s per vCore (Maxi maal 22 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N.v.t. | [4 MB/s per VCore (max. 48 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Beschikbaarheid**|Alle| 99,99% |  [99,95% met één secundaire replica, 99,99% met meer replica's](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99% <br/> [99,995% met zone redundante single data base](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Back-ups**|Alle|RA-GRS, 7-35 dagen (standaard 7 dagen)| RA-GRS, 7 dagen, constant tijdstip herstel (PITR) | RA-GRS, 7-35 dagen (standaard 7 dagen) |
|**OLTP in het geheugen** | | N.v.t. | N.v.t. | Beschikbaar |
|**Alleen-lezen replica's**| | 0  | 0 - 4 | 1 (ingebouwd, inbegrepen in de prijs) |
|**Prijzen/facturering** | Individuele database | [vCore, gereserveerde opslag en back-upopslag](https://azure.microsoft.com/pricing/details/sql-database/single/) worden in rekening gebracht. <br/>Voor IOPS worden geen kosten in rekening gebracht. | [vCore voor elke replica en gebruikte opslag](https://azure.microsoft.com/pricing/details/sql-database/single/) worden in rekening gebracht. <br/>IOPS nog niet gefactureerd. | [vCore, gereserveerde opslag en back-upopslag](https://azure.microsoft.com/pricing/details/sql-database/single/) worden in rekening gebracht. <br/>Voor IOPS worden geen kosten in rekening gebracht. |
|| Beheerd exemplaar | [vCore en gereserveerde opslag](https://azure.microsoft.com/pricing/details/sql-database/managed/) worden in rekening gebracht. <br/>Voor IOPS worden geen kosten in rekening gebracht.<br/>Er wordt nog geen back-upopslag in rekening gebracht. | N.v.t. | [vCore en gereserveerde opslag](https://azure.microsoft.com/pricing/details/sql-database/managed/) worden in rekening gebracht. <br/>Voor IOPS worden geen kosten in rekening gebracht.<br/>Er wordt nog geen back-upopslag in rekening gebracht. | 
|**Kortings modellen**| | [Gereserveerde instanties](sql-database-reserved-capacity.md)<br/>[Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (niet beschikbaar in dev/test-abonnementen)<br/>Ontwikkel-en test abonnementen voor [ondernemingen](https://azure.microsoft.com/offers/ms-azr-0148p/) en [betalen naar gebruik](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (niet beschikbaar in dev/test-abonnementen)<br/>Ontwikkel-en test abonnementen voor [ondernemingen](https://azure.microsoft.com/offers/ms-azr-0148p/) en [betalen naar gebruik](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Gereserveerde instanties](sql-database-reserved-capacity.md)<br/>[Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (niet beschikbaar in dev/test-abonnementen)<br/>Ontwikkel-en test abonnementen voor [ondernemingen](https://azure.microsoft.com/offers/ms-azr-0148p/) en [betalen naar gebruik](https://azure.microsoft.com/offers/ms-azr-0023p/)|

Zie voor meer informatie de gedetailleerde verschillen tussen de service lagen in [één data base (vCore)](sql-database-vcore-resource-limits-single-databases.md), [Single Data Base Pools (vCore)](sql-database-dtu-resource-limits-single-databases.md), [Single Data Base (DTU)](sql-database-dtu-resource-limits-single-databases.md), [Single Data Base Pools (DTU)](sql-database-dtu-resource-limits-single-databases.md)en een [beheerd exemplaar](sql-database-managed-instance-resource-limits.md) pagina's.

> [!NOTE]
> Zie [grootschalige service tier](sql-database-service-tier-hyperscale.md)(grootschalige) voor meer informatie over de servicelaag in het op vCore gebaseerde aankoop model. Zie [Azure SQL database-inkoop modellen en-resources](sql-database-purchase-models.md)voor een vergelijking van het op vCore gebaseerde aankoop model met het DTU-gebaseerde aankoop model.

## <a name="data-and-log-storage"></a>Gegevens-en logboek opslag

De volgende factoren zijn van invloed op de hoeveelheid opslag die wordt gebruikt voor gegevens-en logboek bestanden en geldt voor Algemeen en Bedrijfskritiek. Zie [grootschalige-servicelaag](sql-database-service-tier-hyperscale.md)voor meer informatie over gegevens en logboek opslag in grootschalige.

- De toegewezen opslag wordt gebruikt door gegevens bestanden (MDF) en logboek bestanden (LDF).
- Elke reken grootte van één data base ondersteunt een maximale database grootte, met een standaard maximale grootte van 32 GB.
- Wanneer u de vereiste grootte voor één data base (de grootte van het MDF-bestand) configureert, wordt er automatisch 30 procent extra opslag toegevoegd ter ondersteuning van LDF-bestanden.
- De opslag grootte voor een SQL Database beheerd exemplaar moet worden opgegeven in veelvouden van 32 GB.
- U kunt de grootte van één data base tussen 10 GB en het ondersteunde maximum selecteren.
  - Verg root of verklein de grootte in stappen van 10 GB voor opslag in de service lagen standaard of algemeen.
  - Verg root of verklein de grootte in stappen van 250 GB voor opslag in de essentiële of bedrijfs kritieke service lagen.
- In de servicelaag voor algemeen gebruik `tempdb` een bijgevoegde SSD gebruikt, en deze opslag kosten worden opgenomen in de vCore prijs.
- In de laag bedrijfskritische service-`tempdb` deelt de gekoppelde SSD met de MDF-en LDF-bestanden en de `tempdb` opslag kosten worden opgenomen in de vCore-prijs.

> [!IMPORTANT]
> Er worden kosten in rekening gebracht voor de totale opslag ruimte die is toegewezen voor MDF-en LDF-bestanden.

Gebruik [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql)om de huidige totale grootte van uw MDF-en LDF-bestanden te controleren. Gebruik [sys. database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql)om de huidige grootte van de afzonderlijke MDF-en LDF-bestanden te bewaken.

> [!IMPORTANT]
> In sommige gevallen moet u mogelijk een Data Base verkleinen om ongebruikte ruimte te claimen. Zie [Bestands ruimte beheren in Azure SQL database](sql-database-file-space-management.md)voor meer informatie.

## <a name="backups-and-storage"></a>Back-ups en opslag

Opslag voor back-ups van data bases wordt toegewezen ter ondersteuning van de mogelijkheden voor PITR (Point-in-time Restore) en [v.l.n.r. (Long-term retention)](sql-database-long-term-retention.md) van SQL database. Deze opslag wordt afzonderlijk voor elke Data Base toegewezen en gefactureerd als twee afzonderlijke kosten per data base.

- **PITR**: afzonderlijke database back-ups worden automatisch naar [geografisch redundante opslag met lees toegang (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md) gekopieerd. De opslag grootte neemt dynamisch toe wanneer er nieuwe back-ups worden gemaakt. De opslag wordt gebruikt door wekelijkse volledige back-ups, dagelijkse differentiële back-ups en back-ups van transactie logboeken, die elke vijf minuten worden gekopieerd. Het opslag verbruik is afhankelijk van de frequentie waarmee de data base wordt gewijzigd en de retentie periode voor back-ups. U kunt tussen 7 en 35 dagen een afzonderlijke Bewaar periode configureren voor elke Data Base. Er wordt geen extra kosten in rekening gebracht voor een minimale opslag hoeveelheid die gelijk is aan 100 procent (1x) van de grootte van de data base. Voor de meeste data bases is deze hoeveelheid voldoende om zeven dagen back-ups op te slaan.
- **LTR**: SQL database biedt u de mogelijkheid om lange termijn retentie van volledige back-ups te configureren gedurende Maxi maal tien jaar. Als u een LTR-beleid instelt, worden deze back-ups automatisch opgeslagen in RA-GRS-opslag, maar u kunt bepalen hoe vaak de back-ups worden gekopieerd. Als u wilt voldoen aan verschillende nalevings vereisten, kunt u verschillende Bewaar perioden selecteren voor wekelijkse, maandelijkse en/of jaarlijkse back-ups. De configuratie die u kiest, bepaalt hoeveel opslag ruimte wordt gebruikt voor V.L.N.R.-back-ups. U kunt de LTR-prijs calculator gebruiken om de kosten van V.L.N.R.-opslag te schatten. Zie [SQL database lange termijn retentie](sql-database-long-term-retention.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL database resource limieten op basis van vCore voor afzonderlijke data bases](sql-database-vcore-resource-limits-single-databases.md)voor meer informatie over de specifieke berekenings grootten en opslag grootten die beschikbaar zijn voor één data base in de laag algemeen en bedrijfskritische service lagen.
- Zie [SQL database resource limieten op basis van vCore voor elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md)voor meer informatie over de specifieke berekenings grootten en opslag grootten die beschikbaar zijn voor elastische Pools in de categorie Algemeen en bedrijfskritische service.
