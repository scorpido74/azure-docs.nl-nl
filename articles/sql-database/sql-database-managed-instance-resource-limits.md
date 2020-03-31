---
title: Resourcelimieten - beheerde instantie
description: In dit artikel vindt u een overzicht van de azure SQL Database-bronlimieten voor beheerde exemplaren.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 02/25/2020
ms.openlocfilehash: b2871ec87e4d7f337c26b3ff3de83c1c3c88aea2
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365384"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Overzicht Azure SQL Database managed instance resource limits Overview Azure SQL Sql Database managed instance resource limits Overview Azure SQL SQL Database managed instance resource limits

In dit artikel vindt u een overzicht van de technische kenmerken en resourcelimieten voor azure SQL Database-beheerde instantie en vindt u informatie over het aanvragen van een verhoging van deze limieten.

> [!NOTE]
> Zie [Functieverschillen](sql-database-features.md) en [T-SQL-instructieondersteuning](sql-database-managed-instance-transact-sql-information.md)voor verschillen in ondersteunde functies en T-SQL-instructies . Zie [Service-laagvergelijking](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison)voor algemene verschillen tussen servicelagen in één database en beheerde instantie .

## <a name="hardware-generation-characteristics"></a>Hardwaregeneratiekenmerken

Beheerde instantie heeft kenmerken en resourcelimieten die afhankelijk zijn van de onderliggende infrastructuur en architectuur. De beheerde instantie van Azure SQL Database kan worden geïmplementeerd op twee hardwaregeneraties: Gen4 en Gen5. Hardwaregeneraties hebben verschillende kenmerken, zoals beschreven in de volgende tabel:

|   | **Gen4 Gen4** | **Gen5 Gen5** |
| --- | --- | --- |
| Hardware | Intel E5-2673 v3 (Haswell) 2,4-GHz processors, aangesloten SSD vCore = 1 PP (fysieke kern) | Intel E5-2673 v4 (Broadwell) 2,3-GHz en Intel SP-8160 (Skylake) processors, snelle NVMe SSD, vCore=1 LP (hyper-thread) |
| Aantal vCores | 8, 16, 24 vCores | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| Maximaal geheugen (geheugen/kernverhouding) | 7 GB per vCore<br/>Voeg meer vCores toe om meer geheugen te krijgen. | 5,1 GB per vCore<br/>Voeg meer vCores toe om meer geheugen te krijgen. |
| Max In-Memory OLTP-geheugen | Instantielimiet: 1-1,5 GB per vCore| Instantielimiet: 0,8 - 1,65 GB per vCore |
| Gereserveerde opslag max. |  Algemeen doel: 8 TB<br/>Bedrijfskritisch: 1 TB | Algemeen doel: 8 TB<br/> Bedrijfskritische 1 TB, 2 TB of 4 TB, afhankelijk van het aantal cores |

> [!IMPORTANT]
> - Gen4 hardware wordt uitgefaseerd en is niet meer beschikbaar voor de nieuwe implementaties. Alle nieuwe beheerde exemplaren moeten worden geïmplementeerd op Gen5-hardware.
> - Overweeg [uw beheerde exemplaren naar Gen 5-hardware te verplaatsen](sql-database-service-tiers-vcore.md) om een breder scala aan vCore- en opslagschaalbaarheid, versnelde netwerken, de beste IO-prestaties en minimale latentie te ervaren.

### <a name="in-memory-oltp-available-space"></a>Beschikbare ruimte in het geheugen OLTP 

De hoeveelheid OLTP-ruimte in het geheugen in de servicelaag [Business Critical](sql-database-service-tier-business-critical.md) is afhankelijk van het aantal vCores en de productie van hardware. In de volgende tabel worden geheugenlimieten weergegeven die kunnen worden gebruikt voor OLTP-objecten in het geheugen.

| In-memory OLTP-ruimte  | **Gen5 Gen5** | **Gen4 Gen4** |
| --- | --- | --- |
| 4 vCores  | 3,14 GB | |   
| 8 vCores  | 6,28 GB | 8 GB |
| 16 vCores | 15,77 GB | 20 GB |
| 24 vCores | 25,25 GB | 36 GB |
| 32 vCores | 37,94 GB | |
| 40 vCores | 52,23 GB | |
| 64 vCores | 99,9 GB    | |
| 80 vCores | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Kenmerken van servicelagen

