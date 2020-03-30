---
title: Overzicht van vCore-modellen
description: Met het vCore-inkoopmodel u gegevens- en opslagbronnen onafhankelijk schalen, on-premises prestaties afstemmen en de prijs optimaliseren.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 5fd69dcd30292630862887ab5434764ba377b396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481079"
---
# <a name="vcore-model-overview"></a>Overzicht van vCore-modellen

Het virtual core (vCore) model biedt verschillende voordelen:

- Hogere reken-, geheugen-, IO- en opslaglimieten.
- Controle over de hardwaregeneratie om beter aan de reken- en geheugenvereisten van de workload te voldoen.
- Kortingen op [Azure Hybrid Benefit (AHB)](sql-database-azure-hybrid-benefit.md) en [Gereserveerde instantie (RI)](sql-database-reserved-capacity.md).
- Meer transparantie in de hardwaredetails die de compute van stroom voorstaan; vergemakkelijkt de planning voor migraties van on-premises implementaties.

## <a name="service-tiers"></a>Servicelagen

Opties voor servicelagen in het vCore-model zijn General Purpose, Business Critical en Hyperscale. De servicelaag definieert over het algemeen de opslagarchitectuur, ruimte- en IO-limieten en opties voor bedrijfscontinuïteit met betrekking tot beschikbaarheid en noodherstel.

