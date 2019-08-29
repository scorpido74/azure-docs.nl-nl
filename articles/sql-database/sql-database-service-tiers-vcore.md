---
title: Azure SQL Database-Service-vCore | Microsoft Docs
description: Met het op vCore gebaseerde aankoop model kunt u de reken-en opslag resources onafhankelijk van elkaar schalen, de on-premises prestaties afstemmen en de prijs optimaliseren.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 06/26/2019
ms.openlocfilehash: c35863ed1d564adf4190efa1888d24f4f4f68ddf
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147859"
---
# <a name="choose-among-the-vcore-service-tiers-and-migrate-from-the-dtu-service-tiers"></a>Kies uit de vCore-service lagen en migreer vanuit de DTU-service lagen

Met het virtuele kern (vCore) gebaseerde aankoop model kunt u de reken-en opslag resources onafhankelijk van elkaar schalen, de on-premises prestaties afstemmen en de prijs optimaliseren. U kunt er ook voor kiezen om het apparaat te genereren:

- **Gen4**: Maxi maal 24 logische Cpu's op basis van Intel E5-2673 v3 (Haswell) 2,4-GHz processors, vCore = 1 PP (fysieke kern), 7 GB per kern, gekoppelde SSD
- **GEN5**: Maxi maal 80 logische Cpu's op basis van Intel E5-2673 v4 (Broadwell) 2,3-GHz processors, vCore = 1 LP (Hyper-Thread), 5,1 GB per kern, Fast eNVM SSD

Gen4 hardware biedt aanzienlijk meer geheugen per vCore. Met GEN5-hardware kunt u echter reken resources veel meer schalen.

> [!IMPORTANT]
> Nieuwe Gen4-data bases worden niet meer ondersteund in de Australië-oost-of Brazilië-zuid regio's.
> [!NOTE]
> Zie [service lagen voor het op DTU gebaseerde aankoop model](sql-database-service-tiers-dtu.md)voor meer informatie over de op DTU gebaseerde service lagen. Zie [Azure SQL database-inkoop modellen](sql-database-purchase-models.md)voor informatie over de verschillen tussen de service lagen voor de op DTU gebaseerde en de op vCore gebaseerde aankoop modellen.

## <a name="service-tier-characteristics"></a>Kenmerken van service tier

Het op vCore gebaseerde aankoop model biedt drie service lagen: algemeen gebruik, grootschalige en bedrijfs kritiek. Deze service lagen worden gedifferentieerd met een bereik van berekenings grootten, ontwerpen met hoge Beschik baarheid, methoden voor fout isolatie, typen en grootte van opslag en I/O-bereiken.

U moet de vereiste opslag-en bewaar periode voor back-ups afzonderlijk configureren. Als u de Bewaar periode voor back-ups wilt instellen, opent u de Azure Portal, gaat u naar de-server (niet de data base) en gaat u naar **beheer back-ups** > beheren**beleids regel** > configureren > **configuratie punt in tijd herstellen** **7- 35 dagen**.

In de volgende tabel worden de verschillen tussen de drie lagen beschreven:

||**Algemeen doel**|**Bedrijfs kritiek**|**Grootschalige**|
|---|---|---|---|
|Het best voor|De meeste zakelijke workloads. Biedt budget gerichte, evenwichtige en schaal bare reken-en opslag opties.|Zakelijke toepassingen met hoge I/O-vereisten. Biedt de hoogste flexibiliteit voor storingen door gebruik te maken van verschillende geïsoleerde replica's.|De meeste zakelijke workloads met zeer schaal bare opslag-en lees vereisten.|
|Compute|**Ingerichte Compute**:<br/>Gen4 1 tot 24 vCores<br/>GEN5 2 tot 80 vCores<br/>**Serverloze Compute**:<br/>GEN5 0,5-16 vCores|**Ingerichte Compute**:<br/>Gen4 1 tot 24 vCores<br/>GEN5 2 tot 80 vCores|**Ingerichte Compute**:<br/>Gen4 1 tot 24 vCores<br/>GEN5 2 tot 80 vCores|
|Geheugen|**Ingerichte Compute**:<br/>Gen4 7 GB per vCore<br/>GEN5 5,1 GB per vCore<br/>**Serverloze Compute**:<br/>GEN5 3 GB per vCore|**Ingerichte Compute**:<br/>Gen4 7 GB per vCore<br/>GEN5 5,1 GB per vCore |**Ingerichte Compute**:<br/>Gen4 7 GB per vCore<br/>GEN5 5,1 GB per vCore|
|Storage|Maakt gebruik van externe opslag.<br/>**Berekenings reken single data base**:<br/>5 GB – 4 TB<br/>**Eén reken database zonder server**:<br/>5 GB - 1 TB<br/>**Beheerd exemplaar**: 32 GB - 8 TB |Maakt gebruik van lokale SSD-opslag.<br/>**Berekenings reken single data base**:<br/>5 GB – 4 TB<br/>**Beheerd exemplaar**:<br/>32 GB - 4 TB |Flexibele Automatische toename van opslag als dat nodig is. Ondersteunt Maxi maal 100 TB aan opslag ruimte. Maakt gebruik van lokale SSD-opslag voor lokale buffer-pool cache en lokale gegevens opslag. Maakt gebruik van Azure externe opslag als definitieve gegevens opslag op lange termijn. |
|I/O-door Voer (ongeveer)|**Eén data base**: 500 IOPS per vCore met 7000 maximum aantal IOPS.<br/>**Beheerd exemplaar**: Is afhankelijk van de [grootte van het bestand](../virtual-machines/windows/premium-storage-performance.md#premium-storage-disk-sizes).|5000 IOPS per kern met 200.000 maximum aantal IOPS|Grootschalige is een architectuur met meerdere lagen met caching op meerdere niveaus. Effectief IOPs is afhankelijk van de werk belasting.|
|Beschikbaarheid|1 replica, geen replica's met lees schaal|3 replica's, 1 [replica met lees grootte](sql-database-read-scale-out.md),<br/>zone-redundante hoge Beschik baarheid (HA)|1 replica met lees-en schrijf bewerkingen, plus 0-4 [replica's met lees grootte](sql-database-read-scale-out.md)|
|Back-ups|[Geografisch redundante opslag met lees toegang (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dagen (standaard 7 dagen)|[Ra-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dagen (standaard 7 dagen)|Back-ups op basis van moment opnamen in azure externe opslag. Herstelt het gebruik van deze moment opnamen voor snel herstel. Back-ups zijn onmiddellijk en zijn niet van invloed op de I/O-prestaties van compute. Herstel bewerkingen zijn snel en zijn geen omvang van de gegevens bewerking (minuten in plaats van uren of dagen).|
|In het geheugen|Niet ondersteund|Ondersteund|Niet ondersteund|
|||

> [!NOTE]
> U kunt in combi natie met een gratis Azure-account een gratis Azure-SQL database in de laag basis service krijgen. Zie [een beheerde Cloud database maken met uw gratis Azure-account](https://azure.microsoft.com/free/services/sql-database/)voor meer informatie.

- Zie voor meer informatie over vCore-resource limieten [vCore resource limieten in één data base](sql-database-vcore-resource-limits-single-databases.md) en [vCore resource limieten in een beheerd exemplaar](sql-database-managed-instance.md#vcore-based-purchasing-model).
- Zie [algemene en bedrijfskritische service lagen](sql-database-service-tiers-general-purpose-business-critical.md)voor meer informatie over de categorieën algemeen gebruik en bedrijfskritische service.
- Zie [grootschalige service tier](sql-database-service-tier-hyperscale.md)(Engelstalig) voor meer informatie over de grootschalige-servicelaag in het op vCore gebaseerde aankoop model.  

## <a name="azure-hybrid-benefit"></a>Azure Hybrid Benefit

In de ingerichte Compute-laag van het op vCore gebaseerde aankoop model kunt u uw bestaande licenties uitwisselen voor kortings tarieven op SQL Database met behulp van [Azure Hybrid Benefit voor SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). Met dit voor deel van Azure kunt u tot wel 30% besparen op Azure SQL Database met behulp van uw on-premises SQL Server licenties met Software Assurance.

![prijzen](./media/sql-database-service-tiers/pricing.png)

Met Azure Hybrid Benefit kunt u ervoor kiezen om alleen te betalen voor de onderliggende Azure-infra structuur met behulp van uw bestaande SQL Server licentie voor de SQL database engine zelf (prijzen voor basis berekeningen). u kunt ook betalen voor de onderliggende infra structuur en de SQL Server licentie (inbegrepen prijzen in licentie).

U kunt uw licentie model kiezen of wijzigen met behulp van de Azure Portal of door gebruik te maken van een van de volgende Api's:

- Het licentie type instellen of bijwerken met behulp van Power shell:

  - [New-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabase)
  - [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase)
  - [New-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance)
  - [Set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

- Het licentie type instellen of bijwerken met behulp van de Azure CLI:

  - [az sql db create](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create)
  - [az sql db update](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update)
  - [AZ SQL mi Create](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create)
  - [AZ SQL mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update)

- Het licentie type instellen of bijwerken met behulp van de REST API:

  - [Data bases: maken of bijwerken](https://docs.microsoft.com/rest/api/sql/databases/createorupdate)
  - [Data bases-bijwerken](https://docs.microsoft.com/rest/api/sql/databases/update)
  - [Beheerde instanties: maken of bijwerken](https://docs.microsoft.com/rest/api/sql/managedinstances/createorupdate)
  - [Beheerde instanties-bijwerken](https://docs.microsoft.com/rest/api/sql/managedinstances/update)

## <a name="migrate-from-the-dtu-based-model-to-the-vcore-based-model"></a>Migreren van het DTU-model naar het model op basis van vCore

### <a name="migrate-a-database"></a>Een database migreren

Het migreren van een Data Base van het DTU-gebaseerde aankoop model naar het op vCore gebaseerde aankoop model is vergelijkbaar met het bijwerken of downgradeen tussen de Standard-en Premium-Service lagen in het op DTU gebaseerde aankoop model.

### <a name="migrate-databases-with-geo-replication-links"></a>Data bases met geo-replicatie koppelingen migreren

Migreren van het DTU-model naar het op vCore gebaseerde aankoop model is vergelijkbaar met het bijwerken of verlagen van de geo-replicatie relaties tussen data bases in de Standard-en Premium-Service lagen. Tijdens de migratie hoeft u geo-replicatie niet te stoppen, maar moet u deze regels voor volgorde bepaling volgen:

- Wanneer u een upgrade uitvoert, moet u eerst de secundaire data base upgraden en vervolgens een upgrade uitvoeren van de primaire.
- Wanneer u een downgrade uitvoert, maakt u de volg orde ongedaan: u moet eerst de primaire data base opdowngradeen en vervolgens het secundaire database downgrade verlagen.

Wanneer u geo-replicatie tussen twee elastische Pools gebruikt, raden we u aan om één groep als primair en als de secundaire groep aan te wijzen. In dat geval moet u bij het migreren van elastische Pools dezelfde richt lijnen voor sequentiëren gebruiken. Als u echter elastische Pools hebt die zowel primaire als secundaire data bases bevatten, behandelt u de pool met het hogere gebruik als primair en volgt u de regels voor volgorde bepaling dienovereenkomstig.  

De volgende tabel bevat richt lijnen voor specifieke migratie scenario's:

|Huidige servicelaag|Doel service-laag|Type migratie|Gebruikerssacties|
|---|---|---|---|
|Standard|Algemeen gebruik|Zij|Kan in een wille keurige volg orde worden gemigreerd, maar moet de juiste vCore-grootte worden gegarandeerd *|
|Premium|Bedrijfskritiek|Zij|Kan in een wille keurige volg orde worden gemigreerd, maar moet de juiste vCore-grootte worden gegarandeerd *|
|Standard|Bedrijfskritiek|Upgrade|Moet secundair eerst worden gemigreerd|
|Bedrijfskritiek|Standard|Downgrade uitvoeren|Moet primair eerst worden gemigreerd|
|Premium|Algemeen gebruik|Downgrade uitvoeren|Moet primair eerst worden gemigreerd|
|Algemeen gebruik|Premium|Upgrade|Moet secundair eerst worden gemigreerd|
|Bedrijfskritiek|Algemeen gebruik|Downgrade uitvoeren|Moet primair eerst worden gemigreerd|
|Algemeen gebruik|Bedrijfskritiek|Upgrade|Moet secundair eerst worden gemigreerd|
||||

\*Elke 100-Dtu's in de laag standaard vereist ten minste 1 vCore, en voor elke 125 Dtu's in de laag Premium is ten minste 1 vCore vereist.

### <a name="migrate-failover-groups"></a>Failover-groepen migreren

Migratie van failover-groepen met meerdere data bases vereist een afzonderlijke migratie van de primaire en secundaire data bases. Tijdens dat proces gelden dezelfde aandachtspunten en sequentiëren. Nadat de data bases zijn geconverteerd naar het op vCore gebaseerde aankoop model, blijft de failovergroep van kracht met dezelfde beleids instellingen.

### <a name="create-a-geo-replication-secondary-database"></a>Een secundaire data base met geo-replicatie maken

U kunt een secundaire data base met geo-replicatie (een geo-secundair) alleen maken door dezelfde servicelaag te gebruiken als u hebt gebruikt voor de primaire data base. Voor data bases met een hoge frequentie voor het genereren van het logboek, wordt aangeraden om de geo-secundair te maken met dezelfde reken grootte als de primaire.

Als u een geo-secundair maakt in de elastische pool voor één primaire data base, moet u ervoor `maxVCore` zorgen dat de instelling voor de pool overeenkomt met de reken grootte van de primaire data base. Als u een geo-secundair maakt voor een primaire groep in een andere elastische pool, raden we aan dat de groepen dezelfde `maxVCore` instellingen hebben.

### <a name="use-database-copy-to-convert-a-dtu-based-database-to-a-vcore-based-database"></a>Database kopie gebruiken om een DTU-gebaseerde data base te converteren naar een op vCore gebaseerde data base

U kunt elke Data Base met een op DTU gebaseerde reken grootte kopiëren naar een Data Base met een op vCore gebaseerde reken grootte zonder beperkingen of speciale sequentiëren, zolang de doel berekenings grootte de maximale database grootte van de bron database ondersteunt. De kopie van de data base maakt een moment opname van de gegevens vanaf de begin tijd van de Kopieer bewerking en synchroniseert geen gegevens tussen de bron en het doel.

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL database resource limieten op basis van vCore voor afzonderlijke data](sql-database-vcore-resource-limits-single-databases.md)bases voor de specifieke berekenings grootte en beschik bare opslag grootte voor afzonderlijke data bases.
- Zie [SQL database op vCore gebaseerde resource limieten voor elastische Pools](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)voor de specifieke berekenings grootten en opties voor opslag grootte die beschikbaar zijn voor elastische Pools.