Beheerde instantie heeft twee servicelagen: [Algemeen doel](sql-database-service-tier-general-purpose.md) en [bedrijfskritisch](sql-database-service-tier-business-critical.md). Deze lagen bieden [verschillende mogelijkheden,](sql-database-service-tiers-general-purpose-business-critical.md)zoals beschreven in de onderstaande tabel.

> [!Important]
> Business Critical service-tier biedt extra ingebouwde kopie van instantie (secundaire replica) die kan worden gebruikt voor alleen-lezen werkbelasting. Als u lees-schrijfquery's en lees-only/analytische/rapportagequery's scheiden, krijgt u twee keer vCores en geheugen voor dezelfde prijs. Secundaire replica kan enkele seconden achterblijven bij de primaire instantie, dus het is ontworpen om rapportage/ analytische werkbelasting te ontladen die geen exacte huidige status van gegevens nodig heeft. In de onderstaande tabel zijn **alleen-lezen query's** de query's die worden uitgevoerd op secundaire replica.In the table below, read-only queries are the queries that are executed on secondary replica.

| **Functie** | **Algemeen doel** | **Bedrijfskritisch** |
| --- | --- | --- |
| Aantal vCores\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*Hetzelfde aantal vCores is gewijd voor alleen-lezen query's. |
| Maximaal geheugen | Gen4: 56 GB - 168 GB (7 GB/vCore)<br/>Gen5: 20,4 GB - 408 GB (5,1 GB/vCore)<br/>Voeg meer vCores toe om meer geheugen te krijgen. | Gen4: 56 GB - 168 GB (7 GB/vCore)<br/>Gen5: 20,4 GB - 408 GB (5,1 GB/vCore) voor leesschrijfquery's<br/>+ extra 20,4 GB - 408 GB (5,1 GB/vCore) voor alleen-lezen query's.<br/>Voeg meer vCores toe om meer geheugen te krijgen. |
| Maximale opslaggrootte van bijvoorbeeld (gereserveerd) | - 2 TB voor 4 vCores (alleen Gen5)<br/>- 8 TB voor andere maten | Gen4: 1 TB <br/> Gen5: <br/>- 1 TB voor 4, 8, 16 vCores<br/>- 2 TB voor 24 vCores<br/>- 4 TB voor 32, 40, 64, 80 vCores |
| Maximale databasegrootte | Maximaal de huidige beschikbare instantiegrootte (max 2 TB - 8 TB, afhankelijk van het aantal vCores). | Maximaal de huidige beschikbare instantiegrootte (max 1 TB - 4 TB, afhankelijk van het aantal vCores). |
| Maximale tempDB grootte | Beperkt tot 24 GB/vCore (96 - 1.920 GB) en momenteel beschikbare instantieopslaggrootte.<br/>Voeg meer vCores toe om meer TempDB-ruimte te krijgen.<br/> De bestandsgrootte van het logboek is beperkt tot 120 GB.| Maximaal de huidige beschikbare opslaggrootte van bijvoorbeeld. |
| Maximum aantal databases per exemplaar | 100, tenzij de limiet voor de opslaggrootte van de instantie is bereikt. | 100, tenzij de limiet voor de opslaggrootte van de instantie is bereikt. |
| Maximum aantal databasebestanden per exemplaar | Maximaal 280, tenzij de limiet voor de opslaggrootte van de instantie of de [toewijzingsruimtelimiet voor Azure Premium Disk-opslag](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files) is bereikt. | 32.767 bestanden per database, tenzij de limiet voor de opslaggrootte van de instantie is bereikt. |
| Maximale grootte van gegevensbestanden | Beperkt tot de momenteel beschikbare opslaggrootte van bijvoorbeeld (max 2 TB - 8 TB) en [Azure Premium Disk-opslagtoewijzingsruimte](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files). | Beperkt tot de momenteel beschikbare instantie opslaggrootte (tot 1 TB - 4 TB). |
| Maximale bestandsgrootte van logboek | Beperkt tot 2 TB en momenteel beschikbare instantie opslaggrootte. | Beperkt tot 2 TB en momenteel beschikbare instantie opslaggrootte. |
| Gegevens/Log IOPS (bij benadering) | Maximaal 30-40 K IOPS per instantie*, 500 - 7500 per bestand<br/>\*[Bestandsgrootte vergroten om meer IOPS te krijgen](#file-io-characteristics-in-general-purpose-tier)| 10 K - 200 K (2500 IOPS/vCore)<br/>Voeg meer vCores toe om betere IO-prestaties te krijgen. |
| Doorvoerlimiet voor logboekschrijftekst (per instantie) | 3 MB/s per vCore<br/>Max. | 4 MB/s per vCore<br/>Max. |
| Gegevensdoorvoer (bij benadering) | 100 - 250 MB/s per bestand<br/>\*[Verhoog de bestandsgrootte om betere IO-prestaties te krijgen](#file-io-characteristics-in-general-purpose-tier) | Niet beperkt. |
| Storage IO latency (bij benadering) | 5-10 ms | 1-2 ms |
| In-memory OLTP | Niet ondersteund | Beschikbaar, [grootte is afhankelijk van het aantal vCore](#in-memory-oltp-available-space) |
| Max sessies | 30.000 | 30.000 |
| [Alleen-lezen replica's](sql-database-read-scale-out.md) | 0 | 1 (inbegrepen in prijs) |

> [!NOTE]
> - **De momenteel beschikbare instantieopslaggrootte** is het verschil tussen de gereserveerde instantiegrootte en de gebruikte opslagruimte.
> - Zowel gegevens als logboekbestandsgrootte in de gebruikers- en systeemdatabases zijn opgenomen in de opslaggrootte van de instantie die wordt vergeleken met de limiet voor de maximale opslaggrootte. Gebruik <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> systeemweergave om de totale gebruikte ruimte van databases te bepalen. Foutlogboeken worden niet gehandhaafd en niet opgenomen in de grootte. Back-ups zijn niet inbegrepen in de opslaggrootte.
> - Doorvoer en IOPS op de laag Algemeen doel zijn ook afhankelijk van de [bestandsgrootte](#file-io-characteristics-in-general-purpose-tier) die niet expliciet wordt beperkt door beheerde instantie.
> - U een andere leesbare replica maken in verschillende Azure-regio's met auto-failovergroepen.
> - Max instance IOPS is afhankelijk van de bestandsindeling en verdeling van de werkbelasting. Als u bijvoorbeeld 7 x 1 TB-bestanden maakt met maximaal 5K IOPS per stuk en 7 kleine bestanden (kleiner dan 128 GB) met elk 500 IOPS, u 38500 IOPS per exemplaar (7x5000 +7x500) krijgen als uw werkbelasting alle bestanden kan gebruiken. Houd er rekening mee dat een bepaalde hoeveelheid IOPS ook wordt gebruikt voor automatische back-ups.

> [!NOTE]
> Meer informatie over de [resourcelimieten in beheerde instantiegroepen vindt u in dit artikel](sql-database-instance-pools.md#instance-pools-resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Io-kenmerken bestand in de laag Algemeen doel

In de servicelaag van Algemeen gebruik krijgt elk databasebestand speciale IOPS en doorvoer die afhankelijk is van de bestandsgrootte. Grotere bestanden krijgen steeds meer IOPS en doorvoer. Io-kenmerken van de databasebestanden worden weergegeven in de volgende tabel:

| Bestandsgrootte | >=0 en <=128 GiB | >128 en <=256 GiB | >256 en <= 512 GiB | >0,5 en <=1 TiB    | >1 en <=2 TiB    | >2 en <=4 TiB | >4 en <=8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS per bestand       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12.500   |
| Doorvoer per bestand | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Als u een hoge IO-latentie op een databasebestand opmerkt of als u ziet dat IOPS/throughput de limiet bereikt, u de prestaties verbeteren door [de bestandsgrootte te vergroten.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337)

Er zijn ook limieten op instantieniveau, zoals de doorvoer van maximaal logboekschrijfdoorvoer van 22 MB/s, zodat u mogelijk het bestand in het hele logboekbestand niet bereiken omdat u de doorvoerlimiet voor bijvoorbeelden bereikt.

## <a name="supported-regions"></a>Ondersteunde regio’s

Beheerde exemplaren kunnen alleen worden gemaakt in [ondersteunde regio's.](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all) Als u een beheerde instantie wilt maken in een regio die momenteel niet wordt ondersteund, u [een ondersteuningsaanvraag verzenden via de Azure-portal.](quota-increase-request.md)

## <a name="supported-subscription-types"></a>Ondersteunde abonnementstypen

Beheerde instantie ondersteunt momenteel alleen implementatie op de volgende typen abonnementen:

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Cloud Service Provider (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Dev/Test met betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Abonnementen met maandelijks Azure-tegoed voor Visual Studio-abonnees](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Beperkingen van regionale middelen

Ondersteunde abonnementstypen kunnen een beperkt aantal bronnen per regio bevatten. Beheerde instantie heeft twee standaardlimieten per Azure-gebied (die on-demand kunnen worden verhoogd door een speciale ondersteuningsaanvraag te maken [in de Azure-portal,](quota-increase-request.md) afhankelijk van een type abonnementstype:

- **Subnetlimiet:** het maximum aantal subnetten waarbij beheerde exemplaren in één regio worden geïmplementeerd.
- **vCore-eenheidslimiet:** het maximum aantal vCore-eenheden dat kan worden geïmplementeerd in alle instanties in één regio. Eén GP vCore maakt gebruik van één vCore-eenheid en één BC vCore neemt 4 vCore-eenheden. Het totale aantal exemplaren is niet beperkt zolang deze binnen de vCore-eenheidslimiet valt.

> [!Note]
> Deze limieten zijn standaardinstellingen en geen technische beperkingen. De limieten kunnen on-demand worden verhoogd door een speciaal [ondersteuningsverzoek in de Azure-portal](quota-increase-request.md) te maken als u meer beheerde exemplaren in de huidige regio nodig hebt. Als alternatief u nieuwe beheerde exemplaren maken in een andere Azure-regio zonder ondersteuningsaanvragen te verzenden.

In de volgende tabel worden de **standaardregionale limieten** voor ondersteunde abonnementstypen weergegeven (standaardlimieten kunnen worden verlengd met behulp van onderstaande ondersteuningsaanvraag):

|Abonnementstype| Maximaal aantal beheerde instantiesubnetten | Maximum aantal vCore-eenheden* |
| :---| :--- | :--- |
|Pay-as-you-go|3|320|
|CSP |8 (15 in sommige regio's**)|960 (1440 in sommige regio's**)|
|Pay-as-you-go Dev/Test|3|320|
|Enterprise Dev/Test|3|320|
|Ea|8 (15 in sommige regio's**)|960 (1440 in sommige regio's**)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional- en MSDN-platforms|2|32|

\*Houd er bij het plannen van implementaties rekening mee dat de servicelaag Business Critical (BC) vier (4) keer meer vCore-capaciteit vereist dan de servicelaag (General Purpose) (GP). Bijvoorbeeld: 1 GP vCore = 1 vCore unit en 1 BC vCore = 4 vCore units. Als u uw verbruiksanalyse wilt vereenvoudigen aan de hand van de standaardlimieten, vat u de vCore-eenheden op alle subnetten in het gebied waar beheerde exemplaren worden geïmplementeerd en vergelijkt u de resultaten met de limiet voor de instantie-eenheid voor uw abonnementstype. Voor elk abonnement in een regio geldt een maximum **aantal vCore-eenheden.** Er is geen limiet per afzonderlijke subnetten, behalve dat de som van alle vCores die op meerdere subnetten worden geïmplementeerd, lager of gelijk moet zijn aan **het maximum aantal vCore-eenheden.**

\*\*Grotere subnet- en vCore-limieten zijn beschikbaar in de volgende regio's: Australië-Oost, Oost-VS, Oost-VS 2, Noord-Europa, Zuid-Centraal-VS, Zuidoost-Azië, Het Verenigd Koninkrijk Zuid, West-Europa, West-VS 2.

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>Een quotumverhoging aanvragen voor SQL-beheerde instantie

Als u meer beheerde exemplaren in uw huidige regio's nodig hebt, stuurt u een ondersteuningsaanvraag om het quotum uit te breiden met de Azure-portal. Zie [Quotaverhogingen voor Azure SQL Database aanvragen voor](quota-increase-request.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie Wat is een [beheerde instantie voor](sql-database-managed-instance.md)meer informatie over beheerde instantie.
- Zie [SQL Database managed instance pricing](https://azure.microsoft.com/pricing/details/sql-database/managed/)voor prijsinformatie voor prijsinformatie .
- Zie [de quickstart-handleiding](sql-database-managed-instance-get-started.md)voor meer informatie over het maken van uw eerste beheerde instantie.