||**Algemeen doel**|**Bedrijfskritisch**|**Hyperscale**|
|---|---|---|---|
|Ideaal voor|De meeste zakelijke workloads. Biedt budgetgerichte, gebalanceerde en schaalbare reken- en opslagopties. |Biedt bedrijfstoepassingen de hoogste veerkracht voor storingen door verschillende geïsoleerde replica's te gebruiken en biedt de hoogste I/O-prestaties per databasereplica.|De meeste bedrijfsworkloads met zeer schaalbare opslag- en leesschaalvereisten.  Biedt een hogere veerkracht voor storingen door configuratie van meer dan één geïsoleerde databasereplica toe te staan. |
|Storage|Maakt gebruik van externe opslag.<br/>**Afzonderlijke databases en elastische pools voorzien compute:**<br/>5 GB – 4 TB<br/>**Serverless compute:**<br/>5 GB - 3 TB<br/>**Beheerde instantie**: 32 GB - 8 TB |Maakt gebruik van lokale SSD-opslag.<br/>**Afzonderlijke databases en elastische pools voorzien compute:**<br/>5 GB – 4 TB<br/>**Beheerde instantie:**<br/>32 GB - 4 TB |Flexibele autogrow van opslag als dat nodig is. Ondersteunt maximaal 100 TB opslagruimte. Gebruikt lokale SSD-opslag voor lokale bufferpoolcache en lokale gegevensopslag. Gebruikt Azure remote storage als laatste gegevensarchief voor de lange termijn. |
|IOPS en doorvoer (bij benadering)|**Afzonderlijke databases en elastische pools:** zie resourcelimieten voor [afzonderlijke databases](../sql-database/sql-database-vcore-resource-limits-single-databases.md) en [elastische pools.](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)<br/>**Beheerde instantie:** Zie [Overzicht Azure SQL Database managed instance resource limits](../sql-database/sql-database-managed-instance-resource-limits.md#service-tier-characteristics).|Zie resourcelimieten voor [afzonderlijke databases](../sql-database/sql-database-vcore-resource-limits-single-databases.md) en [elastische pools.](../sql-database/sql-database-vcore-resource-limits-elastic-pools.md)|Hyperscale is een multi-tiered architectuur met caching op meerdere niveaus. Effectieve IOPS en doorvoer zijn afhankelijk van de werkbelasting.|
|Beschikbaarheid|1 replica, geen leesschaal replica's|3 replica's, 1 [read-scale replica,](sql-database-read-scale-out.md)<br/>zone-redundante hoge beschikbaarheid (HA)|1 lees-schrijf replica, plus 0-4 [leesschaal replica's](sql-database-read-scale-out.md)|
|Back-ups|[Georedundant-opslag met leestoegang (RA-GRS),](../storage/common/storage-designing-ha-apps-with-ragrs.md)7-35 dagen (standaard 7 dagen)|[RA-GRS](../storage/common/storage-designing-ha-apps-with-ragrs.md), 7-35 dagen (standaard 7 dagen)|Back-ups op basis van momentopnamen in azure-externe opslag. Herstelt gebruik maken van deze snapshots voor snel herstel. Back-ups zijn onmiddellijk en hebben geen invloed op de I/O-prestaties van de compute.Backups are instantaneous and don't impact compute I/O performance. Herstelt zijn snel en zijn geen grootte van gegevens (minuten in beslag nemen in plaats van uren of dagen).|
|In het geheugen|Niet ondersteund|Ondersteund|Niet ondersteund|
|||


### <a name="choosing-a-service-tier"></a>Het kiezen van een servicelaag

Zie de volgende artikelen voor informatie over het selecteren van een servicelaag voor uw specifieke werkbelasting:

- [Wanneer de servicelaag voor algemeen gebruik kiezen](sql-database-service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Wanneer kiest u de servicelaag Bedrijfskritiek](sql-database-service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Wanneer kiest u de hyperscale-servicelaag](sql-database-service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Rekenniveaus

Rekenlaagsopties in het vCore-model omvatten de ingerichte en serverloze rekenlagen.


### <a name="provisioned-compute"></a>Ingerichte rekenkracht

De ingerichte compute-laag biedt een specifieke hoeveelheid rekenresources die continu onafhankelijk van werkbelastingactiviteit worden ingericht en facturen voor de hoeveelheid compute die is ingericht tegen een vaste prijs per uur.


### <a name="serverless-compute"></a>Serverloze compute

De [automatische gegevenslaag serverless-schalen](sql-database-serverless.md) beschalen gegevensresources op basis van werkbelastingactiviteit en facturen voor de hoeveelheid compute die per seconde wordt gebruikt.



## <a name="hardware-generations"></a>Hardware generaties

Hardware generatie opties in de vCore model omvatten Gen 4/5, M-serie (preview), en Fsv2-serie (preview). De hardwaregeneratie definieert over het algemeen de compute- en geheugenlimieten en andere kenmerken die van invloed zijn op de prestaties van de werkbelasting.

### <a name="gen4gen5"></a>Gen4/Gen5

- Gen4/Gen5-hardware biedt evenwichtige compute- en geheugenbronnen en is geschikt voor de meeste databaseworkloads die geen hoger geheugen, hogere vCore of snellere enkele vCore-vereisten hebben, zoals die worden geboden door de Fsv2-serie of De M-serie.

Zie [Gen4/Gen5 beschikbaarheid](#gen4gen5-1)voor regio's waar Gen4/Gen5 beschikbaar is.

### <a name="fsv2-seriespreview"></a>Fsv2-serie (preview)

- De FSV2-serie is een voor compute geoptimaliseerde hardwareoptie die een lage CPU-latentie en hoge kloksnelheid levert voor de meest cpu-veeleisende workloads.
- Afhankelijk van de workload kunnen de Fsv2-serie meer CPU-prestaties per vCore leveren dan Gen5, en de 72 vCore-grootte kan meer CPU-prestaties leveren voor minder kosten dan 80 vCores op Gen5. 
- Fsv2 biedt minder geheugen en tempdb per vCore dan andere hardware, dus workloads die gevoelig zijn voor die limieten kunnen in plaats daarvan gen5- of M-serie overwegen.  

Fsv2-serie in alleen ondersteund in de General Purpose tier.  Voor regio's waar fsv2-serie beschikbaar is, zie [fsv2-serie beschikbaarheid](#fsv2-series).


### <a name="m-seriespreview"></a>M-serie (preview)

- De M-serie is een geheugengeoptimaliseerde hardwareoptie voor workloads die meer geheugen en hogere rekenlimieten eisen dan gen5 biedt.
- De M-serie biedt 29 GB per vCore en 128 vCores, waardoor de geheugenlimiet ten opzichte van Gen5 met 8x toeneemt tot bijna 4 TB.

M-series worden alleen ondersteund in de business critical-laag en ondersteunen geen redundantie in de zone.

Om M-serie hardware in te schakelen voor een abonnement en regio, moet een ondersteuningsverzoek worden geopend. Het abonnement moet een betaalde aanbieding zijn, inclusief Betalen per gebruik of Ondernemingsovereenkomst (EA).  Als de ondersteuningsaanvraag wordt goedgekeurd, volgt de selectie- en inrichtingservaring van de M-serie hetzelfde patroon als voor andere hardwaregeneraties. Zie Beschikbaarheid van [de M-serie](#m-series)voor regio's waar m-series beschikbaar zijn.


### <a name="compute-and-memory-specifications"></a>Reken- en geheugenspecificaties


|Hardwaregeneratie  |Compute  |Geheugen  |
|:---------|:---------|:---------|
|Gen4 Gen4     |- Intel E5-2673 v3 (Haswell) 2,4 GHz-processors<br>- Levering tot 24 vCores (1 vCore = 1 fysieke kern)  |- 7 GB per vCore<br>- Voorziening tot 168 GB|
|Gen5 Gen5     |**Ingerichte rekenkracht**<br>- Intel E5-2673 v4 (Broadwell) 2,3-GHz en Intel SP-8160 (Skylake)* processors<br>- Tot 80 vCores (1 vCore = 1 hyperthread) inrichten<br><br>**Serverloze compute**<br>- Intel E5-2673 v4 (Broadwell) 2,3-GHz en Intel SP-8160 (Skylake)* processors<br>- Automatisch opschalen tot 16 vCores (1 vCore = 1 hyper-thread)|**Ingerichte rekenkracht**<br>- 5,1 GB per vCore<br>- Voorziening tot 408 GB<br><br>**Serverloze compute**<br>- Automatisch schalen tot 24 GB per vCore<br>- Automatisch schalen tot maximaal 48 GB|
|Fsv2-serie     |- Intel Xeon Platinum 8168 (SkyLake) processors<br>- Met een aanhoudende all core turbo kloksnelheid van 3,4 GHz en een maximale single core turbo kloksnelheid van 3,7 GHz.<br>- Voorziening 72 vCores (1 vCore = 1 hyper-thread)|- 1,9 GB per vCore<br>- Voorziening 136 GB|
|M-serie     |- Intel Xeon E7-8890 v3 2,5 GHz-processors<br>- Voorziening 128 vCores (1 vCore = 1 hyper-thread)|- 29 GB per vCore<br>- Voorziening 3,7 TB|

\*In de [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) dynamic management view wordt hardwaregeneratie voor Gen5-databases met Intel SP-8160 (Skylake)-processors weergegeven als Gen6. Resourcelimieten voor alle Gen5-databases zijn hetzelfde, ongeacht het processortype (Broadwell of Skylake).

Zie [Resourcelimieten voor afzonderlijke databases (vCore)](sql-database-vcore-resource-limits-single-databases.md)of [Resourcelimieten voor elastische pools (vCore) voor](sql-database-vcore-resource-limits-elastic-pools.md)meer informatie over resourcelimieten.

### <a name="selecting-a-hardware-generation"></a>Een hardwaregeneratie selecteren

In de Azure-portal u de hardwaregeneratie voor een SQL-database of -groep selecteren op het moment van maken, of u de hardwaregeneratie van een bestaande SQL-database of -groep wijzigen.

**Een hardwaregeneratie selecteren bij het maken van een SQL-database of -groep**

Zie [Een SQL-database maken](sql-database-single-database-get-started.md)voor gedetailleerde informatie.

Selecteer op het tabblad **Basisbeginselen** de **koppeling Database configureren** in de sectie Compute + **storage** en selecteer vervolgens de koppeling **Configuratie wijzigen:**

  ![database configureren](media/sql-database-service-tiers-vcore/configure-sql-database.png)

Selecteer de gewenste hardwaregeneratie:

  ![hardware selecteren](media/sql-database-service-tiers-vcore/select-hardware.png)


**De hardwaregeneratie van een bestaande SQL-database of -groep wijzigen**

Selecteer voor een database op de pagina Overzicht de koppeling **Prijslaag:**

  ![hardware wijzigen](media/sql-database-service-tiers-vcore/change-hardware.png)

Selecteer **Configureren**voor een groep op de pagina Overzicht .

Volg de stappen om de configuratie te wijzigen en selecteer de hardwaregeneratie zoals beschreven in de vorige stappen.

**Een hardwaregeneratie selecteren bij het maken van een beheerde instantie**

Zie [Een beheerde instantie maken](sql-database-managed-instance-get-started.md)voor gedetailleerde informatie.

Selecteer op het tabblad **Basisbeginselen** de **koppeling Database configureren** in de sectie Compute + **storage** en selecteer de gewenste hardwaregeneratie:

  ![beheerde instantie configureren](media/sql-database-service-tiers-vcore/configure-managed-instance.png)
  
**De hardwaregeneratie van een bestaand beheerd exemplaar wijzigen**

# <a name="portal"></a>[Portal](#tab/azure-portal)

Selecteer op de pagina Beheerde instantie de koppeling **Prijslaag** die onder de sectie Instellingen wordt geplaatst

![beheerde instantiehardware wijzigen](media/sql-database-service-tiers-vcore/change-managed-instance-hardware.png)

Op de pagina **Prijscategorie** u de hardwaregeneratie wijzigen zoals beschreven in de vorige stappen.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Gebruik het volgende PowerShell-script:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Controleer voor meer informatie de opdracht [Set-AzSqlInstance.](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de volgende opdracht CLI:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Voor meer details controleer [az sql mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update) commando.

---

### <a name="hardware-availability"></a>Beschikbaarheid van hardware

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a>Gen4/Gen5

Gen4 hardware [wordt uitgefaseerd](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) en is niet meer beschikbaar voor de nieuwe implementaties. Alle nieuwe databases moeten worden geïmplementeerd op Gen5-hardware.

Gen5 is beschikbaar in de meeste regio's wereldwijd.

#### <a name="fsv2-series"></a>Fsv2-serie

Fsv2-serie is beschikbaar in de volgende regio's: Australia Central, Australia Central 2, Australia East, Australia Southeast, Brazil South, Canada Central, East Asia, East Us, France Central, India Central, India West, Korea Central, Korea South, North Europa, Zuid-Afrika Noord, Zuidoost-Azië, Uk South, UK West, West-Europa, West-Us 2.


#### <a name="m-series"></a>M-serie

M-serie is beschikbaar in de volgende regio's: Oost-VS, Noord-Europa, West-Europa, West-VS 2.
M-series kunnen ook een beperkte beschikbaarheid hebben in andere regio's. U een andere regio aanvragen dan hier wordt vermeld, maar afhandeling in een andere regio is mogelijk niet mogelijk.

Om de beschikbaarheid van M-series in een abonnement mogelijk te maken, moet toegang worden aangevraagd door [een nieuw ondersteuningsverzoek in te dienen.](#create-a-support-request-to-enable-m-series)


##### <a name="create-a-support-request-to-enable-m-series"></a>Maak een ondersteuningsverzoek om M-reeksen in te schakelen: 

1. Selecteer **Help + ondersteuning** in de portal.
2. Selecteer **Nieuw ondersteuningsverzoek**.

Geef op de pagina **Basisbeginselen** het volgende op:

1. Selecteer **Service- en abonnementslimieten (quota)** **voor issuetype.**
2. Voor **Abonnement** = selecteer het abonnement om M-reeksen in te schakelen.
3. Selecteer **SQL-database**voor **quotumtype**.
4. Selecteer **Volgende** om naar de pagina **Details** te gaan.

Geef op de pagina **Details** het volgende op:

1. Selecteer in de sectie **PROBLEEMDETAILS** de koppeling **Details geven.** 
2. Selecteer **Voor SQL Database-quotatype** **M-reeksen**.
3. Selecteer **bij Regio**de regio om M-reeksen in te schakelen.
    Zie Beschikbaarheid van [de M-serie](#m-series)voor regio's waar m-series beschikbaar zijn.

Goedgekeurde ondersteuningsaanvragen worden doorgaans binnen 5 werkdagen uitgevoerd.


## <a name="next-steps"></a>Volgende stappen

- Zie [Een SQL-database maken met de Azure-portal](sql-database-single-database-get-started.md)als u een SQL-database wilt maken.
- Zie [SQL Database vCore-gebaseerde resourcelimieten voor afzonderlijke databases voor](sql-database-vcore-resource-limits-single-databases.md)de specifieke rekengroottes en opslaggroottekeuzes die beschikbaar zijn voor afzonderlijke databases.
- Zie [SQL Database vCore-gebaseerde resourcelimieten voor elastische pools voor](sql-database-vcore-resource-limits-elastic-pools.md)de specifieke rekengroottes en opties voor opslaggroottes die beschikbaar zijn voor elastische pools.
- Zie de [prijspagina azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/)voor prijsgegevens .
