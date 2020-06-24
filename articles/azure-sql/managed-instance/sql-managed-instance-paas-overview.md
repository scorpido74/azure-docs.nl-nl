---
title: Wat is Azure SQL Managed instance?
description: Meer informatie over de manier waarop Azure SQL Managed instance bij de nieuwste data base-engine van SQL Server (Enter prise Edition) bijna 100% biedt.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 04/02/2020
ms.openlocfilehash: db476d32d3b087e86329f8ed40446caf122c0a00
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84944794"
---
# <a name="what-is-azure-sql-managed-instance"></a>Wat is Azure SQL Managed instance?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Een onderdeel van de Azure SQL-product familie, Azure SQL Managed instance is de intelligente, schaal bare Cloud database service die de breedste SQL Server data base-engine-compatibiliteit combineert met alle voor delen van een volledig beheerd en groen wordende platform als een service. SQL Managed instance heeft bijna 100% compatibel met de nieuwste data base-engine van SQL Server (Enter prise Edition), waarbij een [VNet-implementatie (native Virtual Network](../../virtual-network/virtual-networks-overview.md) ) wordt geboden waarmee veelvoorkomende beveiligings problemen worden opgelost en een [bedrijfs model](https://azure.microsoft.com/pricing/details/sql-database/) kan worden gebruikt voor bestaande SQL Server klanten. Met SQL Managed instance kunnen bestaande SQL Server klanten hun on-premises toepassingen naar de Cloud tillen en naar een andere data base verplaatsen met minimale toepassings-en database wijzigingen. Tegelijkertijd behoudt SQL Managed instance alle PaaS-mogelijkheden (automatische patches en versie-updates, [geautomatiseerde back-ups](../database/automated-backups-overview.md), [hoge Beschik baarheid](../database/high-availability-sla.md)) waarmee de overhead en TCO van het beheer drastisch worden verminderd.

