---
title: Azure SQL Database resource limieten-beheerd exemplaar | Microsoft Docs
description: Dit artikel bevat een overzicht van de Azure SQL Database resource limieten voor beheerde exemplaren.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 09/16/2019
ms.openlocfilehash: 7f7faf11ed18fa2a85587c193376a3e4ce905fd2
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010193"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Overzicht Azure SQL Database limieten voor beheerde exemplaar bronnen

Dit artikel bevat een overzicht van de technische kenmerken en resource limieten voor Azure SQL Database beheerde instantie en bevat informatie over het aanvragen van een verhoging van deze limieten.

> [!NOTE]
> Zie voor verschillen in ondersteunde functies en T-SQL-instructies [functie verschillen](sql-database-features.md) en [ondersteuning voor t-SQL-instructie](sql-database-managed-instance-transact-sql-information.md). Zie de [vergelijking](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison)van de servicelaag voor algemene verschillen tussen service lagen in één data base en een beheerd exemplaar.

## <a name="instance-level-resource-limits"></a>Resource limieten op exemplaar niveau

Het beheerde exemplaar heeft kenmerken en resource limieten die afhankelijk zijn van de onderliggende infra structuur en architectuur. Limieten zijn afhankelijk van de hardware-generatie en de servicelaag.

### <a name="hardware-generation-characteristics"></a>Kenmerken voor het genereren van hardware

Azure SQL Database Managed instance kan worden geïmplementeerd op twee hardware gegenereerde items: Gen4 en GEN5. Hardware-generaties hebben verschillende kenmerken, zoals wordt beschreven in de volgende tabel:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardware | Intel E5-2673 v3 (Haswell) 2,4-GHz-processors, Attached SSD vCore = 1 PP (fysieke kern) | Intel E5-2673 v4 (Broadwell) 2,3-GHz processors, Fast NVMe SSD, vCore = 1 LP (Hyper-Thread) |
| Aantal vCores | 8, 16, 24 vCores | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| Maxi maal geheugen (geheugen/kern percentage) | 7 GB per vCore<br/>Voeg meer vCores toe om meer geheugen te verkrijgen. | 5,1 GB per vCore<br/>Voeg meer vCores toe om meer geheugen te verkrijgen. |
| Maxi maal in-Memory OLTP-geheugen | Limiet voor instanties: 3 GB per vCore<br/>Database limieten:<br/> -8-kern: 8 GB per data base<br/> -16-core: 20 GB per data base<br/> -24-Core: 36 GB per data base | Limiet voor instanties: 2,5 GB per vCore<br/>Database limieten:<br/> -8-kern: 13 GB per data base<br/> -16-core: 32 GB per data base |
| Maximum aantal gereserveerde exemplaren |  Algemeen: 8 TB<br/>Bedrijfskritiek: CAPACITEIT | Algemeen: 8 TB<br/> Bedrijfskritiek 1 TB, 2 TB of 4 TB afhankelijk van het aantal kernen |

> [!IMPORTANT]
> - Gen4-hardware wordt gefaseerd uitgevoerd. Het is raadzaam om nieuwe beheerde exemplaren te implementeren op GEN5-hardware.
> - Gen4-hardware is op dit moment nog steeds alleen beschikbaar in de volgende regio's: Europa-noord, Europa-west, VS-Oost, Zuid-Centraal VS, Noord-Centraal VS, VS-West 2, VS-midden, Canada-centraal, India-zuid, Zuidoost-Azië en Korea-centraal.

### <a name="service-tier-characteristics"></a>Kenmerken van servicelaag

Het beheerde exemplaar heeft twee service lagen: [Algemeen](sql-database-service-tier-general-purpose.md) en [bedrijfskritiek](sql-database-service-tier-business-critical.md). Deze lagen bieden [verschillende mogelijkheden](sql-database-service-tiers-general-purpose-business-critical.md), zoals wordt beschreven in de volgende tabel:

| **Functie** | **Algemeen** | **Bedrijfskritiek** |
| --- | --- | --- |
| Aantal vCores\* | Gen4 8, 16, 24<br/>GEN5 4, 8, 16, 24, 32, 40, 64, 80 | Gen4 8, 16, 24 <br/> GEN5 4, 8, 16, 24, 32, 40, 64, 80 |
| Maxi maal geheugen | Gen4 56 GB-168 GB (7GB/vCore)<br/>GEN5 40,8 GB-408 GB (5,1 GB/vCore)<br/>Voeg meer vCores toe om meer geheugen te verkrijgen. | Gen4 56 GB-168 GB (7GB/vCore)<br/>GEN5 40,8 GB-408 GB (5,1 GB/vCore)<br/>Voeg meer vCores toe om meer geheugen te verkrijgen. |
| Maximale opslag grootte voor gereserveerde instanties | -2 TB voor 4 vCores (alleen GEN5)<br/>-8 TB voor andere grootten | Gen4 1 TB <br/> GEN5 <br/>-1 TB voor 4, 8, 16 vCores<br/>-2 TB voor 24 vCores<br/>-4 TB voor 32, 40, 64, 80 vCores |
| Maximale databasegrootte | 8 TB | 4 TB |
| Maximum aantal data bases per exemplaar | 100 | 100 |
| Maximum aantal database bestanden per exemplaar | Maxi maal 280 | 32.767 bestanden per data base |
| Maximale bestands grootte | 8 TB | 4 TB |
| Maximale grootte van logboek bestand | 2 TB | 2 TB |
| Gegevens/logboek IOPS (benadering) | 500-7.500 per bestand<br/>\*[Bestands grootte verg Roten om meer IOPS te verkrijgen](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 5,5 k-110 K (1375/vCore)<br/>Voeg meer vCores toe om betere IO-prestaties te krijgen. |
| Doorvoer limiet schrijf bewerkingen vastleggen in logboek | 3 MB/s per vCore<br/>Maxi maal 22 MB/s per instantie | 4 MB/s per vCore<br/>Maxi maal 48 MB/s per instantie|
| Gegevens doorvoer (bij benadering) | 100-250 MB/s per bestand<br/>\*[De bestands grootte verg Roten om betere IO-prestaties te krijgen](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | N/A |
| I/o-latentie van opslag (ongeveer) | 5-10 MS | 1-2 MS |
| Maximale grootte van tempDB | 192-1.920 GB (24 GB per vCore)<br/>Voeg meer vCores toe om meer TempDB-ruimte te krijgen. | Beperkt door de maximale opslag grootte van het exemplaar. De grootte van het TempDB-logboek bestand is momenteel beperkt tot 24 GB/vCore. |
| In-memory OLTP | Niet ondersteund | Beschikbaar |
| Maximum aantal sessies | 30000 | 30000 |
| [Alleen-lezen replica's](sql-database-read-scale-out.md) | 0 | 1 (inclusief prijs) |

> [!NOTE]
> - De grootte van de gegevens en het logboek bestand in de gebruikers-en systeem databases zijn opgenomen in de opslag grootte van het exemplaar, vergeleken met de maximale opslag grootte. Gebruik de <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys. master_files</a> -systeem weergave om de totale hoeveelheid gebruikte ruimte te bepalen op basis van data bases. Fouten logboeken worden niet persistent gemaakt en zijn niet opgenomen in de grootte. Back-ups worden niet opgenomen in de opslag grootte.
> - De door Voer en IOPS zijn ook afhankelijk van het pagina formaat dat niet expliciet wordt beperkt door een beheerd exemplaar.
> U kunt een andere Lees bare replica maken in een andere Azure-regio met behulp van groepen voor automatische failover.

> [!NOTE]
> Meer informatie over de [resource limieten vindt u in dit artikel in beheerde exemplaar groepen](sql-database-instance-pools.md#instance-pools-resource-limitations).

## <a name="supported-regions"></a>Ondersteunde regio’s

Beheerde exemplaren kunnen alleen worden gemaakt in [ondersteunde regio's](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Als u een beheerd exemplaar wilt maken in een regio die momenteel niet wordt ondersteund, kunt u [via de Azure Portal een ondersteunings aanvraag verzenden](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Ondersteunde abonnements typen

Managed instance biedt momenteel alleen ondersteuning voor de implementatie van de volgende typen abonnementen:

- [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Cloud serviceprovider (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Pay-As-You-Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Abonnementen met een maandelijks Azure-tegoed voor Visual Studio-abonnees](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Regionale resource beperkingen

Ondersteunde abonnements typen kunnen een beperkt aantal resources per regio bevatten. Het beheerde exemplaar heeft twee standaard limieten per Azure-regio, afhankelijk van een type abonnements type:

- **Subnet limiet**: Het maximum aantal subnetten waarop beheerde exemplaren in één regio worden geïmplementeerd.
- **limiet voor vCore**: Het maximum aantal vCores dat kan worden geïmplementeerd in alle instanties in één regio. Het totale aantal exemplaren is niet beperkt zolang het binnen de vCore-limiet valt.

> [!Note]
> Deze limieten zijn standaard instellingen en niet van technische beperkingen. De limieten kunnen op aanvraag worden verhoogd door een speciale [ondersteunings aanvraag te maken in de Azure Portal](#obtaining-a-larger-quota-for-sql-managed-instance) als u meer beheerde instanties in de huidige regio nodig hebt. Als alternatief kunt u nieuwe beheerde instanties maken in een andere Azure-regio zonder ondersteunings aanvragen te verzenden.

De volgende tabel bevat de **standaard regionale limieten** voor ondersteunde abonnements typen (standaard limieten kunnen worden uitgebreid met ondersteunings aanvraag die hieronder wordt beschreven):

|Abonnementstype| Maximum aantal subnetten beheerde exemplaren | Maximum aantal vCore-eenheden * |
| :---| :--- | :--- |
|Betalen naar gebruik|3|320|
|CSP |8 (15 inch in sommige regio's * *)|960 (1440 in sommige regio's * *)|
|Pay-as-you-go Dev/Test|3|320|
|Enterprise Dev/Test|3|320|
|EA|8 (15 inch in sommige regio's * *)|960 (1440 in sommige regio's * *)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional en MSDN Platforms|2|32|

\*In het plannen van implementaties moet u rekening houden dat de service tier van Bedrijfskritiek (BC) vier (4) keer zoveel vCore capaciteit nodig heeft dan de servicelaag van Algemeen (GP). Bijvoorbeeld: 1 GP vCore = 1 vCore-eenheid en 1 BC vCore = 4 vCore-eenheden. Als u de verbruiks analyse wilt vereenvoudigen met de standaard limieten, bekijkt u de vCore-eenheden in alle subnetten in de regio waarin beheerde exemplaren worden geïmplementeerd en vergelijkt u de resultaten met de limieten van de exemplaar eenheid voor uw abonnements type. De limiet voor het **maximum aantal vCore-eenheden** is van toepassing op elk abonnement in een regio. Er is geen limiet per afzonderlijke subnetten, behalve dat de som van alle vCores die in meerdere subnetten zijn geïmplementeerd, kleiner of gelijk moet zijn aan het **maximum aantal vCore-eenheden**.

\*\*Grotere subnet-en vCore-limieten zijn beschikbaar in de volgende regio's: Australië-oost, VS-Oost, VS-Oost 2, Europa-noord, Zuid-Centraal VS, Zuidoost-Azië, UK-zuid, Europa-west, VS-West 2.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Een groter quotum verkrijgen voor het beheerde exemplaar van SQL

Als u meer beheerde exemplaren in uw huidige regio's nodig hebt, verzendt u een ondersteunings aanvraag om het quotum uit te breiden met behulp van de Azure Portal.
Het proces voor het verkrijgen van een groter quotum initiëren:

1. Open **Help en ondersteuning**en klik op **nieuwe ondersteunings aanvraag**.

   ![Help en ondersteuning](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Op het tabblad basis beginselen van de nieuwe ondersteunings aanvraag:
   - Selecteer voor **probleem type** **service-en abonnements limieten (quota's)** .
   - Bij **Abonnement** selecteert u uw abonnement.
   - Selecteer **SQL database beheerde instantie**bij **quotum type**.
   - Voor het **ondersteunings plan**selecteert u uw ondersteunings plan.

     ![Quotum voor het probleem type](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Klik op **Volgende**.
4. Op het **tabblad probleem** voor de nieuwe ondersteunings aanvraag:
   - Voor **Ernst**selecteert u het Ernst niveau van het probleem.
   - Geef aanvullende informatie over uw probleem, inclusief fout berichten, voor **Details**.
   - Als u **bestanden wilt uploaden**, voegt u een bestand toe met meer informatie (Maxi maal 4 MB).

     ![Probleemdetails](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Een geldige aanvraag moet het volgende omvatten:
     > - De regio waarin de abonnements limiet moet worden verhoogd.
     > - Het vereiste aantal vCores, per servicelaag in bestaande subnetten nadat het quotum is verhoogd (als een van de bestaande subnetten moet worden uitgebreid.
     > - Het vereiste aantal nieuwe subnetten en het totale aantal vCores per servicelaag in de nieuwe subnetten (als u beheerde exemplaren in nieuwe subnetten moet implementeren).

5. Klik op **Volgende**.
6. Voer op het tabblad contact gegevens voor de nieuwe ondersteunings aanvraag de voor Keurs methode voor contact personen (e-mail adres of telefoon nummer) en de contact gegevens in.
7. Klik op **Create**.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een beheerd exemplaar?](sql-database-managed-instance.md)voor meer informatie over het beheerde exemplaar.
- Zie [prijzen van beheerde exemplaren SQL database](https://azure.microsoft.com/pricing/details/sql-database/managed/)voor prijs informatie.
- Zie [de Snelstartgids](sql-database-managed-instance-get-started.md)voor meer informatie over het maken van uw eerste beheerde exemplaar.
