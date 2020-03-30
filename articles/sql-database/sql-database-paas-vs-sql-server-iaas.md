---
title: De juiste implementatieoptie kiezen
description: Meer informatie over het kiezen van de implementatieopties binnen Azure SQL tussen SQL-databases, SQL-beheerde exemplaren en SQL Server op Virtuele Azure-machines.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
keywords: SQL Server-cloud, SQL Server in de cloud, PaaS-database, SQL-cloudserver, DBaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/22/2019
ms.openlocfilehash: e642454807511e8e0bc0b6b6ca7af837e03de2cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73821355"
---
# <a name="choose-the-right-deployment-option-in-azure-sql"></a>De juiste implementatieoptie kiezen in Azure SQL

Ontdek hoe elke implementatieoptie past in het Azure SQL-gegevensplatform van Microsoft en krijg hulp bij het afstemmen van de juiste optie voor uw bedrijfsvereisten. Of u nu prioriteit geeft aan kostenbesparingen of minimale administratie, dit artikel kan u helpen beslissen welke aanpak levert tegen de zakelijke vereisten die u het meest bevalt.

## <a name="microsofts-azure-sql-data-platform"></a>Microsoft's Azure SQL-gegevensplatform

Van lift-and-shift migraties tot modernisering van bestaande toepassingen tot het bouwen van moderne cloudservices, Azure SQL is een modern SQL-platform dat verschillende implementatieopties biedt, aangedreven door een toonaangevende Microsoft SQL Server-engine. Azure SQL is ontworpen om een breed scala aan toepassingspatronen te ondersteunen met verschillende niveaus van controle over het onderliggende platform om te voldoen aan de meest veeleisende migratie- en moderniseringsvereisten. Azure SQL elimineert de complexiteit van het beheren van diverse verzamelingen SQL Server-gebaseerde toepassingen op schaal door één uniforme beheerervaring te bieden.