> [!IMPORTANT]
> Zie [ondersteunde regio's](resource-limits.md#supported-regions)voor een lijst met REGIO'S waar SQL Managed Instance momenteel beschikbaar is.

In het volgende diagram worden de belangrijkste functies van SQL Managed instance beschreven:

![Belangrijke functies](./media/sql-managed-instance-paas-overview/key-features.png)

Azure SQL Managed instance is ontworpen voor klanten die een groot aantal apps willen migreren van een on-premises of IaaS, zelfgebouwde of ISV-geleverde omgeving naar een volledig beheerde PaaS-cloud omgeving, met zo weinig mogelijk migratie inspanningen. Met de volledig geautomatiseerde [Azure Data Migration-service](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)kunnen klanten hun bestaande SQL Server exemplaar naar SQL Managed instance verplaatsen en verschuiven, wat compatibel is met SQL Server en volledige isolatie van klant instanties biedt met systeem eigen VNet-ondersteuning.  Met Software Assurance kunt u uw bestaande licenties uitwisselen voor kortings tarieven op een SQL-beheerd exemplaar met behulp [van de Azure Hybrid Benefit voor SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). SQL Managed instance is de beste migratie bestemming in de Cloud voor SQL Server instanties waarvoor een hoge beveiliging en een uitgebreid programmeerbaar Opper vlak zijn vereist.

## <a name="key-features-and-capabilities"></a>Belangrijkste functies en mogelijkheden

SQL Managed instance combineert de beste functies die beschikbaar zijn in Azure SQL Database en de SQL Server data base-engine.

> [!IMPORTANT]
> SQL Managed instance wordt uitgevoerd met alle functies van de meest recente versie van SQL Server, waaronder online bewerkingen, Automatische plannings correcties en andere verbeteringen voor bedrijfs prestaties. Een vergelijking van de beschik bare functies wordt uitgelegd in [functie vergelijking: Azure SQL Managed instance versus SQL Server](../database/features-comparison.md).

| **PaaS-voor delen** | **Bedrijfs continuïteit** |
| --- | --- |
|Geen hardware kopen en beheren <br>Geen beheer overhead voor het beheren van de onderliggende infra structuur <br>Snelle inrichting en service schalen <br>Automatische patching en versie-upgrade <br>Integratie met andere PaaS-gegevens Services |SLA voor 99,99% uptime  <br>Ingebouwde [hoge Beschik baarheid](../database/high-availability-sla.md) <br>Gegevens die worden beveiligd met [automatische back-ups](../database/automated-backups-overview.md) <br>Door de klant Configureer bare Bewaar periode voor back-ups <br>Door de gebruiker geïnitieerde [back-ups](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[Herstel mogelijkheid voor tijdgebonden data bases](../database/recovery-using-backups.md#point-in-time-restore) |
|**Beveiliging en naleving** | **Beheer**|
|Geïsoleerde omgeving ([VNet-integratie](connectivity-architecture-overview.md), single tenant service, specifieke reken kracht en opslag) <br>[TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure Active Directory-verificatie (Azure AD)](../database/authentication-aad-overview.md), ondersteuning voor eenmalige aanmelding <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD-server-principals (aanmeldingen)</a>  <br>Voldoet aan nalevings normen hetzelfde als Azure SQL Database <br>[SQL-controle](auditing-configure.md) <br>[Advanced Threat Protection](threat-detection-configure.md) |Azure Resource Manager-API voor het automatiseren van het inrichten en schalen van services <br>Azure Portal functionaliteit voor het hand matig inrichten en schalen van services <br>Data Migration Service

> [!IMPORTANT]
> Azure SQL Managed instance is gecertificeerd op basis van een aantal nalevings standaarden. Zie voor meer informatie de [Microsoft Azure compliance-aanbiedingen](https://servicetrust.microsoft.com/ViewPage/MSComplianceGuideV3?command=Download&downloadType=Document&downloadId=44bbae63-bf4d-4e3b-9d3d-c96fb25ec363&tab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb&docTab=7027ead0-3d6b-11e9-b9e1-290b1eb4cdeb_FAQ_and_White_Papers), waar u de meest recente lijst kunt vinden met SQL Managed instance compliance certificeringen, vermeld onder **SQL database**.

De belangrijkste functies van SQL Managed instance worden weer gegeven in de volgende tabel:

|Functie | Beschrijving|
|---|---|
| Versie/build van SQL Server | SQL Server data base-engine (laatste stabiel) |
| Beheerde geautomatiseerde back-ups | Yes |
| Ingebouwde instantie-en database controle en-metrische gegevens | Yes |
| Automatische software patching | Yes |
| De nieuwste functies van de data base-engine | Yes |
| Aantal gegevens bestanden (rijen) per data base | Meerdere |
| Aantal logboek bestanden (logboek) per data base | 1 |
| VNet-Azure Resource Manager-implementatie | Yes |
| VNet-klassiek implementatie model | No |
| Portal ondersteuning | Yes|
| SSIS (ingebouwde integratie service) | No-SSIS maakt deel uit van [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| SSAS (ingebouwde Analysis Service) | Nee-SSAS is afzonderlijke [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Ingebouwde Reporting service (SSRS) | Geen gebruik [Power bi gepagineerde rapporten](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) of host SSRS op een virtuele machine van Azure. SQL Managed instance kan geen SSRS als service uitvoeren, maar kan de SSRS 2019-catalogus databases voor een externe rapport Server hosten met behulp van SQL Server-verificatie. |
|||

## <a name="vcore-based-purchasing-model"></a>Aankoopmodel op basis van vCore

Het [op vCore gebaseerde aankoop model](../database/service-tiers-vcore.md) voor SQL Managed instance biedt u flexibiliteit, controle, transparantie en een eenvoudige manier om on-premises werkbelasting vereisten te vertalen naar de Cloud. Met dit model kunt u de reken capaciteit, het geheugen en de opslag aanpassen op basis van de behoeften van uw werk belasting. Het vCore-model is ook in aanmerking voor een besparing van Maxi maal 55 procent met de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) voor SQL Server.

In het vCore-model kunt u kiezen tussen generaties van hardware.

- **Gen4** logische cpu's zijn gebaseerd op Intel E5-2673 v3-processors (Haswell 2,4), gekoppelde SSD, fysieke kernen, 7 GB RAM per kern, en reken grootten tussen 8 en 24 vCores.
- **GEN5** logische cpu's zijn gebaseerd op Intel E5-2673 v4 (Broadwell) 2,3-GHz en Intel SP-8160 (Skylake)-processors, snelle NVME-SSD, Hyper-Threaded logische kern en reken grootten tussen 4 en 80 kernen.

Meer informatie over het verschil tussen hardware-generaties in [SQL Managed instance resource limieten](resource-limits.md#hardware-generation-characteristics).

## <a name="service-tiers"></a>Servicelagen

SQL Managed instance is beschikbaar in twee service lagen:

- **Algemeen gebruik**: ontworpen voor toepassingen met typische prestatie-en I/O-latentie vereisten.
- **Bedrijfs kritiek**: ontworpen voor toepassingen met een lage I/O-latentie vereisten en minimale gevolgen van onderliggende onderhouds bewerkingen op de werk belasting.

Beide service lagen garanderen een Beschik baarheid van 99,99% en bieden u de mogelijkheid om de opslag grootte en de berekenings capaciteit onafhankelijk te selecteren. Zie [hoge Beschik baarheid en Azure SQL Managed instance](../database/high-availability-sla.md)voor meer informatie over de architectuur met hoge Beschik baarheid van Azure SQL Managed instance.

### <a name="general-purpose-service-tier"></a>Algemeen servicelaag

In de volgende lijst worden de belangrijkste kenmerken van de Algemeen servicelaag beschreven:

- Ontworpen voor het meren deel van zakelijke toepassingen met typische prestatie vereisten
- Azure Blob-opslag met hoge prestaties (8 TB)
- Ingebouwde [hoge Beschik baarheid](../database/high-availability-sla.md#basic-standard-and-general-purpose-service-tier-availability) op basis van betrouw bare Azure Blob-opslag en [Azure service Fabric](../../service-fabric/service-fabric-overview.md)

Zie [Storage Layer in de algemeen laag](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) en [Aanbevolen procedures voor opslag prestaties en overwegingen voor SQL Managed instance (algemeen)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/)voor meer informatie.

Meer informatie over het verschil tussen service lagen in [SQL Managed instance resource limieten](resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Bedrijfskritiek servicelaag

De Bedrijfskritiek servicelaag is gebouwd voor toepassingen met hoge I/O-vereisten. Het biedt de hoogste flexibiliteit voor storingen met behulp van verschillende geïsoleerde replica's.

De volgende lijst geeft een overzicht van de belangrijkste kenmerken van de Bedrijfskritiek servicelaag:

- Ontworpen voor zakelijke toepassingen met de meeste vereisten voor prestaties en HA
- Wordt geleverd met super snelle lokale SSD-opslag (Maxi maal 1 TB op Gen4 en Maxi maal 4 TB op GEN5)
- Ingebouwde [hoge Beschik baarheid](../database/high-availability-sla.md#premium-and-business-critical-service-tier-availability) op basis van AlwaysOn- [beschikbaarheids groepen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) en [Azure service Fabric](../../service-fabric/service-fabric-overview.md)
- Ingebouwde aanvullende [alleen-lezen database replica](../database/read-scale-out.md) die kan worden gebruikt voor rapportage en andere alleen-lezen workloads
- [In-Memory OLTP](../in-memory-oltp-overview.md) die kan worden gebruikt voor workloads met hoge prestatie vereisten  

Meer informatie over de verschillen tussen de service lagen in [SQL Managed instance resource limieten](resource-limits.md#service-tier-characteristics).

## <a name="management-operations"></a>Beheer bewerkingen

Azure SQL Managed instance biedt beheer bewerkingen die u kunt gebruiken om automatisch nieuwe beheerde instanties te implementeren, exemplaar-eigenschappen bij te werken en instanties te verwijderen wanneer ze niet meer nodig zijn. Deze sectie bevat informatie over beheer bewerkingen en hun typische duur.

Voor de ondersteuning van [implementaties in azure Virtual Networks](../../virtual-network/virtual-network-for-azure-services.md) en het bieden van isolatie en beveiliging voor klanten, is SQL Managed instance afhankelijk van [virtuele clusters](connectivity-architecture-overview.md#high-level-connectivity-architecture), die een specifieke set geïsoleerde virtuele machines vertegenwoordigen die zijn geïmplementeerd in het subnet van het virtuele netwerk van de klant. In wezen resulteert elke implementatie van een beheerd exemplaar in een leeg subnet in een nieuw virtueel cluster buildout.

Volgende bewerkingen in geïmplementeerde beheerde instanties kunnen ook gevolgen hebben voor het onderliggende virtuele cluster. Dit is van invloed op de duur van beheer bewerkingen, zoals het implementeren van aanvullende virtuele machines wordt geleverd met een overhead die moet worden overwogen wanneer u nieuwe implementaties of updates voor bestaande beheerde exemplaren plant.

Alle beheerbewerkingen kunnen als volgt worden gecategoriseerd:

- Implementatie van instanties (nieuwe instantie maken).
- Update van instantie (instantie-eigenschappen wijzigen, zoals vCores of gereserveerde opslag).
- Instantie verwijderen.

Normaal gesp roken hebben bewerkingen op virtuele clusters het langst. De duur van de bewerkingen op virtuele clusters is afhankelijk van de waarden die u doorgaans kunt verwachten, op basis van de bestaande telemetrie-gegevens van de service:

- **Virtueel cluster maken**: dit is een synchrone stap in bewerkingen voor het beheer van exemplaren. **90% van de bewerkingen zijn voltooid over vier uur**.
- **Verg Roten/verkleinen van een virtueel cluster (uitbrei ding of krimpen)**: uitbrei ding is een synchrone stap, terwijl het comprimeren wordt asynchroon uitgevoerd (zonder invloed op de duur van de beheer bewerkingen voor exemplaren). **90% van de cluster uitbreidingen eindigen in minder dan 2,5 uur**.
- **Virtueel cluster verwijderen**: de verwijdering is een asynchrone stap, maar kan ook [hand matig worden gestart](virtual-cluster-delete.md) op een leeg virtueel cluster. in dat geval wordt het synchroon uitgevoerd. **90% van verwijderde virtuele clusters is in 1,5 uur voltooid**.

Daarnaast kan het beheer van instanties ook een van de bewerkingen op gehoste data bases bevatten, die de duur van een langere periode in beslag neemt:

- **Database bestanden van Azure Storage koppelen**: dit is een synchrone stap, zoals Compute (vCore) of het omhoog of omlaag schalen van opslag in de servicelaag algemeen. **90% van deze bewerkingen zijn voltooid in 5 minuten**.
- AlwaysOn- **beschikbaarheids groep moet worden geseedd**: dit is een synchrone stap, zoals Compute (vCore), of opslag schalen in de laag bedrijfskritiek en in het wijzigen van de servicelaag van Algemeen in bedrijfskritiek (of andersom). De duur van deze bewerking is evenredig met de totale database grootte en de huidige database activiteit (aantal actieve trans acties). Data base-activiteit bij het bijwerken van een exemplaar kan aanzienlijk verschillen veroorzaken in de totale duur. **90% van deze bewerkingen worden uitgevoerd om 220 GB/uur of hoger**.

De volgende tabel bevat een overzicht van de bewerkingen en typische totale duur:

|Categorie  |Bewerking  |Langlopend segment  |Geschatte duur  |
|---------|---------|---------|---------|
|**Implementatie** |Eerste instantie in een leeg subnet|Virtueel cluster maken|90% van de bewerkingen zijn voltooid over vier uur.|
|Implementatie |Eerste exemplaar van een andere hardware-generatie in een niet-leeg subnet (bijvoorbeeld eerste generatie 5-exemplaar in een subnet met exemplaren van de generatie 4)|Virtueel cluster maken *|90% van de bewerkingen zijn voltooid over vier uur.|
|Implementatie |Eerste instantie maken van 4 vCores, in een leeg of niet-leeg subnet|Virtueel cluster maken * *|90% van de bewerkingen zijn voltooid over vier uur.|
|Implementatie |Het maken van de volgende instantie binnen het niet-lege subnet (2e, 3e, enz.)|Verg Roten/verkleinen van virtueel cluster|90% van de bewerkingen zijn voltooid over 2,5 uur.|
|**Bijwerk** |Wijziging van instantie-eigenschap (beheerders wachtwoord, Azure AD-aanmelding, Azure Hybrid Benefit vlag)|N.v.t.|Maxi maal 1 minuut.|
|Bijwerken |Opslag van exemplaren omhoog/omlaag schalen (Algemeen servicelaag)|Database bestanden koppelen|90% van de bewerkingen zijn voltooid over vijf minuten.|
|Bijwerken |Opslag van exemplaren omhoog/omlaag schalen (Bedrijfskritiek servicelaag)|-Verg Roten/verkleinen van virtueel cluster<br>-Always on-beschikbaarheids groep seeding|90% van de bewerkingen zijn voltooid om 2,5 uur en tijd om alle data bases te seeden (220 GB/uur).|
|Bijwerken |VCores (instance Compute) omhoog en omlaag schalen (Algemeen)|-Verg Roten/verkleinen van virtueel cluster<br>-Database bestanden koppelen|90% van de bewerkingen zijn voltooid over 2,5 uur.|
|Bijwerken |VCores (instance Compute) omhoog en omlaag schalen (Bedrijfskritiek)|-Verg Roten/verkleinen van virtueel cluster<br>-Always on-beschikbaarheids groep seeding|90% van de bewerkingen zijn voltooid om 2,5 uur en tijd om alle data bases te seeden (220 GB/uur).|
|Bijwerken |Exemplaar wordt omlaag geschaald naar 4 vCores (Algemeen)|-Het wijzigen van het formaat van het virtuele cluster (als dit voor de eerste keer wordt uitgevoerd, kan het maken van een virtueel cluster vereist zijn * *)<br>-Database bestanden koppelen|90% van de bewerkingen zijn voltooid in 4 uur 5 minuten. * *|
|Bijwerken |Exemplaar wordt omlaag geschaald naar 4 vCores (Bedrijfskritiek)|-Het wijzigen van het formaat van het virtuele cluster (als dit voor de eerste keer wordt uitgevoerd, kan het maken van een virtueel cluster vereist zijn * *)<br>-Always on-beschikbaarheids groep seeding|90% van de bewerkingen zijn voltooid in 4 uur + tijd voor het seeden van alle data bases (220 GB/uur).|
|Bijwerken |Wijziging van de instantie-servicelaag (Algemeen Bedrijfskritiek en omgekeerd)|-Verg Roten/verkleinen van virtueel cluster<br>-Always on-beschikbaarheids groep seeding|90% van de bewerkingen zijn voltooid om 2,5 uur en tijd om alle data bases te seeden (220 GB/uur).|
|**Verwijdering**|Verwijdering van exemplaar|Back-ups van staart vastleggen voor alle data bases|90% bewerkingen zijn Maxi maal 1 minuut voltooid.<br>Opmerking: als de laatste instantie in het subnet wordt verwijderd, wordt de verwijdering van het virtuele cluster na 12 uur door deze bewerking gepland. * * *|
|Verwijdering|Virtueel cluster verwijderen (als door de gebruiker geïnitieerde bewerking)|Virtueel cluster verwijderen|90% van de bewerkingen zijn Maxi maal 1,5 uur voltooid.|

\*Virtueel cluster is gebouwd per generatie van hardware.

\*\*De 4-vCores optie is uitgebracht in juni 2019 en vereist een nieuwe versie van het virtuele cluster. Als er instanties in het doel-subnet stonden die al zijn gemaakt vóór 12 juni, wordt er automatisch een nieuw virtueel cluster geïmplementeerd voor het hosten van 4 vCore-instanties.

\*\*\*12 uur is de huidige configuratie, maar dit kan in de toekomst veranderen, dus neem geen vaste afhankelijkheid op. Als u eerder een virtueel cluster moet verwijderen (bijvoorbeeld voor het vrijgeven van het subnet), raadpleegt u [een subnet verwijderen na het verwijderen van een beheerd exemplaar](virtual-cluster-delete.md).

### <a name="instance-availability-during-management-operations"></a>Beschik baarheid van exemplaren tijdens beheer bewerkingen

SQL Managed instance **is beschikbaar tijdens update bewerkingen**, met uitzonde ring van een korte downtime die wordt veroorzaakt door de failover die aan het einde van de update plaatsvindt. Het duurt meestal Maxi maal 10 seconden, zelfs in het geval van langdurige langlopende trans acties, dankzij het [versnelde herstel van de data base](../accelerated-database-recovery.md).

> [!IMPORTANT]
> Het is niet raadzaam om Compute of opslag van een door Azure SQL beheerd exemplaar te schalen of de servicelaag tegelijkertijd te wijzigen met de langlopende trans acties (gegevens importeren, gegevens verwerkings taken, index Rebuild, enzovoort). Voor de data base-failover die aan het einde van de bewerking wordt uitgevoerd, worden alle lopende trans acties geannuleerd.

SQL Managed instance is niet beschikbaar voor client toepassingen tijdens implementatie-en verwijderings bewerkingen.

### <a name="management-operations-cross-impact"></a>Beheer bewerkingen, cross-effect

Beheer bewerkingen op een beheerd exemplaar kunnen invloed hebben op andere beheer bewerkingen van de instanties die binnen hetzelfde virtuele cluster worden geplaatst. Dit omvat het volgende:

- Voor **langlopende herstel bewerkingen** in een virtueel cluster worden andere bewerkingen voor het maken of schalen van instanties in hetzelfde subnet opgeslagen.<br/>**Voor beeld:** Als er een langlopende herstel bewerking is en er in hetzelfde subnet een aanvraag voor maken of schalen wordt uitgevoerd, duurt het langer voordat deze aanvraag is voltooid, omdat er wordt gewacht tot de herstel bewerking is voltooid voordat deze wordt voortgezet.
    
- **Een volgende bewerking voor het maken of schalen van een exemplaar** wordt in de wacht geplaatst door een eerder geïnitieerde instantie voor het maken of schalen van een exemplaar waarbij het formaat van het virtuele cluster wordt gestart.<br/>**Voor beeld:** Als er meerdere aanvragen voor maken en/of schalen worden uitgevoerd in hetzelfde subnet onder hetzelfde virtuele cluster, en een ervan het formaat van een virtueel cluster initieert, worden alle aanvragen die 5 + minuten na de wijziging van het virtuele cluster zijn ingediend, langer dan verwacht, omdat deze aanvragen moeten wachten tot de grootte is voltooid voordat het proces wordt hervat.

- **Create/Scale-bewerkingen die zijn verzonden in een venster van vijf minuten** , worden parallel in batch verwerkt en uitgevoerd.<br/>**Voor beeld:** Er wordt slechts één grootte van het virtuele cluster uitgevoerd voor alle bewerkingen die zijn ingediend in een periode van vijf minuten (gemeten vanaf het moment waarop de eerste bewerkings aanvraag wordt uitgevoerd). Als een andere aanvraag meer dan vijf minuten na het verzenden van de eerste wordt verzonden, wordt gewacht tot de grootte van het virtuele cluster is voltooid voordat de uitvoering wordt gestart.

> [!IMPORTANT]
> Beheer bewerkingen die in de wacht worden gezet omdat een andere bewerking wordt uitgevoerd, worden automatisch hervat wanneer aan de voor waarden wordt voldaan. Er is geen actie van de gebruiker nodig om tijdelijk onderbroken beheer bewerkingen te hervatten.

### <a name="canceling-management-operations"></a>Beheer bewerkingen annuleren

De volgende tabel bevat een overzicht van de mogelijkheid om specifieke beheer bewerkingen en typische totale duur te annuleren:

Categorie  |Bewerking  |Geannuleerd  |Geschatte annulerings duur  |
|---------|---------|---------|---------|
|Implementatie |Instantie maken |No |  |
|Bijwerken |Opslag van exemplaren omhoog/omlaag schalen (Algemeen) |No |  |
|Bijwerken |Opslag van exemplaren omhoog/omlaag schalen (Bedrijfskritiek) |Yes |90% van de bewerkingen zijn voltooid over vijf minuten. |
|Bijwerken |VCores (instance Compute) omhoog en omlaag schalen (Algemeen) |Yes |90% van de bewerkingen zijn voltooid over vijf minuten. |
|Bijwerken |VCores (instance Compute) omhoog en omlaag schalen (Bedrijfskritiek) |Yes |90% van de bewerkingen zijn voltooid over vijf minuten. |
|Bijwerken |Wijziging van de instantie-servicelaag (Algemeen Bedrijfskritiek en omgekeerd) |Yes |90% van de bewerkingen zijn voltooid over vijf minuten. |
|Verwijderen |Verwijdering van exemplaar |No |  |
|Verwijderen |Virtueel cluster verwijderen (als door de gebruiker geïnitieerde bewerking) |No |  |

Als u de beheer bewerking wilt annuleren, gaat u naar de Blade overzicht en klikt u op meldingen venster van de actieve bewerking. Aan de rechter kant verschijnt een scherm met de doorlopende bewerking en er wordt een knop weer gegeven voor het annuleren van de bewerking. Nadat u de eerste keer hebt geklikt, wordt u gevraagd opnieuw te klikken en bevestigen dat u de bewerking wilt annuleren.

[![Bewerking annuleren](./media/sql-managed-instance-paas-overview/canceling-operation.png)](./media/sql-managed-instance-paas-overview/canceling-operation.png#lightbox)

Nadat een annulerings aanvraag is verzonden en verwerkt, ontvangt u een melding als de annulering is geslaagd of niet.

Als het annuleren is voltooid, wordt de beheer bewerking in een paar minuten geannuleerd, wat resulteert in een fout.

![Het resultaat van de bewerking wordt geannuleerd](./media/sql-managed-instance-paas-overview/canceling-operation-result.png)

Als de annulerings aanvraag mislukt of de knop Annuleren niet actief is, betekent dit dat de beheer bewerking een niet-Annuleer bare status heeft en dat deze binnen een paar minuten wordt voltooid. De beheer bewerking wordt voortgezet totdat deze is voltooid.

> [!IMPORTANT]
> Het annuleren van bewerkingen wordt momenteel alleen ondersteund in de portal.

## <a name="advanced-security-and-compliance"></a>Geavanceerde beveiliging en naleving van voorschriften

SQL Managed instance wordt geleverd met geavanceerde beveiligings functies die worden geleverd door het Azure-platform en de SQL Server data base-engine.

### <a name="security-isolation"></a>Beveiligings isolatie

SQL Managed instance biedt extra beveiligings isolatie van andere tenants op het Azure-platform. Beveiligings isolatie omvat:

- [Systeem eigen virtuele netwerk implementatie](connectivity-architecture-overview.md) en connectiviteit met uw on-premises omgeving met behulp van Azure ExpressRoute of VPN gateway.
- In een standaard implementatie wordt het SQL-eind punt alleen weer gegeven via een privé-IP-adres, waardoor er veilige connectiviteit mogelijk is vanuit persoonlijke Azure-of hybride netwerken.
- Eén Tenant met toegewezen onderliggende infra structuur (compute, Storage).

Het volgende diagram geeft een overzicht van de verschillende connectiviteits opties voor uw toepassingen:

![Hoge beschikbaarheid](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

Zie voor meer informatie over VNet-integratie en het afdwingen van het netwerk beleid op subnetniveau [vnet-architectuur voor beheerde instanties](connectivity-architecture-overview.md) en [Verbind uw toepassing met een beheerd exemplaar](connect-application-instance.md).

> [!IMPORTANT]
> Plaats meerdere beheerde instanties in hetzelfde subnet, overal waar uw beveiligings vereisten worden toegestaan, zodat u extra voor delen krijgt. Door gezamenlijk instanties in hetzelfde subnet te plaatsen, wordt het beheer van de netwerk infrastructuur aanzienlijk vereenvoudigd en wordt de inrichtings tijd van het exemplaar beperkt, omdat een lange inrichtings duur is gekoppeld aan de kosten van de implementatie van het eerste beheerde exemplaar in een subnet.

### <a name="security-features"></a>Beveiligingsfuncties

Azure SQL Managed instance biedt een aantal geavanceerde beveiligings functies die kunnen worden gebruikt om uw gegevens te beveiligen.

- Met [controle van SQL Managed instance](auditing-configure.md) worden database gebeurtenissen bijgehouden en naar een audit logboek bestand geschreven dat in uw Azure Storage-account is opgeslagen. Dankzij controles kunt u zorgen voor naleving van wet- en regelgeving, krijgt u inzicht in de activiteit in uw database en in de afwijkingen en discrepanties die kunnen wijzen op problemen voor het bedrijf of vermoedelijke schendingen van de beveiliging.
- Gegevens versleuteling in Motion-SQL Managed instance beveiligt uw gegevens door versleuteling van gegevens in beweging te bieden met behulp van Transport Layer Security. Naast Transport Layer Security biedt SQL Managed instance beveiliging van gevoelige gegevens in vlucht, op rest en tijdens de verwerking van query's met [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Always Encrypted biedt gegevens beveiliging tegen inbreuken waarbij essentiële gegevens worden gestolen. Met Always Encrypted worden creditcard nummers bijvoorbeeld in de data base versleuteld, zelfs tijdens de query verwerking, voor ontsleuteling op het punt van gebruik toegestaan door geautoriseerde mede werkers of toepassingen die deze gegevens moeten verwerken.
- [Advanced Threat Protection](threat-detection-configure.md) is een aanvulling op de [controle](auditing-configure.md) door een extra laag van beveiligings informatie op te geven die is ingebouwd in de service en die ongebruikelijke en mogelijk schadelijke pogingen voor toegang tot of exploiten van data bases detecteert. U wordt gewaarschuwd over verdachte activiteiten, mogelijke beveiligings problemen en SQL-injectie aanvallen, evenals afwijkende database toegangs patronen. Geavanceerde beveiligings waarschuwingen voor bedreigingen kunnen worden weer gegeven vanuit [Azure Security Center](https://azure.microsoft.com/services/security-center/). Ze bieden Details over verdachte activiteiten en aanbevolen actie voor het onderzoeken en oplossen van de dreiging.  
- [Dynamische gegevens maskering](/sql/relational-databases/security/dynamic-data-masking) beperkt de bloot stelling van gevoelige gegevens door deze te maskeren voor niet-bevoegde gebruikers. Dynamische gegevens maskering helpt onbevoegde toegang tot gevoelige gegevens te voor komen, doordat u kunt opgeven hoeveel gevoelige gegevens moeten worden weer gegeven met minimale gevolgen voor de toepassingslaag. Het is een op beleid gebaseerde beveiligings functie waarmee de gevoelige gegevens in de resultatenset van een query over bepaalde database velden worden verborgen, terwijl de gegevens in de data base niet worden gewijzigd.
- Met [beveiliging](/sql/relational-databases/security/row-level-security) op rijniveau kunt u de toegang tot rijen in een database tabel beheren op basis van de kenmerken van de gebruiker die een query uitvoert (bijvoorbeeld per groepslid maatschap of uitvoerings context). Met beveiliging op RIJNIVEAU wordt het ontwerp en de code ring van beveiliging in uw toepassing vereenvoudigd. Met RLS kunt u beperkingen instellen voor de toegang tot gegevens in rijen. Bijvoorbeeld, zodat werk nemers alleen toegang hebben tot de gegevens rijen die relevant zijn voor hun afdeling, of de toegang tot gegevens beperken tot alleen de relevante gegevens.
- [Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) versleutelt gegevens bestanden van SQL Managed instance, ook wel het versleutelen van gegevens in rust. TDE voert realtime-I/O-versleuteling en ontsleuteling van de gegevens en logboek bestanden uit. De versleuteling maakt gebruik van een database versleutelings sleutel (DEK), die is opgeslagen in de data base boot record voor Beschik baarheid tijdens het herstel. U kunt al uw data bases in een beheerd exemplaar beveiligen met transparante gegevens versleuteling. TDE is bewezen-at-rest-technologie in SQL Server die wordt vereist door veel nalevings standaarden om te beschermen tegen dief stal van opslag media.

Migratie van een versleutelde data base naar een SQL Managed instance wordt ondersteund via Azure Database Migration Service of systeem eigen herstel. Als u van plan bent om een versleutelde data base te migreren met behulp van systeem eigen herstel, is de migratie van het bestaande TDE-certificaat van het SQL Server exemplaar naar een SQL Managed instance een vereiste stap. Zie [SQL Server Migration to SQL Managed instance](migrate-to-instance-from-sql-server.md)(Engelstalig) voor meer informatie over migratie opties.

## <a name="azure-active-directory-integration"></a>Integratie van Azure Active Directory

SQL Managed instance ondersteunt traditionele SQL Server data base-engine aanmeldingen en aanmeldingen die zijn geïntegreerd met Azure AD. Azure AD server-principals (aanmeldingen) (**open bare preview**) zijn een Azure-Cloud versie van on-premises data base-aanmeldingen die u in uw on-premises omgeving gebruikt. Met Azure AD-server-principals (aanmeldingen) kunt u gebruikers en groepen van uw Azure AD-Tenant opgeven als echte instantie-principals met een exemplaar, waardoor elke bewerking op exemplaar niveau kan worden uitgevoerd, inclusief query's tussen data bases binnen hetzelfde beheerde exemplaar.

Er is een nieuwe syntaxis geïntroduceerd voor het maken van Azure AD-server-principals (aanmeldingen) **van externe provider**. Zie voor meer informatie over de syntaxis <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">aanmelden maken</a>en Bekijk het artikel [een Azure Active Directory beheerder voor SQL beheerd exemplaar inrichten](../database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) .

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integratie en meervoudige verificatie

Met SQL Managed Instance kunt u de identiteiten van database gebruikers en andere micro soft-services centraal beheren met [Azure Active Directory-integratie](../database/authentication-aad-overview.md). Deze mogelijkheid vereenvoudigt het beheer van machtigingen en verbetert de beveiliging. Azure Active Directory ondersteunt [multi-factor Authentication](../database/authentication-mfa-ssms-configure.md) om de beveiliging van gegevens en toepassingen te verbeteren tijdens het ondersteunen van een proces voor eenmalige aanmelding.

### <a name="authentication"></a>Verificatie

Verificatie met SQL Managed instance verwijst naar hoe gebruikers hun identiteit bewijzen wanneer ze verbinding maken met de data base. SQL Managed instance ondersteunt twee typen verificatie:  

- **SQL-verificatie**:

  Bij deze verificatiemethode wordt een combinatie van gebruikersnaam en wachtwoord gebruikt.
- **Azure Active Directory-verificatie**:

  Deze verificatie methode maakt gebruik van identiteiten die worden beheerd door Azure Active Directory en wordt ondersteund voor beheerde en geïntegreerde domeinen. Gebruik [waar mogelijk](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) Active Directory-verificatie (geïntegreerde beveiliging).

### <a name="authorization"></a>Autorisatie

Autorisatie verwijst naar wat een gebruiker kan doen binnen een data base in een Azure SQL Managed instance en wordt beheerd door de databaserol lidmaatschappen en object machtigingen van uw gebruikers account. SQL Managed instance heeft dezelfde autorisatie mogelijkheden als SQL Server 2017.

## <a name="database-migration"></a>Databasemigratie

SQL Managed instance streeft naar gebruikers scenario's met massale data base-migratie van on-premises of IaaS data base-implementaties. SQL Managed instance ondersteunt diverse opties voor database migratie:

### <a name="backup-and-restore"></a>Back-ups en herstellen  

De migratie benadering maakt gebruik van SQL-back-ups naar Azure Blob-opslag. Back-ups die zijn opgeslagen in een Azure Storage-BLOB kunnen rechtstreeks worden hersteld in een beheerd exemplaar met behulp van de [T-SQL-opdracht herstellen](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Zie [een back-upbestand herstellen naar een beheerd exemplaar](restore-sample-database-quickstart.md)voor een Snelstartgids waarin wordt getoond hoe u de Wide World Importers herstelt: standaard back-upbestand voor data base. In deze Quick start ziet u dat u een back-upbestand moet uploaden naar Azure Blob-opslag en het kunt beveiligen met behulp van een SAS-sleutel (Shared Access Signature).
- Zie [systeem eigen herstel van URL](migrate-to-instance-from-sql-server.md#native-restore-from-url)voor meer informatie over het terugzetten van URL.

> [!IMPORTANT]
> Back-ups van een beheerd exemplaar kunnen alleen worden hersteld naar een ander beheerd exemplaar. Ze kunnen niet worden hersteld naar een SQL Server-exemplaar of Azure SQL Database.

### <a name="database-migration-service"></a>Database Migration Service

Azure Database Migration Service is een volledig beheerde service die is ontworpen om naadloze migratie van meerdere database bronnen naar Azure-gegevens platforms mogelijk te maken met minimale downtime. Deze service stroomlijnt de taken die nodig zijn voor het verplaatsen van bestaande data bases van derden en SQL Server naar Azure SQL Database, Azure SQL Managed instance en SQL Server op Azure VM. Zie [de migratie van uw on-premises Data Base naar een met SQL beheerd exemplaar met behulp van database Migration service](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Ondersteunde SQL-functies

SQL Managed instance is erop gericht om de compatibiliteit van 100% surface area te bieden met de meest recente versie van SQL Server door middel van een gefaseerde release plan. Zie voor een functie en vergelijkings lijst het [onderdeel vergelijking van SQL Managed instance](../database/features-comparison.md)en voor een lijst met t-SQL-verschillen in SQL Managed instance versus SQL Server, Zie [SQL Managed instance T-SQL-verschillen van SQL Server](transact-sql-tsql-differences-sql-server.md).

SQL Managed instance ondersteunt achterwaartse compatibiliteit voor SQL Server 2008-data bases. Directe migratie van SQL Server 2005-database servers wordt ondersteund en het compatibiliteits niveau voor gemigreerde SQL Server 2005-data bases wordt bijgewerkt naar SQL Server 2008.
  
Het volgende diagram geeft een overzicht van surface area compatibiliteit in SQL Managed instance:  

![Migratie](./media/sql-managed-instance-paas-overview/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-sql-managed-instance"></a>De belangrijkste verschillen tussen SQL Server on-premises en SQL Managed instance

Voor delen van SQL Managed instance van zijn altijd up-to-date in de Cloud, wat betekent dat sommige functies in SQL Server mogelijk verouderd zijn, buiten gebruik worden gesteld of alternatieven hebben. Er zijn specifieke gevallen waarin hulpprogram ma's moeten herkennen dat een bepaalde functie op een iets andere manier werkt of dat de service wordt uitgevoerd in een omgeving die u niet volledig beheert.

Enkele belang rijke verschillen:

- Hoge Beschik baarheid is ingebouwd in en vooraf geconfigureerd met technologie die vergelijkbaar is met [on-beschikbaarheids groepen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Er zijn alleen geautomatiseerde back-ups en herstel naar een bepaald tijdstip. Klanten kunnen `copy-only` back-ups initiëren die geen conflicten met de automatische back-upketen hebben.
- Het opgeven van volledige fysieke paden wordt niet ondersteund, dus alle bijbehorende scenario's moeten anders worden ondersteund: Restore DB biedt geen ondersteuning voor het verplaatsen. CREATE DB staat geen fysieke paden toe, BULK INSERT werkt alleen met Azure-blobs, enzovoort.
- SQL Managed instance biedt ondersteuning voor [Azure AD-verificatie](../database/authentication-aad-overview.md) als een Cloud alternatief voor Windows-verificatie.
- SQL Managed instance beheert automatisch XTP-bestands groepen en-bestanden voor data bases die OLTP-objecten in het geheugen bevatten.
- SQL Managed instance ondersteunt SQL Server Integration Services (SSIS) en kan een SSIS-catalogus (SSISDB) hosten waarmee SSIS-pakketten worden opgeslagen, maar ze worden uitgevoerd op een beheerde Azure-SSIS Integration Runtime (IR) in Azure Data Factory. Zie [Azure-SSIS IR maken in Data Factory](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Zie [compare SQL database to SQL Managed instance](../../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-sql-database-and-sql-managed-instance)(Engelstalig) om de SSIS-functies te vergelijken.

### <a name="administration-features"></a>Beheer functies

Met SQL Managed instance kunnen systeem beheerders minder tijd best Eden aan beheer taken, omdat deze door de service voor u worden uitgevoerd of de taken aanzienlijk vereenvoudigt. Bijvoorbeeld [installatie en patching van het besturings systeem/RDBMS](../database/high-availability-sla.md), [dynamische instantie grootte en-configuratie](../database/single-database-scale.md), [back-ups](../database/automated-backups-overview.md), [database replicatie](replication-between-two-instances-configure-tutorial.md) (inclusief systeem databases), [configuratie van hoge Beschik baarheid](../database/high-availability-sla.md)en configuratie van gegevens stromen voor de status en [prestaties bewaking](../../azure-monitor/insights/azure-sql.md) .

Zie [een lijst met ondersteunde en niet-ondersteunde functies van SQL Managed instance](../database/features-comparison.md)en [T-SQL-verschillen tussen SQL Managed instance en SQL Server](transact-sql-tsql-differences-sql-server.md)voor meer informatie.

### <a name="programmatically-identify-a-managed-instance"></a>Programmatisch identificeren van een beheerd exemplaar

De volgende tabel bevat verschillende eigenschappen, die toegankelijk zijn via Transact-SQL, die u kunt gebruiken om te detecteren of uw toepassing werkt met een SQL-beheerd exemplaar en belang rijke eigenschappen op te halen.

|Eigenschap|Waarde|Opmerking|
|---|---|---|
|`@@VERSION`|Micro soft SQL Azure (RTM)-12.0.2000.8 2018-03-07 copyright (C) 2018 micro soft Corporation.|Deze waarde is hetzelfde als in SQL Database. Dit duidt **niet** op SQL-engine versie 12 (SQL Server 2014). SQL Managed instance voert altijd de nieuwste stabiele SQL-engine versie uit, die gelijk is aan of hoger is dan de laatste beschik bare RTM-versie van SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Deze waarde is hetzelfde als in SQL Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Deze waarde is een unieke aanduiding voor een beheerd exemplaar.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|De volledige DNS-naam van het exemplaar in de volgende indeling: `<instanceName>` . `<dnsPrefix>` . database.windows.net, waarbij `<instanceName>` de naam van de klant wordt verstrekt, terwijl `<dnsPrefix>` automatisch een deel van de naam wordt gegenereerd dat de uniekheid van de globale DNS-naam garandeert (bijvoorbeeld ' wcus17662feb9ce98 ')|Voor beeld: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Volgende stappen

- Zie [Quick Start Guide (Engelstalig](instance-create-quickstart.md)) voor meer informatie over het maken van uw eerste beheerde exemplaar.
- Zie [algemene SQL-functies](../database/features-comparison.md)voor een lijst met functies en vergelijkingen.
- Zie [vnet-configuratie voor SQL Managed instance](connectivity-architecture-overview.md)voor meer informatie over vnet-configuraties.
- Zie [een beheerd exemplaar maken](instance-create-quickstart.md)voor een Snelstartgids die een beheerd exemplaar maakt en een Data Base herstelt vanuit een back-upbestand.
- Voor een zelf studie over het gebruik van Azure Database Migration Service voor migratie raadpleegt u [migratie van SQL-beheerde exemplaren met behulp van database Migration service](../../dms/tutorial-sql-server-to-managed-instance.md).
- Zie [Azure SQL Managed instance bewaken met Azure SQL-analyse](../../azure-monitor/insights/azure-sql.md)voor geavanceerde bewaking van prestaties van SQL Managed instance-data bases met ingebouwde probleemoplossings informatie.
- Zie [SQL database prijzen](https://azure.microsoft.com/pricing/details/sql-database/managed/)voor prijs informatie.
