---
title: Wat is Azure SQL?
titleSuffix: " "
description: 'Meer informatie over de verschillende opties in de Azure SQL-familie van services: Azure SQL Database, Azure SQL Managed instance en SQL Server op Azure VM.'
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
keywords: SQL Server Cloud, SQL Server in de Cloud, PaaS-data base, Cloud SQL Server, DBaaS, IaaS
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/27/2020
ms.openlocfilehash: 1ad7dc2452cdc2756413db1a07da74e2a1dc3fcd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84344625"
---
# <a name="what-is-azure-sql"></a>Wat is Azure SQL? 
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

Azure SQL is een familie van beheerde, veilige en intelligente producten die gebruikmaken van de SQL Server data base-engine in de Azure-Cloud.

- **Azure SQL database**: ondersteuning voor moderne Cloud toepassingen op een intelligente, beheerde database service, waaronder serverloze reken kracht. 
- **Azure SQL Managed instance**: modern uw bestaande SQL Server-toepassingen op schaal met een intelligent volledig beheerd exemplaar als een service, met bijna 100% van de functie pariteit met de SQL server-data base-engine. Het meest geschikt voor de meeste migraties naar de Cloud.
- **SQL Server op virtuele machines van Azure**: til de SQL Server-workloads met 100 gemak en houd de SQL Server compatibiliteit en toegang tot het toegangs niveau van het besturings systeem bij. 
 
Azure SQL is gebaseerd op de vertrouwde SQL Server-engine, zodat u toepassingen eenvoudig kunt migreren en de hulp middelen, talen en bronnen die u kent, blijven gebruiken. Uw vaardig heden en ervaring worden overgedragen naar de Cloud, zodat u nog meer kunt doen met wat u al hebt. 

Ontdek hoe elk product in het Azure SQL-gegevens platform van micro soft past, zodat dit overeenkomt met de juiste optie voor uw zakelijke vereisten. Of u de kosten besparingen of het minimale beheer wilt priori teren, u kunt dit artikel helpen bij het bepalen van de aanpak van de bedrijfs vereisten die u het belangrijkst vindt.


## <a name="overview"></a>Overzicht

In de huidige gegevensgestuurde wereld is het bevorderen van digitale trans formatie in toenemende mate afhankelijk van de mogelijkheid om enorme hoeveel heden gegevens te beheren en de mogelijkheden ervan te benutten. De huidige gegevens zijn echter steeds complexer, met gegevens die on-premises, in de Cloud of aan de rand van het netwerk worden gehost. Ontwikkel aars die intelligente en ingrijpende toepassingen bouwen, kunnen zichzelf ondervinden op beperkingen die uiteindelijk van invloed kunnen zijn op hun ervaring. Beperkingen die voortvloeien uit incompatibele platforms, onvoldoende gegevens beveiliging, ontoereikende bronnen en prijs prestatie barrières maken complexiteit waardoor de modernisering en ontwikkeling van apps kunnen worden belemmerd. 

Een van de eerste belangrijke punten in een discussie over Azure ten opzichte van on-premises SQL Server-databases is dat u alles kunt gebruiken. Het gegevens platform van micro soft maakt gebruik van SQL Server technologie en is beschikbaar via fysieke on-premises machines, particuliere cloud omgevingen, gehoste privé-Cloud omgevingen van derden en de open bare Cloud. 


### <a name="fully-managed-and-always-up-to-date"></a>Volledig beheerd en altijd up-to-date 

Besteed meer tijd aan innoveren en minder tijd om uw data bases te patchen, bij te werken en een back-up te maken. Azure is de enige Cloud met een blijvende SQL-versie waarmee automatisch de meest recente updates en patches worden toegepast, zodat uw data bases altijd up-to-date zijn, waardoor het einde van de ondersteunings problemen wordt voor komen. Zelfs complexe taken, zoals prestaties afstemmen, hoge Beschik baarheid, herstel na nood gevallen en back-ups, zijn geautomatiseerd, zodat u zich kunt richten op toepassingen.  

### <a name="protect-your-data-with-built-in-intelligent-security"></a>Bescherm uw gegevens met ingebouwde intelligente beveiliging 

Azure bewaakt uw gegevens voortdurend op bedreigingen. Met Azure SQL kunt u het volgende doen:

- Mogelijke bedreigingen in realtime herstellen met intelligente [geavanceerde bedreigingen detectie](https://docs.microsoft.com/azure/security/fundamentals/threat-detection#advanced-threat-detection-features-other-azure-services) en evaluatie van proactief beveiligings problemen. 
- Profiteer van toonaangevende, op meerdere lagen gebaseerde beveiliging met [ingebouwde beveiligings](https://azure.microsoft.com/overview/security/) maatregelen, waaronder T-SQL, authenticatie, netwerken en sleutel beheer. 
- Profiteer van de meest uitgebreide [nalevings](https://azure.microsoft.com/overview/trusted-cloud/compliance/) dekking van elke Cloud database service. 


### <a name="business-motivations"></a>Zakelijke motivaties

Er zijn verschillende factoren die van invloed kunnen zijn op uw beslissing om te kiezen tussen de verschillende gegevens aanbiedingen:

- [Kosten](#cost): zowel de Paas-als de IaaS-optie zijn basis prijs die betrekking heeft op de onderliggende infra structuur en licenties. Met de optie IaaS moet u echter extra tijd en resources investeren om uw data base te beheren, terwijl u in PaaS deze beheer functies in de prijs hebt opgenomen. Met IaaS kunt u resources afsluiten terwijl u ze niet gebruikt om de kosten te verlagen, terwijl PaaS altijd wordt uitgevoerd, tenzij u uw resources verwijdert en opnieuw maakt wanneer dat nodig is.
- [Beheer](#administration): PaaS opties verminderen de hoeveelheid tijd die u moet investeren om de data base te beheren. Het kan echter ook het bereik van aangepaste beheer taken en scripts beperken dat u kunt uitvoeren of uitvoeren. De CLR wordt bijvoorbeeld niet ondersteund met één of gegroepeerde Data Base, maar wordt wel ondersteund voor een exemplaar van een SQL-beheerd exemplaar. Daarnaast bieden geen implementatie opties in PaaS ondersteuning voor het gebruik van tracerings vlaggen.
- [Service overeenkomst](#service-level-agreement-sla): zowel IaaS als PaaS bieden een hoge, industrie standaard sla. De PaaS-optie garandeert een SLA van 99,99%, terwijl IaaS 99,95% SLA voor infra structuur garandeert, wat betekent dat u extra mechanismen moet implementeren om de beschik baarheid van uw data bases te garanderen. U kunt 99,99% SLA bereiken door een extra virtuele SQL-machine te maken en de oplossing voor de hoge Beschik baarheid van de SQL Server-groep altijd te implementeren. 
- [Tijd om over te stappen op Azure](#market): SQL Server op de virtuele machine van Azure is het exacte resultaat van uw omgeving, dus migratie van on-premises naar de Azure-VM is niet anders dan het verplaatsen van de data bases van een lokale server naar een andere. Met SQL Managed Instance kunt u ook eenvoudige migratie; Er zijn echter enkele wijzigingen die u moet Toep assen vóór de migratie. 


## <a name="service-comparison"></a>Servicevergelijking

   ![Opties voor Cloud SQL Server: SQL Server op IaaS of SaaS SQL Database in de Cloud.](./media/azure-sql-iaas-vs-paas-what-is-overview/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Zoals u in het diagram kunt zien, kan elke service aanbieding worden gekenmerkt door het beheer niveau dat u over de infra structuur hebt en op de mate van kosten efficiëntie.

In azure kunt u uw SQL Server-workloads uitvoeren als een gehoste service ([PaaS](https://azure.microsoft.com/overview/what-is-paas/)) of een gehoste infra structuur ([IaaS](https://azure.microsoft.com/overview/what-is-iaas/)). Binnen PaaS hebt u meerdere product opties en service lagen binnen elke optie. De belang rijke vraag die u moet stellen bij het kiezen tussen PaaS of IaaS is dat u uw Data Base wilt beheren, patches wilt Toep assen en back-ups wilt maken, of dat u deze bewerkingen wilt delegeren naar Azure?

### <a name="azure-sql-database"></a>Azure SQL Database

[Azure SQL database](database/sql-database-paas-overview.md) is een relationele data base-as-a-Service (DBaaS) die wordt gehost in Azure en die in de branche categorie van *platform-as-a-Service (PaaS)* valt. 
- Het beste voor moderne Cloud toepassingen die de nieuwste stabiele SQL Server functies willen gebruiken en tijd beperkingen hebben in ontwikkeling en marketing. 
- Een volledig beheerde SQL Server data base-engine, gebaseerd op de nieuwste stabiele Enter prise-editie van SQL Server. SQL Database heeft twee implementatie opties die zijn gebaseerd op gestandaardiseerde hardware en software die eigendom is van, worden gehost en onderhouden door micro soft. 

Met SQL Server kunt u ingebouwde functies en functionaliteit gebruiken die een uitgebreide configuratie vereist (on-premises of op een virtuele machine van Azure). Als u SQL Database gebruikt, betaalt u per gebruik met opties voor het omhoog schalen voor meer vermogen zonder onderbrekingen. SQL Database heeft een aantal extra functies die niet beschikbaar zijn in SQL Server, zoals ingebouwde hoge Beschik baarheid, intelligentie en beheer.


Azure SQL Database biedt de volgende implementatie opties:
  - Als [*één data base*](database/single-database-overview.md) met een eigen set resources die worden beheerd via een [logische SQL-Server](database/logical-servers.md). Een enkele data base is vergelijkbaar met een [Inge sloten data base](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases) in SQL Server. Deze optie is geoptimaliseerd voor de ontwikkeling van moderne toepassingen van nieuwe Cloud toepassingen. Opties voor [grootschalige](database/service-tier-hyperscale.md) en [serverloos](database/serverless-tier-overview.md) zijn beschikbaar.
  - Een [*elastische pool*](database/elastic-pool-overview.md), een verzameling data bases met een gedeelde set resources die worden beheerd via een [logische SQL-Server](database/logical-servers.md). U kunt afzonderlijke data bases naar en uit een elastische pool verplaatsen. Deze optie is geoptimaliseerd voor de ontwikkeling van moderne toepassingen van nieuwe Cloud toepassingen met behulp van het SaaS-toepassings patroon met meerdere tenants. Elastische Pools bieden een kosteneffectieve oplossing voor het beheren van de prestaties van meerdere data bases met variabele gebruiks patronen.

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

[Azure SQL Managed instance](managed-instance/sql-managed-instance-paas-overview.md) valt onder de branche categorie van *platform-as-a-Service (PaaS)* en is het meest geschikt voor de meeste migratie naar de Cloud. SQL Managed instance is een verzameling systeem-en gebruikers databases met een gedeelde set resources die gereed is voor lift-en SHIFT.  
- Het meest geschikt voor nieuwe toepassingen of bestaande on-premises toepassingen die de nieuwste stabiele SQL Server functies willen gebruiken en die worden gemigreerd naar de Cloud met minimale wijzigingen. Een exemplaar van een SQL-beheerd exemplaar is vergelijkbaar met een exemplaar van de [Microsoft SQL server data base-engine](https://docs.microsoft.com/sql/database-engine/sql-server-database-engine-overview) die gedeelde bronnen biedt voor data bases en aanvullende functies met een exemplaar bereik. 
- SQL Managed instance ondersteunt database migratie van on-premises met minimale wijziging van de data base. Deze optie biedt alle PaaS-voor delen van Azure SQL Database, maar voegt mogelijkheden toe die eerder alleen beschikbaar waren in SQL Server Vm's. Dit omvat een systeem eigen virtueel netwerk en een bijna 100% compatibiliteit met on-premises SQL Server. Exemplaren van SQL Managed instance bieden volledige SQL Server toegang en functie compatibiliteit voor het migreren van SQL-servers naar Azure.

### <a name="sql-server-on-azure-vm"></a>SQL Server op virtuele Azure-machine

[SQL Server op de virtuele machine van Azure](virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md) valt in de branche categorie *Infrastructure-as-a-Service (IaaS)* en biedt u de mogelijkheid om SQL Server uit te voeren in een volledig beheerde virtuele machines (VM) in Azure. 
- Het meest geschikt voor migraties en toepassingen waarvoor toegang op besturingssysteem niveau is vereist. Virtuele SQL-machines in azure zijn lift-en Shift-klaar voor bestaande toepassingen waarvoor snelle migratie naar de Cloud met minimale wijzigingen of geen wijzigingen nodig is. Virtuele SQL-machines bieden volledige administratieve controle over het SQL Server-exemplaar en het onderliggende besturings systeem voor migratie naar Azure. 
- Snelle ontwikkelings- en testscenario's als u geen on-premises SQL Server-hardware wilt kopen die niet bestemd is voor productie. Virtuele SQL-machines worden ook uitgevoerd op gestandaardiseerde hardware die eigendom is van, wordt gehost en wordt beheerd door micro soft. Bij het gebruik van virtuele SQL-machines kunt u betalen naar gebruik voor een SQL Server licentie die al is opgenomen in een SQL Server installatie kopie of kunt u eenvoudig een bestaande licentie gebruiken. U kunt ook de virtuele machine stoppen of hervatten als dat nodig is. 
- SQL Server geïnstalleerd en gehost in de Cloud, wordt uitgevoerd op virtuele Windows Server-of Linux-machines die worden uitgevoerd op Azure, ook wel een IaaS (Infrastructure as a Service) genoemd. Virtuele SQL-machines zijn een goede optie voor het migreren van on-premises SQL Server data bases en toepassingen zonder een Data Base te wijzigen. Alle recente versies en versies van SQL Server zijn beschikbaar voor installatie in een virtuele IaaS-machine. 

    Het belangrijkste verschil van SQL Database en SQL Managed instance is dat SQL Server op Azure Virtual Machines volledige controle over de data base-engine toestaat. U kunt kiezen wanneer u onderhoud/patching wilt starten, het herstel model wilt wijzigen in eenvoudig of bulksgewijs te registreren, de service te onderbreken of te starten wanneer dit nodig is. u kunt de SQL Server data base-engine volledig aanpassen. Met dit extra besturings element krijgt u de verantwoordelijkheid om de virtuele machine te beheren.
- Geoptimaliseerd voor het migreren van bestaande toepassingen naar Azure of het uitbreiden van bestaande on-premises toepassingen naar de cloud in hybride implementaties. Bovendien kunt u SQL Server op een virtuele machine gebruiken om traditionele SQL Server-toepassingen te ontwikkelen en te testen. Met virtuele SQL-machines beschikt u over de volledige beheerders rechten voor een toegewezen SQL Server-exemplaar en een Cloud-VM. Het is een ideale keuze als een organisatie al IT-bronnen beschikbaar heeft voor het onderhouden van de virtuele machines. Met deze mogelijkheden kunt u een aangepast systeem bouwen voor de specifieke prestaties en beschikbaarheidsvereisten van uw toepassing.

Aanvullende verschillen worden weer gegeven in de volgende tabel, maar *zowel SQL database als SQL Managed instance zijn geoptimaliseerd om de totale beheer kosten te verlagen tot het minimum voor het inrichten en beheren van veel data bases.* Voortdurende beheer kosten worden verlaagd omdat u geen virtuele machines, besturings systemen of database software hoeft te beheren. U hoeft upgrades, hoge beschikbaarheid of [back-ups](database/automated-backups-overview.md) niet te beheren. 

In het algemeen kan SQL Database en SQL Managed instance het aantal data bases dat wordt beheerd door een enkele IT-of ontwikkel resource aanzienlijk verhogen. [Elastische Pools](database/elastic-pool-overview.md) ondersteunen ook SaaS multi tenant Application architecturen met functies, waaronder Tenant isolatie, en de mogelijkheid om de kosten te verlagen door resources te delen in verschillende data bases. [SQL Managed instance](managed-instance/sql-managed-instance-paas-overview.md) biedt ondersteuning voor functies met een exemplaar bereik, waardoor bestaande toepassingen eenvoudig kunnen worden gemigreerd en resources worden gedeeld tussen data bases.

### <a name="comparison-table"></a>Vergelijkingstabel

| Azure SQL Database | Azure SQL Managed Instance | SQL Server op virtuele Azure-machine |
| :--- | :--- | :--- |
|Ondersteunt de meeste on-premises mogelijkheden op database niveau. De meestgebruikte SQL Server-functies zijn beschikbaar.<br/>gegarandeerde Beschik baarheid van 99,995%.<br/>Ingebouwde back-ups, patches, herstel.<br/>Nieuwste stabiele data base-engine versie.<br/>De mogelijkheid om de benodigde resources (CPU/opslag) toe te wijzen aan afzonderlijke data bases.<br/>Ingebouwde geavanceerde intelligentie en beveiliging.<br/>Online wijziging van resources (CPU/opslag).| Biedt ondersteuning voor vrijwel alle on-premises op het niveau van de instantie en op database niveau. Hoge compatibiliteit met SQL Server.<br/>gegarandeerde Beschik baarheid van 99,99%.<br/>Ingebouwde back-ups, patches, herstel.<br/>Nieuwste stabiele data base-engine versie.<br/>Eenvoudige migratie van SQL Server.<br/>Privé-IP-adres in azure Virtual Network.<br/>Ingebouwde geavanceerde intelligentie en beveiliging.<br/>Online wijziging van resources (CPU/opslag).| U hebt volledige controle over de SQL Server-engine. Ondersteunt alle on-premises mogelijkheden.<br/>Maxi maal 99,99% Beschik baarheid.<br/>Volledige pariteit met de overeenkomende versie van on-premises SQL Server.<br/>Gecorrigeerde versie van de bekende data base-engine.<br/>Eenvoudige migratie van SQL Server.<br/>Privé-IP-adres in azure Virtual Network.<br/>U hebt de mogelijkheid om toepassingen of services te implementeren op de host waar SQL Server wordt geplaatst.|
|Migratie van SQL Server kan lastig zijn.<br/>Sommige SQL Server functies zijn niet beschikbaar.<br/>Geen gegarandeerd exacte onderhouds tijd (maar bijna transparant).<br/>De compatibiliteit met de SQL Server versie kan alleen worden bereikt door database compatibiliteits niveaus te gebruiken.<br/>Privé-IP-adres kan niet worden toegewezen (u kunt de toegang beperken met behulp van firewall regels).|Er is nog een aantal SQL Server functies die niet beschikbaar zijn.<br/>Geen gegarandeerd exacte onderhouds tijd (maar bijna transparant).<br/>De compatibiliteit met de SQL Server versie kan alleen worden bereikt door database compatibiliteits niveaus te gebruiken.|U moet uw back-ups en patches beheren.<br>U moet uw eigen oplossing voor hoge Beschik baarheid implementeren.<br/>Er is een downtime bij het wijzigen van de resources (CPU/opslag)|
| Data bases van Maxi maal 100 TB. | Maxi maal 8 TB. | SQL Server instanties met Maxi maal 256 TB aan opslag ruimte. Het exemplaar kan zoveel databases ondersteunen als nodig is. |
| On-premises toepassing heeft toegang tot gegevens in Azure SQL Database. | [Systeem eigen virtuele netwerk implementatie](managed-instance/vnet-existing-add-subnet.md) en connectiviteit met uw on-premises omgeving met behulp van Azure Express Route of VPN gateway. | Met virtuele SQL-machines kunt u toepassingen hebben die deels in de Cloud en gedeeltelijk op locatie worden uitgevoerd. Zo kunt u uw on-premises netwerk en Active Directory-domein uitbreiden naar de cloud met [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Zie [on-premises gegevens oplossingen uitbreiden naar de Cloud](https://docs.microsoft.com/azure/architecture/data-guide/scenarios/hybrid-on-premises-and-cloud)voor meer informatie over hybride cloud oplossingen. |


## <a name="cost"></a>Kosten

Of u nu een opstart proces bent dat is gebonden aan het geld, of een team in een gevestigde bedrijf dat werkt onder krappe budget beperkingen, een beperkte financiering is vaak het primaire stuur programma wanneer u bepaalt hoe u uw data bases wilt hosten. In deze sectie vindt u meer informatie over de basis principes voor facturering en licenties in azure die zijn gekoppeld aan de Azure SQL-service.  Ook leest u over hoe u de totale kosten voor de toepassing berekent.

### <a name="billing-and-licensing-basics"></a>Grondbeginselen facturering en licenties

Op dit moment worden zowel **SQL database** als **SQL Managed instance** als een service verkocht en zijn er verschillende opties beschikbaar in verschillende service lagen met verschillende prijzen voor resources, die allemaal elk uur worden gefactureerd tegen een vast tarief op basis van de servicelaag en de reken grootte die u kiest. Zie voor de meest recente informatie over de huidige ondersteunde service lagen, reken grootten en opslag bedragen het [op DTU gebaseerde aankoop model voor SQL database](database/service-tiers-dtu.md) en [vCore aankoop model voor zowel SQL database als SQL Managed instance](database/service-tiers-vcore.md).

- Met SQL Database kunt u een servicelaag kiezen die aan uw behoeften voldoet vanuit een breed scala aan prijzen vanaf 5 $/maand voor Basic-laag. u kunt ook [elastische Pools](database/elastic-pool-overview.md) maken om resources te delen tussen data bases om de kosten te verlagen en gebruiks pieken te benutten.
- Met SQL Managed Instance kunt u ook uw eigen licentie meenemen. Zie [License Mobility via Software Assurance op Azure](https://azure.microsoft.com/pricing/license-mobility/) of de [Azure Hybrid Benefit Calculator](https://azure.microsoft.com/pricing/hybrid-benefit/#sql-database) gebruiken om **Maxi maal 40%** op te slaan voor meer informatie over het nemen van uw eigen licentie.

Bovendien wordt uitgaand internetverkeer in rekening gebracht bij u tegen het reguliere [tarief voor gegevensoverdracht](https://azure.microsoft.com/pricing/details/data-transfers/). U kunt Service lagen en reken grootten dynamisch aanpassen zodat deze overeenkomen met de verschillende doorvoer behoeften van uw toepassing.

Met **SQL database** en **SQL Managed instance**wordt de database software automatisch geconfigureerd, gepatched en bijgewerkt door Azure, waardoor de beheer kosten worden verminderd. Bovendien kunt u met de [ingebouwde back-up](database/automated-backups-overview.md)mogelijkheden  aanzienlijk kosten besparen, vooral wanneer u een groot aantal databases hebt.

Met **SQL op virtuele machines van Azure**kunt u een van de platform-geleverde SQL Server installatie kopieën (met een licentie) gebruiken of uw SQL Server-licentie meenemen. Alle ondersteunde SQL Server versies (2008R2, 2012, 2014, 2016, 2017, 2019) en edities (Developer, Express, Web, Standard, Enter prise) zijn beschikbaar. Bovendien zijn er Bring Your Own License-versies (BYOL) van de installatiekopieën beschikbaar. Wanneer u de door Azure geleverde installatiekopieën gebruikt, zijn de operationele kosten afhankelijk van de VM-grootte en de versie van SQL Server die u kiest. Ongeacht de VM-grootte of SQL Server editie betaalt u per minuut licentie kosten van SQL Server en de Windows-of Linux-server, samen met de Azure Storage kosten voor de VM-schijven. Met de optie factureren per minuut kunt u SQL Server blijven gebruiken zonder aanvullende SQL Server-licenties te kopen. Als u uw eigen SQL Server-licentie naar Azure brengt, worden alleen de server-en opslag kosten in rekening gebracht. Zie voor meer informatie over meenemen van uw eigen licentie [License Mobility through Software Assurance on Azure](https://azure.microsoft.com/pricing/license-mobility/). Bovendien wordt uitgaand internetverkeer in rekening gebracht bij u tegen het reguliere [tarief voor gegevensoverdracht](https://azure.microsoft.com/pricing/details/data-transfers/).

#### <a name="calculating-the-total-application-cost"></a>De totale kosten voor de toepassing berekenen

Wanneer u begint met het gebruik van een Cloud platform, bevatten de kosten van het uitvoeren van uw toepassing de kosten voor nieuwe ontwikkelings-en continue beheer kosten, plus de service kosten van het open bare Cloud platform.

Zie de volgende bronnen voor meer informatie over prijzen:

- [Prijzen SQL Database & SQL Managed instance](https://azure.microsoft.com/pricing/details/sql-database/)
- [Prijs informatie voor virtuele machines](https://azure.microsoft.com/pricing/details/virtual-machines/) voor [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) en voor [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Azure-prijs calculator](https://azure.microsoft.com/pricing/calculator/)

## <a name="administration"></a>Beheer

Veel bedrijven besluiten over te stappen naar een cloudservice zowel vanwege offloading van beheercomplexiteit als de kosten. Met IaaS en PaaS beheert Azure de onderliggende infra structuur en worden alle gegevens automatisch gerepliceerd om herstel na nood gevallen te bieden, de database software te configureren en bij te voegen, de-taak verdeling te beheren en een transparante failover uit te voeren als er sprake is van een server storing in een data centrum.

- Met **SQL database** en **SQL Managed instance**kunt u uw data base blijven beheren, maar u hoeft de data base-engine, het besturings systeem of de hardware niet langer te beheren. Voorbeelden van items die u kunt blijven beheren zijn onder meer databases en aanmeldingen, index en query afstemmen, en controle en beveiliging.  Daarnaast is het configureren van hoge Beschik baarheid voor een ander Data Center vereist minimale configuratie en beheer.
- Met **SQL op Azure VM**hebt u volledige controle over het besturings systeem en de configuratie van het SQL Server-exemplaar. Met een VM kunt u zelf bepalen wanneer u het besturings systeem en de database software bijwerkt of bijwerkt en wanneer u aanvullende software, zoals een antivirus programma, installeert. Sommige geautomatiseerde functies worden geleverd om patchen, het maken van een back-up en hoge beschikbaarheid aanzienlijk te vereenvoudigen. Bovendien kunt u de grootte van de VM, het aantal schijven en hun opslagconfiguraties beheren. Met Azure kunt de grootte van een VM indien nodig wijzigen. Zie voor meer informatie [Virtual Machine en Cloud Service Sizes for Azure](../virtual-machines/windows/sizes.md).

## <a name="service-level-agreement-sla"></a>Service Level Agreement (SLA)

Voor veel IT-afdelingen is het belang rijk dat u voldoet aan de belangrijkste verplichtingen van een Service Level Agreement (SLA). In dit gedeelte kijken we welke SLA van toepassing is op de databasehostingopties.

Voor zowel **Azure SQL database** als **Azure SQL Managed instance**biedt micro soft een beschikbaarheids-sla van 99,99%. Zie [Service Level Agreement](https://azure.microsoft.com/support/legal/sla/sql-database/)voor de meest recente informatie.

Voor **SQL op Azure VM**biedt micro soft een beschikbaarheids-SLA van 99,95% dat betrekking heeft op alleen de virtuele machine. Deze SLA heeft geen betrekking op de processen (zoals SQL Server) die worden uitgevoerd op de VM en vereist dat u ten minste twee exemplaren van de virtuele machine in een beschikbaarheidsset host. Voor de meest recente informatie, zie de [VM SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Voor maximale Beschik baarheid van de data base binnen Vm's, moet u een van de ondersteunde opties voor hoge Beschik baarheid configureren in SQL Server, zoals [Always on-beschikbaarheids groepen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Het gebruik van een ondersteunde optie voor hoge beschikbaarheid biedt geen extra SLA, maar u kunt wel een databasebeschikbaarheid van >99,99% behalen.

## <a name="time-to-move-to-azure"></a><a name="market"></a>Tijd om over te stappen op Azure

**Azure SQL database** is de juiste oplossing voor toepassingen die in de Cloud zijn ontworpen wanneer de productiviteit van ontwikkel aars en snelle time-to-Market voor nieuwe oplossingen cruciaal zijn. Met programmatische DBA-functies is het ideaal voor cloud-architecten en -ontwikkelaars aangezien het de noodzaak voor het beheren van het onderliggende besturingssysteem en de database vermindert.

**Azure SQL Managed instance** vereenvoudigt de migratie van bestaande toepassingen naar Azure, waardoor u snel gemigreerde database toepassingen naar de markt kunt brengen in Azure.

**SQL op Azure VM** is ideaal als uw bestaande of nieuwe toepassingen grote data bases of toegang tot alle functies in SQL Server of Windows/Linux nodig hebben en u wilt voor komen dat er nieuwe on-premises hardware worden aangestuurd. Het is ook een goed idee wanneer u bestaande on-premises toepassingen en data bases wilt migreren naar Azure, omdat SQL Database of SQL Managed instance niet geschikt is. Omdat u de presentatie-, toepassings-en gegevens lagen niet hoeft te wijzigen, bespaart u tijd en budget bij het opnieuw ontwerpen van uw bestaande oplossing. In plaats daarvan kunt u zich richten op het migreren van uw oplossingen naar Azure en het uitvoeren van prestatieoptimalisatie die mogelijk wordt vereist door het Azure-platform. Zie voor meer informatie [Best practices prestaties for SQL Server on Azure Virtual Machines](virtual-machines/windows/performance-guidelines-best-practices.md).

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="next-steps"></a>Volgende stappen

- Zie [Uw eerste Azure SQL Database](database/single-database-create-quickstart.md) om aan de slag te gaan met SQL Database.
- Bekijk [uw eerste Azure SQL Managed instance](managed-instance/instance-create-quickstart.md) om aan de slag te gaan met SQL Managed instance. 
- Zie [Prijzen van SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).
- Zie [Provision a SQL Server virtual machine in Azure](virtual-machines/windows/create-sql-vm-portal.md) om aan de slag te kunnen met SQL Server op Azure VM’s.
- [Bepaal de juiste SQL database of SQL Managed instance SKU voor uw on-premises data base](/sql/dma/dma-sku-recommend-sql-db/).