Een van de eerste belangrijke punten in een discussie over Azure ten opzichte van on-premises SQL Server-databases is dat u alles kunt gebruiken. Het dataplatform van Microsoft maakt gebruik van SQL Server-technologie en maakt het beschikbaar voor fysieke on-premises machines, private cloudomgevingen, externe gehoste private cloudomgevingen en de openbare cloud. SQL Server op Azure virtual machines (SQL virtual machines) stelt u in staat om te voldoen aan unieke en uiteenlopende zakelijke behoeften door een combinatie van on-premises en cloud-hosted implementaties, terwijl u dezelfde set serverproducten, ontwikkelingstools en expertise gebruikt in deze omgevingen.

   ![Opties SQL Server cloud: SQL server op IaaS of SaaS-SQL Database in de cloud.](./media/sql-database-paas-vs-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Zoals te zien in het diagram, kan elk aanbod worden gekenmerkt door het niveau van administratie die u hebt over de infrastructuur, en door de mate van kostenefficiëntie.

In Azure u uw SQL Server-workloads laten uitvoeren als een gehoste service[(PaaS)](https://azure.microsoft.com/overview/what-is-paas/)of een gehoste infrastructuur[(IaaS).](https://azure.microsoft.com/overview/what-is-iaas/) Binnen PaaS hebt u meerdere implementatieopties en servicelagen binnen elke implementatieoptie. De belangrijkste vraag die u moet stellen bij de keuze tussen PaaS of IaaS is, wilt u uw database beheren, patches toepassen en back-ups maken, of wilt u deze bewerkingen delegeren aan Azure?

Afhankelijk van het antwoord hebt u de volgende opties:

- [**SQL-databases:**](sql-database-technical-overview.md)Het beste voor moderne cloudtoepassingen die de nieuwste stabiele SQL Server-functies willen gebruiken en tijdsbeperkingen hebben in ontwikkeling en marketing. Een volledig beheerde SQL-databaseengine, gebaseerd op de nieuwste stabiele Enterprise Edition van SQL Server. Dit is een relationele database-as-a-service (DBaaS) gehost in de Azure cloud die valt in de industrie categorie van *Platform-as-a-Service (PaaS)*. SQL-database heeft meerdere implementatieopties, die elk zijn gebouwd op gestandaardiseerde hardware en software die eigendom zijn van, gehost en onderhouden worden door Microsoft. Met SQL Server u ingebouwde functies en functionaliteit gebruiken die uitgebreide configuratie vereisen (on-premises of in een virtuele Azure-machine). Als u SQL Database gebruikt, betaalt u per gebruik met opties voor het omhoog schalen voor meer vermogen zonder onderbrekingen. SQL Database heeft een aantal extra functies die niet beschikbaar zijn in SQL Server, zoals ingebouwde hoge beschikbaarheid, intelligentie en beheer.


  Databases bieden de volgende implementatieopties:
  - Als [***een enkele database***](sql-database-single-database.md) met een eigen set van middelen beheerd via een database server. Eén database is vergelijkbaar met een [opgenomen database](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) in SQL Server. Deze optie is geoptimaliseerd voor de moderne applicatie-ontwikkeling van nieuwe cloud-born applicaties. [Hyperscale](sql-database-service-tier-hyperscale.md) en [serverless](sql-database-serverless.md) opties zijn beschikbaar.
  - Een [***elastische pool***](sql-database-elastic-pool.md), een verzameling databases met een gedeelde set resources die worden beheerd via een databaseserver. Afzonderlijke databases kunnen worden verplaatst naar en uit een elastische pool. Deze optie is geoptimaliseerd voor de moderne applicatie-ontwikkeling van nieuwe cloud-born applicaties met behulp van de multi-tenant SaaS applicatie patroon. Elastische pools bieden een kosteneffectieve oplossing voor het beheren van de prestaties van meerdere databases met variabele gebruikspatronen.
  - Een [***databaseserver***](sql-database-servers.md)die wordt gebruikt om groepen van afzonderlijke databases en elastische pools te beheren. Databaseservers fungeren als een centraal administratief punt voor meerdere afzonderlijke of gepoolde databases, [aanmeldingen,](sql-database-manage-logins.md) [firewallregels,](sql-database-firewall-configure.md) [controleregels](sql-database-auditing.md), [beleid voor bedreigingsdetectie](sql-database-threat-detection.md)en [failovergroepen](sql-database-auto-failover-group.md).

- [**SQL-beheerde exemplaren**](sql-database-managed-instance.md): Het beste voor de meeste migraties naar de cloud. Beheerde instantie is een verzameling systeem- en gebruikersdatabases met een gedeelde set resources die klaar is voor lift-and-shift. Het beste voor nieuwe toepassingen of bestaande on-premises toepassingen die de nieuwste stabiele SQL Server-functies willen gebruiken en die met minimale wijzigingen naar de cloud worden gemigreerd. Een beheerde instantie is vergelijkbaar met een instantie van de [Microsoft SQL Server-databaseengine](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview) die gedeelde bronnen voor databases en aanvullende functies met een instantie biedt. Beheerde instantie ondersteunt databasemigratie van on-premises met minimale tot geen databasewijziging. Deze optie biedt alle PaaS-voordelen van Azure SQL Database, maar voegt mogelijkheden toe die voorheen alleen beschikbaar waren in SQL VM's. Dit omvat een native virtueel netwerk (VNet) en bijna 100% compatibiliteit met on-premises SQL Server. Beheerde exemplaren bieden volledige SQL Server-toegang en functiecompatibiliteit voor het migreren van SQL-servers naar Azure.


- [**SQL virtuele machines:**](../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)Het beste voor migraties en toepassingen die toegang op OS-niveau vereisen. SQL virtuele machines zijn lift-and-shift klaar voor bestaande toepassingen die snelle migratie naar de cloud vereisen met minimale wijzigingen of geen wijzigingen. SQL virtuele machines bieden volledige administratieve controle over de SQL Server-instantie en het onderliggende besturingssysteem voor migratie naar Azure. Snelle ontwikkelings- en testscenario's als u geen on-premises SQL Server-hardware wilt kopen die niet bestemd is voor productie. SQL virtuele machines vallen in de industriecategorie *Infrastructure-as-a-Service (IaaS)* en stelt u in staat sql server uit te voeren in een volledig beheerde virtuele machine (VM) in de Azure-cloud. SQL virtuele machines draaien ook op gestandaardiseerde hardware die eigendom is van, gehost en onderhouden door Microsoft. Wanneer u SQL-virtuele machines gebruikt, u betalen per gebruik voor een SQL Server-licentie die al in een SQL Server-afbeelding is opgenomen, of eenvoudig een bestaande licentie gebruiken. U de VM ook stoppen of hervatten als dat nodig is. SQL Server die is geïnstalleerd en gehost in de cloud, draait op virtuele Windows Server- of Linux-machines die op Azure worden uitgevoerd, ook wel een infrastructure as a service (IaaS) genoemd. SQL virtuele machines is een goede optie voor het migreren van on-premises SQL Server databases en applicaties zonder enige database wijziging. Alle recente versies en edities van SQL Server zijn beschikbaar voor installatie in een IaaS virtuele machine. Het belangrijkste verschil met SQL-databases en SQL-beheerde exemplaren is dat SQL Server VM's volledige controle over de databaseengine mogelijk maken. U kiezen wanneer u onderhoud/patching wilt starten, het herstelmodel wijzigen in eenvoudig of bulk-ingelogd, de service onderbreken of starten wanneer dat nodig is, en u de SQL Server-databaseengine volledig aanpassen. Met deze extra controle komt de extra verantwoordelijkheid om de virtuele machine te beheren.

  Geoptimaliseerd voor het migreren van bestaande toepassingen naar Azure of het uitbreiden van bestaande on-premises toepassingen naar de cloud in hybride implementaties. Bovendien kunt u SQL Server op een virtuele machine gebruiken om traditionele SQL Server-toepassingen te ontwikkelen en te testen. Met VIRTUELE SQL-machines hebt u de volledige beheerdersrechten ten opzichte van een specifiek SQL Server-exemplaar en een vm in de cloud. Het is een ideale keuze als een organisatie al IT-bronnen beschikbaar heeft voor het onderhouden van de virtuele machines. Met deze mogelijkheden kunt u een aangepast systeem bouwen voor de specifieke prestaties en beschikbaarheidsvereisten van uw toepassing.


In de volgende tabel worden extra verschillen vermeld, maar ***zowel databases als beheerde instanties zijn geoptimaliseerd om de totale beheerkosten tot een minimum te beperken voor het inrichten en beheren van veel databases.*** Dit vermindert beheerkosten omdat u geen virtuele machines, besturingssysteem of database-software hoeft te beheren. U hoeft upgrades, hoge beschikbaarheid of [back-ups](sql-database-automated-backups.md) niet te beheren. Over het algemeen kan Azure SQL Database het aantal databases dat wordt beheerd door één IT of ontwikkelingsresource aanzienlijk verhogen. [Elastische pools](sql-database-elastic-pool.md) ondersteunen ook SaaS multi-tenant applicatiearchitecturen met functies zoals tenantisolatie en de mogelijkheid om te schalen om kosten te verlagen door resources te delen in databases. [Beheerde instantie](sql-database-managed-instance.md) biedt ondersteuning voor functies met een voorbeeldbereik waarmee bestaande toepassingen eenvoudig kunnen worden gemigratie en resources tussen databases kunnen worden gedeeld.

| SQL-databases | Beheerde SQL-exemplaren | VIRTUELE SQL-machines |
| :--- | :--- | :--- |
|Ondersteunt de meeste on-premises mogelijkheden op databaseniveau. De meest gebruikte SQL Server-functies zijn beschikbaar.<br/>99.995% beschikbaarheid gegarandeerd.<br/>Ingebouwde back-ups, patching, herstel.<br/>Nieuwste stabiele Database Engine versie.<br/>Mogelijkheid om de benodigde resources (CPU/opslag) toe te wijzen aan afzonderlijke databases.<br/>Ingebouwde geavanceerde intelligentie en beveiliging.<br/>Online verandering van resources (CPU/opslag).| Ondersteunt bijna alle on-premises mogelijkheden op instantie- en databaseniveau. Hoge compatibiliteit met sql server on-premises.<br/>99,99% beschikbaarheid gegarandeerd.<br/>Ingebouwde back-ups, patching, herstel.<br/>Nieuwste stabiele Database Engine versie.<br/>Eenvoudige migratie van SQL Server.<br/>Privé-IP-adres binnen Azure VNet.<br/>Ingebouwde geavanceerde intelligentie en beveiliging.<br/>Online verandering van resources (CPU/opslag).| U hebt volledige controle over de SQL Server-engine. Ondersteunt alle on-premises mogelijkheden.<br/>Tot 99,99% beschikbaarheid.<br/>Volledige pariteit met de overeenkomende versie van on-premises SQL Server.<br/>Vaste, bekende database engine versie.<br/>Eenvoudige migratie van SQL Server on-premises.<br/>Privé-IP-adres binnen Azure VNet.<br/>U toepassingen of services implementeren op de host waar SQL Server is geplaatst.|
|Migratie van SQL Server kan moeilijk zijn.<br/>Sommige SQL Server-functies zijn niet beschikbaar.<br/>Geen gegarandeerde exacte onderhoudstijd (maar bijna transparant).<br/>Compatibiliteit met de SQL Server-versie kan alleen worden bereikt met behulp van databasecompatibiliteitsniveaus.<br/>Privé-IP-adres kan niet worden toegewezen (u de toegang beperken met behulp van firewallregels).|Er is nog steeds een minimaal aantal SQL Server-functies die niet beschikbaar zijn.<br/>Geen gegarandeerde exacte onderhoudstijd (maar bijna transparant).<br/>Compatibiliteit met de SQL Server-versie kan alleen worden bereikt met behulp van databasecompatibiliteitsniveaus.|U moet uw back-ups en patches beheren.<br>U moet uw eigen oplossing met hoge beschikbaarheid implementeren.<br/>Er is een downtime terwijl de resources (CPU/opslag) worden gewijzigd|
| Databases tot 100 TB. | Tot 8 TB. | SQL Server-exemplaren met maximaal 256 TB opslagruimte. Het exemplaar kan zoveel databases ondersteunen als nodig is. |
| On-premises toepassing heeft toegang tot gegevens in Azure SQL Database. | [Native virtuele netwerkimplementatie](sql-database-managed-instance-vnet-configuration.md) en connectiviteit met uw on-premises omgeving met Azure Express Route of VPN Gateway. | Met SQL virtuele machines u toepassingen hebben die deels in de cloud en deels on-premises worden uitgevoerd. Zo kunt u uw on-premises netwerk en Active Directory-domein uitbreiden naar de cloud met [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Zie [On-premises dataoplossingen uitbreiden naar de cloud voor](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud)meer informatie over hybride cloudoplossingen. |




## <a name="business-motivations-for-choosing-databases-managed-instances-or-sql-virtual-machines"></a>Zakelijke motivaties voor het kiezen van databases, beheerde exemplaren of SQL virtuele machines

Er zijn verschillende factoren die van invloed kunnen zijn op uw beslissing om te kiezen tussen de verschillende gegevensaanbiedingen:

- [Kosten](#cost) - Zowel PaaS als IaaS-optie omvatten de basisprijs die de onderliggende infrastructuur en licenties dekt. Met de IaaS-optie moet u echter extra tijd en middelen investeren om uw database te beheren, terwijl u in PaaS deze beheerfuncties in de prijs krijgt. Met de IaaS-optie u uw resources afsluiten terwijl u ze niet gebruikt om de kosten te verlagen, terwijl de PaaS-versie altijd wordt uitgevoerd, tenzij u uw resources opnieuw laat vallen en opnieuw maakt wanneer ze nodig zijn.
- [Beheer](#administration) - PaaS-opties verminderen de hoeveelheid tijd die u moet investeren om de database te beheren. Het beperkt echter ook het bereik van aangepaste beheertaken en scripts die u uitvoeren of uitvoeren. De CLR wordt bijvoorbeeld niet ondersteund met afzonderlijke of gepoolde databases, maar wordt ondersteund voor een beheerde instantie. Ook ondersteunen geen implementatieopties in PaaS het gebruik van traceflags.
- [Service-Level Agreement](#service-level-agreement-sla) - Zowel IaaS als PaaS bieden hoge, industriestandaard SLA. PaaS-optie garandeert 99,99% SLA, terwijl IaaS 99,95% SLA garandeert voor infrastructuur, wat betekent dat u extra mechanismen moet implementeren om de beschikbaarheid van uw databases te garanderen. U een oplossing met hoge beschikbaarheid implementeren voor 99,99% door een extra SQL Server in VM te maken en AlwaysOn-beschikbaarheidsgroepen te configureren.
- [Tijd om over te stappen naar Azure](#market) - SQL Server in Azure VM is de exacte overeenkomst van uw omgeving, dus migratie van on-premises naar Azure SQL VM is niet anders dan het verplaatsen van de databases van de ene on-premises server naar de andere. Beheerde instantie maakt ook zeer eenvoudige migratie mogelijk; Er kunnen echter enkele wijzigingen zijn die u moet toepassen voordat u migreert naar een beheerde instantie.

Deze factoren zullen in de volgende secties nader worden besproken.

### <a name="cost"></a>Kosten

Of u nu een  startup bent die geld nodig heeft of een team in een goedlopend bedrijf met een krap budget, beperkte middelen zijn vaak de belangrijkste reden bij het bepalen hoe u uw databases wilt hosten. In deze sectie leert u over de basisprincipes voor facturering en licenties in Azure met betrekking tot deze twee relationele databaseopties: SQL Database en SQL virtual machines. Ook leest u over hoe u de totale kosten voor de toepassing berekent.

#### <a name="billing-and-licensing-basics"></a>Grondbeginselen facturering en licenties

Momenteel wordt **SQL Database** verkocht als een service en is beschikbaar met verschillende implementatieopties en in verschillende servicelagen met verschillende prijzen voor resources, die allemaal per uur worden gefactureerd tegen een vast tarief op basis van de servicelaag en rekengrootte die u kiest. Zie [DTU-gebaseerd inkoopmodel](sql-database-service-tiers-dtu.md) en [vCore-gebaseerd inkoopmodel](sql-database-service-tiers-vcore.md)voor de meest recente ondersteunde servicelagen, rekengroottes en opslagbedragen.

- Met SQL-database u een servicelaag kiezen die aan uw behoeften voldoet aan een breed scala aan prijzen vanaf 5$ per maand voor de basislaag.
- U [elastische groepen](sql-database-elastic-pool.md) maken om resources te delen tussen database-exemplaren om de kosten te verlagen en gebruikspieken op te vangen.
- Met SQL managed instance u ook uw eigen licentie meenemen. Zie License Mobility via Software Assurance [op Azure](https://azure.microsoft.com/pricing/license-mobility/) of gebruik Azure Hybrid Benefit [calculator](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) om te zien hoe u **tot 40% besparen**voor meer informatie over bring-your-own licenties.

Bovendien wordt uitgaand internetverkeer in rekening gebracht bij u tegen het reguliere [tarief voor gegevensoverdracht](https://azure.microsoft.com/pricing/details/data-transfers/). U servicelagen en rekenformaten dynamisch aanpassen aan de uiteenlopende doorvoerbehoeften van uw toepassing.

Met **SQL-databases en SQL-beheerde exemplaren**wordt de databasesoftware automatisch geconfigureerd, gepatcht en geüpgraded door Microsoft, wat uw administratiekosten verlaagt. Bovendien kunt u met de [ingebouwde back-up](sql-database-automated-backups.md)mogelijkheden  aanzienlijk kosten besparen, vooral wanneer u een groot aantal databases hebt.

Met **SQL virtuele machines**u een van de door het platform geleverde SQL Server-afbeeldingen (waaronder een licentie) gebruiken of uw SQL Server-licentie meenemen. Alle ondersteunde SQL Server-versies (2008R2, 2012, 2014, 2016) en edities (Developer, Express, Web, Standard, Enterprise) zijn beschikbaar. Bovendien zijn er Bring Your Own License-versies (BYOL) van de installatiekopieën beschikbaar. Wanneer u de door Azure geleverde installatiekopieën gebruikt, zijn de operationele kosten afhankelijk van de VM-grootte en de versie van SQL Server die u kiest. Ongeacht vm-grootte of SQL Server-editie betaalt u per minuut licentiekosten van SQL Server en de Windows- of Linux-server, samen met de Azure Storage-kosten voor de VM-schijven. Met de optie factureren per minuut kunt u SQL Server blijven gebruiken zonder aanvullende SQL Server-licenties te kopen. Als u uw eigen SQL Server-licentie naar Azure brengt, worden alleen server- en opslagkosten in rekening gebracht. Zie voor meer informatie over meenemen van uw eigen licentie [License Mobility through Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/). Bovendien wordt uitgaand internetverkeer in rekening gebracht bij u tegen het reguliere [tarief voor gegevensoverdracht](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>De totale kosten voor de toepassing berekenen

Wanneer u een cloudplatform gaat gebruiken, zijn de kosten voor het uitvoeren van uw toepassing inclusief de kosten voor nieuwe ontwikkeling en doorlopende administratiekosten, plus de servicekosten voor openbare cloudplatforms.

Zie de volgende bronnen voor meer informatie over prijzen:

- [PRIJZEN voor SQL-database](https://azure.microsoft.com/pricing/details/sql-database/)
- [Prijzen voor virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/) voor [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) en voor [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Azure prijscalculator](https://azure.microsoft.com/pricing/calculator/)

### <a name="administration"></a>Beheer

Veel bedrijven besluiten over te stappen naar een cloudservice zowel vanwege offloading van beheercomplexiteit als de kosten. Met IaaS en PaaS beheert Microsoft de onderliggende infrastructuur en repliceert het automatisch alle gegevens om disaster recovery te bieden, configureert en upgradet het de databasesoftware, beheert het load balancing en doet het transparante failover als er een serverstoring in een datacenter.

- Met **SQL-databases en SQL-beheerde exemplaren**u uw database blijven beheren, maar hoeft u de databaseengine, het besturingssysteem of de hardware niet langer te beheren. Voorbeelden van items die u kunt blijven beheren zijn onder meer databases en aanmeldingen, index en query afstemmen, en controle en beveiliging.  Bovendien vereist het configureren van hoge beschikbaarheid voor een ander datacenter minimale configuratie en beheer.
- Met **SQL virtuele machines**hebt u volledige controle over het besturingssysteem en sql server-instantieconfiguratie. Met een VM kunt u zelf bepalen wanneer een upgrade van het besturingssysteem en de databasesoftware nodig is en wanneer u aanvullende software zoals antivirusprogramma's moet installeren. Sommige geautomatiseerde functies worden geleverd om patchen, het maken van een back-up en hoge beschikbaarheid aanzienlijk te vereenvoudigen. Bovendien kunt u de grootte van de VM, het aantal schijven en hun opslagconfiguraties beheren. Met Azure kunt de grootte van een VM indien nodig wijzigen. Zie voor meer informatie [Virtual Machine en Cloud Service Sizes for Azure](../virtual-machines/windows/sizes.md).

### <a name="service-level-agreement-sla"></a>Service Level Agreement (SLA)

Voor veel IT-afdelingen is het voldoen aan uptimeverplichtingen van een Service Level Agreement (SLA) een topprioriteit. In dit gedeelte kijken we welke SLA van toepassing is op de databasehostingopties.

Voor **SQL Database**biedt Microsoft een beschikbaarheidsla van 99,99%. Zie [Service Level Agreement](https://azure.microsoft.com/support/legal/sla/sql-database/) voor de meest recente informatie.

Voor **VIRTUELE SQL-machines**biedt Microsoft een beschikbaarheidsSLA van 99,95% die alleen de virtuele machine dekt. Deze SLA heeft geen betrekking op de processen (zoals SQL Server) die worden uitgevoerd op de VM en vereist dat u ten minste twee exemplaren van de virtuele machine in een beschikbaarheidsset host. Voor de meest recente informatie, zie de [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Voor HA(Database high availability) binnen VM's moet u een van de ondersteunde opties voor hoge beschikbaarheid in SQL Server configureren, zoals [Always On Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Het gebruik van een ondersteunde optie voor hoge beschikbaarheid biedt geen extra SLA, maar u kunt wel een databasebeschikbaarheid van >99,99% behalen.

### <a name="time-to-move-to-azure"></a><a name="market"></a>Tijd om over te stappen naar Azure

**SQL-database** (single databases of elastic pools) zijn de juiste oplossing voor cloud-ontworpen toepassingen wanneer de productiviteit van ontwikkelaars en snelle time-to-market voor nieuwe oplossingen van cruciaal belang zijn. Met programmatische DBA-functies is het ideaal voor cloud-architecten en -ontwikkelaars aangezien het de noodzaak voor het beheren van het onderliggende besturingssysteem en de database vermindert.

**SQL managed instance** vereenvoudigt de migratie van bestaande toepassingen naar Azure SQL sterk, zodat u gemigreerde databasetoepassingen snel op de markt brengen in Azure.

**SQL virtuele machines** zijn perfect als uw bestaande of nieuwe toepassingen grote databases of toegang tot alle functies in SQL Server of Windows/Linux vereisen, en u de tijd en kosten van het verwerven van nieuwe on-premises hardware wilt vermijden. Het is ook een goede pasvorm wanneer u bestaande on-premises toepassingen en databases wilt migreren naar Azure as-is - in gevallen waarin Azure SQL Database beheerde instantie niet geschikt is. Omdat u de presentatie-, toepassings- en gegevenslagen niet hoeft te wijzigen, bespaart u tijd en budget bij het opnieuw ontwerpen van uw bestaande oplossing. In plaats daarvan kunt u zich richten op het migreren van uw oplossingen naar Azure en het uitvoeren van prestatieoptimalisatie die mogelijk wordt vereist door het Azure-platform. Zie voor meer informatie [Best practices prestaties for SQL Server on Azure Virtual Machines](../virtual-machines/windows/sql/virtual-machines-windows-sql-performance.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Volgende stappen

- Zie [uw eerste Azure SQL-database](sql-database-single-database-get-started.md) om aan de slag te gaan met SQL Database.
- Zie [Prijzen van SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
- Zie [Provision a SQL Server virtual machine in Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md) om aan de slag te kunnen met SQL Server op Azure VM’s.
- [Identificeer de juiste SQL-database of SQL-beheerde instantie SKU voor uw on-premises database.](/sql/dma/dma-sku-recommend-sql-db/)
