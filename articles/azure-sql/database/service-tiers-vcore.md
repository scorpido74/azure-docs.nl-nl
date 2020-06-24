---
title: overzicht van vCore-inkoop model
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Met het vCore-aankoop model kunt u de reken-en opslag resources onafhankelijk van elkaar schalen, de on-premises prestaties afstemmen en de prijs optimaliseren voor Azure SQL Database en Azure SQL Managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/27/2019
ms.openlocfilehash: 3a359e4b3523615623c76d48c1aafd7aa95a5277
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85255038"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>overzicht van vCore-model-Azure SQL Database en Azure SQL Managed instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Het Virtual core (vCore)-aankoop model dat wordt gebruikt door Azure SQL Database en Azure SQL Managed instance biedt diverse voor delen:

- Hogere reken-, geheugen-, I/O-en opslag limieten.
- Controle over de hardware-generatie om beter te voldoen aan de reken-en geheugen vereisten van de werk belasting.
- Prijs kortingen voor [Azure Hybrid Benefit (AHB)](../azure-hybrid-benefit.md) en [gereserveerde instanties (RI)](reserved-capacity-overview.md).
- Grotere transparantie in de hardware-details die de reken kracht stroomt; vereenvoudigt het plannen van migraties van on-premises implementaties.

## <a name="service-tiers"></a>Servicelagen

Opties voor de servicelaag in het vCore-model bevatten Algemeen, Bedrijfskritiek en grootschalige. De servicelaag definieert in het algemeen de opslag architectuur, ruimte en I/O-limieten en opties voor bedrijfs continuïteit die betrekking hebben op Beschik baarheid en herstel na nood gevallen.

||**Algemeen**|**Bedrijfskritiek**|**Hyperscale**|
|---|---|---|---|
|Ideaal voor|De meeste zakelijke workloads. Biedt budget gerichte, evenwichtige en schaal bare reken-en opslag opties. |Biedt zakelijke toepassingen de hoogste flexibiliteit voor storingen met behulp van verschillende geïsoleerde replica's en biedt de hoogste I/O-prestaties per database replica.|De meeste zakelijke workloads met zeer schaal bare opslag-en lees vereisten.  Biedt meer flexibiliteit voor storingen door de configuratie van meer dan één geïsoleerde database replica toe te staan. |
|Storage|Maakt gebruik van externe opslag.<br/>**SQL database voorziene Compute**:<br/>5 GB – 4 TB<br/>**Serverloze Compute**:<br/>5 GB-3 TB<br/>**SQL Managed instance**: 32 GB-8 TB |Maakt gebruik van lokale SSD-opslag.<br/>**SQL database voorziene Compute**:<br/>5 GB – 4 TB<br/>**SQL-beheerd exemplaar**:<br/>32 GB-4 TB |Flexibele Automatische toename van opslag als dat nodig is. Ondersteunt Maxi maal 100 TB aan opslag ruimte. Maakt gebruik van lokale SSD-opslag voor lokale buffer-pool cache en lokale gegevens opslag. Maakt gebruik van Azure externe opslag als definitieve gegevens opslag op lange termijn. |
|IOPS en door Voer (ongeveer)|**SQL database**: Zie resource limieten voor [afzonderlijke data bases](resource-limits-vcore-single-databases.md) en [elastische Pools](resource-limits-vcore-elastic-pools.md).<br/>**SQL-beheerd exemplaar**: zie overzicht van de [resource limieten voor Azure SQL Managed instance](../managed-instance/resource-limits.md#service-tier-characteristics).|Zie resource limieten voor [afzonderlijke data bases](resource-limits-vcore-single-databases.md) en [elastische Pools](resource-limits-vcore-elastic-pools.md).|Grootschalige is een architectuur met meerdere lagen met caching op meerdere niveaus. Effectief IOPS en door Voer is afhankelijk van de werk belasting.|
|Beschikbaarheid|1 replica, geen replica's met lees schaal|3 replica's, 1 [replica met lees grootte](read-scale-out.md),<br/>zone-redundante hoge Beschik baarheid (HA)|1 replica met lees-en schrijf bewerkingen, plus 0-4 [replica's met lees grootte](read-scale-out.md)|
|Back-ups|[Geografisch redundante opslag met lees toegang (RA-GRS)](../../storage/common/geo-redundant-design.md), 7-35 dagen (standaard 7 dagen)|[Ra-GRS](../..//storage/common/geo-redundant-design.md), 7-35 dagen (standaard 7 dagen)|Back-ups op basis van moment opnamen in azure externe opslag. Herstelt het gebruik van deze moment opnamen voor snel herstel. Back-ups zijn onmiddellijk en zijn niet van invloed op de I/O-prestaties van compute. Herstel bewerkingen zijn snel en zijn geen omvang van de gegevens bewerking (minuten in plaats van uren of dagen).|
|In het geheugen|Niet ondersteund|Ondersteund|Niet ondersteund|
|||


### <a name="choosing-a-service-tier"></a>Het kiezen van een servicelaag

Raadpleeg de volgende artikelen voor meer informatie over het selecteren van een servicelaag voor uw specifieke workload:

- [Wanneer u de laag Algemeen-Service kiest](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Wanneer u de laag Bedrijfskritiek-service kiest](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Wanneer u de grootschalige-servicelaag kiest](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Reken lagen

Opties voor Compute-lagen in het vCore-model bevatten de ingerichte en serverloze reken lagen.


### <a name="provisioned-compute"></a>Ingerichte compute

De ingerichte Compute-laag biedt een specifieke hoeveelheid reken bronnen die continu worden ingericht, onafhankelijk van de werkbelasting activiteit, en facturen voor de hoeveelheid Compute die is ingericht tegen een vaste prijs per uur.


### <a name="serverless-compute"></a>Serverloze compute

Met de [serverloze Compute-laag](serverless-tier-overview.md) worden reken resources automatisch geschaald op basis van de werkbelasting activiteit en worden er facturen in rekening gebracht voor de hoeveelheid reken kracht per seconde.



## <a name="hardware-generations"></a>Hardware gegenereerd

Opties voor het genereren van hardware in het vCore-model zijn gen 4/5, M-Series (preview) en Fsv2-Series (preview). Het genereren van de hardware definieert doorgaans de reken-en geheugen limieten en andere kenmerken die van invloed zijn op de prestaties van de werk belasting.

### <a name="gen4gen5"></a>Gen4/Gen5

- Gen4/Gen5-hardware biedt evenwichtige reken-en geheugen bronnen en is geschikt voor de meeste data base-workloads die niet meer geheugen, hogere vCore of snellere enkelvoudige vCore vereisten hebben zoals geleverd door de Fsv2-serie of M-serie.

Zie [Gen4/Gen5 Beschik baarheid](#gen4gen5-1)voor regio's waar Gen4/Gen5 beschikbaar is.

### <a name="fsv2-seriespreview"></a>Fsv2-serie (preview-versie)

- Fsv2-serie is een hardwarematige Compute-optie die lage CPU-latentie en hoge klok snelheid levert voor de meeste CPU-werk belastingen.
- Afhankelijk van de werk belasting, kan de Fsv2-serie meer CPU-prestaties leveren per vCore dan GEN5, en de grootte van 72 vCore kan meer CPU-prestaties bieden voor minder kosten dan 80 vCores op GEN5. 
- Fsv2 biedt minder geheugen en tempdb per vCore dan andere hardware, zodat werk belastingen die gevoelig zijn voor deze limieten wellicht in plaats daarvan Gen5 of M-serie willen overwegen.  

De Fsv2-serie wordt alleen ondersteund in de laag Algemeen.  Zie de [Beschik baarheid van Fsv2-Series](#fsv2-series)voor regio's waar Fsv2-serie beschikbaar is.


### <a name="m-seriespreview"></a>M-serie (preview-versie)

- M-serie is een optie voor geoptimaliseerd voor geheugen voor werk belastingen die meer geheugen en hogere reken limieten hebben dan wordt verzorgd door GEN5.
- De M-serie biedt 29 GB per vCore en 128 vCores, waardoor de geheugen limiet wordt verhoogd ten opzichte van GEN5 van 8x tot bijna 4 TB.

De M-serie wordt alleen ondersteund in de laag Bedrijfskritiek en biedt geen ondersteuning voor zone redundantie.  Het abonnement moet een betaald aanbod type zijn, inclusief betalen naar gebruik of Enterprise Agreement (EA).  Zie de [Beschik baarheid van de m-serie](#m-series)voor regio's waar de m-serie beschikbaar is.

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="compute-and-memory-specifications"></a>Specificaties van Compute en geheugen


|Hardware genereren  |Compute  |Geheugen  |
|:---------|:---------|:---------|
|Gen4     |-Intel E5-2673 v3 (Haswell) 2,4 GHz-processors<br>-Tot 24 vCores (1 vCore = 1 fysieke kern) inrichten  |-7 GB per vCore<br>-Maxi maal 168 GB inrichten|
|GEN5     |**Ingerichte compute**<br>-Intel E5-2673 v4 (Broadwell) 2,3-GHz en Intel SP-8160 (Skylake) * processors<br>-Maxi maal 80 vCores (1 vCore = 1 Hyper Thread) inrichten<br><br>**Serverloze compute**<br>-Intel E5-2673 v4 (Broadwell) 2,3-GHz en Intel SP-8160 (Skylake) * processors<br>-Schaal automatisch naar 16 vCores (1 vCore = 1 Hyper Thread)|**Ingerichte compute**<br>-5,1 GB per vCore<br>-Maxi maal 408 GB inrichten<br><br>**Serverloze compute**<br>-Automatisch schalen naar 24 GB per vCore<br>-Maxi maal 48 GB automatisch schalen|
|Fsv2-serie     |-Intel Xeon Platinum 8168-processors (SkyLake)<br>-Met een zeer hoge Turbo klok snelheid van 3,4 GHz en een maximale klok snelheid van Maxi maal één kern van 3,7 GHz.<br>-Provision 72 vCores (1 vCore = 1 Hyper Thread)|-1,9 GB per vCore<br>-Inrichting van 136 GB|
|M-serie     |-Intel Xeon E7-8890 v3 2,5 GHz en Intel Xeon Platinum 8280M 2,7 GHz (Cascade Lake)-processors<br>-Provision 128 vCores (1 vCore = 1 Hyper Thread)|-29 GB per vCore<br>-Inrichting van 3,7 TB|

\*In de weer gave [sys. dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) Dynamic Management worden de hardware-generatie voor GEN5-data bases met Intel SP-8160 (Skylake)-processors weer gegeven als Gen6. Resource limieten voor alle GEN5-data bases zijn hetzelfde, ongeacht het processor type (Broadwell of Skylake).

Zie [resource limieten voor afzonderlijke data bases (vCore)](resource-limits-vcore-single-databases.md)of resource limieten [voor elastische Pools (vCore)](resource-limits-vcore-elastic-pools.md)voor meer informatie over resource limieten.

### <a name="selecting-a-hardware-generation"></a>Een hardware-generatie selecteren

In de Azure Portal kunt u de hardware-generatie voor een Data Base of groep in SQL Database selecteren op het moment dat deze wordt gemaakt, of u kunt de hardware-generatie van een bestaande data base of groep wijzigen.

**Een hardwarematige generatie selecteren bij het maken van een SQL Database of groep**

Zie [een SQL database maken](single-database-create-quickstart.md)voor gedetailleerde informatie.

Selecteer op het tabblad **basis beginselen** de koppeling **Data Base configureren** in de sectie **berekenings** -en opslag en selecteer vervolgens de koppeling **configuratie wijzigen** :

  ![database configureren](./media/service-tiers-vcore/configure-sql-database.png)

Selecteer de gewenste hardware-generatie:

  ![hardware selecteren](./media/service-tiers-vcore/select-hardware.png)


**Het genereren van de hardware van een bestaande SQL Database of groep wijzigen**

Voor een Data Base selecteert u op de pagina overzicht de **prijs categorie** koppeling:

  ![hardware wijzigen](./media/service-tiers-vcore/change-hardware.png)

Voor een groep selecteert u op de pagina overzicht de optie **configureren**.

Volg de stappen voor het wijzigen van de configuratie en selecteer de hardware-generatie zoals beschreven in de vorige stappen.

**Een hardwarematige generatie selecteren bij het maken van een SQL-beheerd exemplaar**

Zie [Create a SQL Managed instance](../managed-instance/instance-create-quickstart.md)(Engelstalig) voor meer informatie.

Selecteer op het tabblad **basis beginselen** de koppeling **Data Base configureren** in de sectie **berekenings** -en opslag en selecteer vervolgens gewenste hardwarematige generatie:

  ![SQL beheerd exemplaar configureren](./media/service-tiers-vcore/configure-managed-instance.png)
  
**Het genereren van de hardware van een bestaand SQL Managed instance wijzigen**

# <a name="the-azure-portal"></a>[Azure Portal](#tab/azure-portal)

Selecteer op de pagina SQL Managed instance de **prijs categorie** koppeling die in de sectie instellingen is geplaatst

![hardware voor SQL Managed instance wijzigen](./media/service-tiers-vcore/change-managed-instance-hardware.png)

Op de pagina **prijs categorie** kunt u de generatie van de hardware wijzigen, zoals beschreven in de vorige stappen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik het volgende PowerShell-script:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Raadpleeg de opdracht [set-AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstance) voor meer informatie.

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

Gebruik de volgende CLI-opdracht:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Raadpleeg [AZ SQL mi update](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-update) Command voor meer informatie.

---

### <a name="hardware-availability"></a>Hardwarematige Beschik baarheid

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a>Gen4/Gen5

Gen4-hardware wordt [gefaseerd uitgevoerd](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) en is niet meer beschikbaar voor nieuwe implementaties. Alle nieuwe data bases moeten worden geïmplementeerd op GEN5-hardware.

GEN5 is wereld wijd beschikbaar in de meeste regio's.

#### <a name="fsv2-series"></a>Fsv2-serie

Fsv2-serie is beschikbaar in de volgende regio's: Australië-centraal, Australië-centraal 2, Australië-oost, Australië-zuidoost, Brazilië-zuid, Canada-centraal, Azië-oost, VS-Oost, Frankrijk-centraal, India centraal, India-West, Korea-centraal, Korea-zuid, Europa-noord, Zuid-Afrika-noord, Zuidoost-Azië, UK-zuid, UK-west, Europa-west, VS-West 2.


#### <a name="m-series"></a>M-serie

De M-serie is beschikbaar in de volgende regio's: VS-Oost, Europa-noord, Europa-west, VS-West 2.
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan, raadpleegt u: 
- [Een SQL Database maken met behulp van de Azure Portal](single-database-create-quickstart.md)
- [Een door SQL beheerd exemplaar maken met behulp van de Azure Portal](../managed-instance/instance-create-quickstart.md)

Zie de pagina met prijzen voor [Azure SQL database](https://azure.microsoft.com/pricing/details/sql-database/single/)voor prijs informatie.

Zie voor meer informatie over de specifieke berekenings-en opslag grootten die beschikbaar zijn in de laag algemeen en bedrijfskritische service lagen:

- [vCore resource limieten voor Azure SQL database](resource-limits-vcore-single-databases.md).
- [vCore resource limieten voor gegroepeerde Azure SQL database](resource-limits-vcore-elastic-pools.md).
- [vCore op basis van resource limieten voor Azure SQL Managed instance](../managed-instance/resource-limits.md). 

