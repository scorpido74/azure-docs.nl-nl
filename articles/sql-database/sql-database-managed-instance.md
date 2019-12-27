---
title: Overzicht van SQL Managed instance
description: In dit artikel wordt Azure SQL Database beheerde instantie beschreven.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 11/27/2019
ms.openlocfilehash: 1a9c24846606c53fefa1ffc1de59f358524020c4
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707632"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>Wat is Azure SQL Database beheerde instantie?

Managed instance is een nieuwe implementatie optie van Azure SQL Database, die bijna 100% compatibel is met de meest recente data base-engine van SQL Server on-premises (Enter prise Edition), waarbij een VNet-implementatie (native [Virtual Network](../virtual-network/virtual-networks-overview.md) ) wordt geboden die veelvoorkomende beveiligings problemen behandelt, en een [bedrijfs model](https://azure.microsoft.com/pricing/details/sql-database/) dat voor on-premises SQL Server klanten kan worden gebruikt. Met het implementatie model voor beheerde instanties kunnen bestaande SQL Server klanten hun on-premises toepassingen naar de Cloud verplaatsen en naar een andere data base overzetten met minimale toepassings-en database wijzigingen. Tegelijkertijd behoudt de implementatie optie Managed instance alle PaaS-mogelijkheden (automatische patches en versie-updates, automatische [back-ups](sql-database-automated-backups.md), [hoge Beschik baarheid](sql-database-high-availability.md) ), waarmee de overhead en TCO van het beheer drastisch worden verminderd.

> [!IMPORTANT]
> Zie [ondersteunde regio's](sql-database-managed-instance-resource-limits.md#supported-regions)voor een lijst met regio's waarin de implementatie optie Managed Instance momenteel beschikbaar is.

Het volgende diagram geeft een overzicht van de belangrijkste functies van beheerde instanties:

![belangrijkste functies](./media/sql-database-managed-instance/key-features.png)

Het implementatie model voor Managed instances is ontworpen voor klanten die een groot aantal apps willen migreren van on-premises of IaaS, zelfgebouwde of ISV geleverde omgeving tot volledig beheerde PaaS-cloud omgeving, met zo weinig mogelijk migratie inspanningen. Met behulp van de volledig geautomatiseerde [gegevens migratie service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) in azure kunnen klanten hun on-premises SQL Server optillen en naar een beheerd exemplaar verplaatsen dat compatibiliteit biedt met SQL Server on-premises en volledige isolatie van klant instanties met systeem eigen VNet-ondersteuning.  Met Software Assurance kunt u hun bestaande licenties uitwisselen voor kortings tarieven voor een beheerd exemplaar met behulp [van de Azure Hybrid Benefit voor SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).  Een beheerd exemplaar is de beste migratie bestemming in de Cloud voor SQL Server instanties waarvoor een hoge beveiliging en een uitgebreid programmeerbaar Opper vlak zijn vereist.

De implementatie optie Managed instance is van toepassing op 100% surface area compatibiliteit met de meest recente on-premises SQL Server versie via een gefaseerde release plan.

Zie [de juiste versie van SQL Server kiezen in azure](sql-database-paas-vs-sql-server-iaas.md)om te bepalen welke Azure SQL database-implementatie opties u wilt: één data base, een gegroepeerde Data Base en een beheerd exemplaar en SQL Server gehost in de virtuele machine.

## <a name="key-features-and-capabilities"></a>Belangrijkste functies en mogelijkheden

Het beheerde exemplaar is een combi natie van de beste functies die beschikbaar zijn in Azure SQL Database en SQL Server data base-engine.

> [!IMPORTANT]
> Een beheerd exemplaar wordt uitgevoerd met alle functies van de meest recente versie van SQL Server, waaronder online bewerkingen, Automatische plannings correcties en andere verbeteringen voor bedrijfs prestaties. Een vergelijking van de beschik bare functies wordt uitgelegd in [functie vergelijking: Azure SQL database versus SQL Server](sql-database-features.md).

| **PaaS-voor delen** | **Bedrijfscontinuïteit** |
| --- | --- |
|Geen hardware kopen en beheren <br>Geen beheer overhead voor het beheren van de onderliggende infra structuur <br>Snelle inrichting en service schalen <br>Automatische patching en versie-upgrade <br>Integratie met andere PaaS-gegevens Services |SLA voor 99,99% uptime  <br>Ingebouwde [hoge Beschik baarheid](sql-database-high-availability.md) <br>Gegevens die worden beveiligd met [automatische back-ups](sql-database-automated-backups.md) <br>Door de klant Configureer bare Bewaar periode voor back-ups <br>Door de gebruiker geïnitieerde [back-ups](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[Herstel mogelijkheid voor Point-in-time database](sql-database-recovery-using-backups.md#point-in-time-restore) |
|**Beveiliging en naleving** | **Beheer**|
|Geïsoleerde omgeving ([VNet-integratie](sql-database-managed-instance-connectivity-architecture.md), single tenant service, specifieke reken kracht en opslag) <br>[TDE (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD-verificatie](sql-database-aad-authentication.md), ondersteuning voor eenmalige aanmelding <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD-server-principals (aanmeldingen)</a>  <br>Voldoet aan nalevings normen hetzelfde als Azure SQL database <br>[SQL-controle](sql-database-managed-instance-auditing.md) <br>[Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) |Azure Resource Manager-API voor het automatiseren van het inrichten en schalen van services <br>Azure Portal functionaliteit voor het hand matig inrichten en schalen van services <br>Gegevens migratie service

> [!IMPORTANT]
> Azure SQL Database (alle implementatie opties), is gecertificeerd op basis van een aantal nalevings standaarden. Zie het [vertrouwens centrum van Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) voor meer informatie over de meest recente lijst met SQL database nalevings certificeringen.

De belangrijkste functies van beheerde exemplaren worden weer gegeven in de volgende tabel:

|Functie | Beschrijving|
|---|---|
| Versie/build van SQL Server | SQL Server data base-engine (laatste stabiel) |
| Beheerde geautomatiseerde back-ups | Ja |
| Ingebouwde instantie-en database controle en-metrische gegevens | Ja |
| Automatische software patching | Ja |
| De nieuwste functies van de data base-engine | Ja |
| Aantal gegevens bestanden (rijen) per data base | Meerdere |
| Aantal logboek bestanden (logboek) per data base | 1 |
| VNet-Azure Resource Manager-implementatie | Ja |
| VNet-klassiek implementatie model | Nee |
| Portal ondersteuning | Ja|
| SSIS (ingebouwde integratie service) | No-SSIS maakt deel uit van [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| SSAS (ingebouwde Analysis Service) | Nee-SSAS is afzonderlijke [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Ingebouwde Reporting service (SSRS) | Geen gebruik Power BI of SSRS IaaS |
|||

## <a name="vcore-based-purchasing-model"></a>Aankoopmodel op basis van vCore

Het [op vCore gebaseerde aankoop model](sql-database-service-tiers-vcore.md) voor beheerde instanties biedt flexibiliteit, controle, transparantie en een eenvoudige manier om on-premises werkbelasting vereisten te vertalen naar de Cloud. Met dit model kunt u de reken capaciteit, het geheugen en de opslag aanpassen op basis van de behoeften van uw werk belasting. Het vCore-model komt ook in aanmerking voor een besparing van Maxi maal 30 procent met de [Azure Hybrid Benefit voor SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).

In vCore-model kunt u kiezen tussen generaties van hardware.

- **Gen4** Logische Cpu's zijn gebaseerd op Intel E5-2673 v3-processors (Haswell 2,4), gekoppelde SSD, fysieke kernen, 7 GB RAM per kern en reken grootten tussen 8 en 24 vCores.
- **GEN5** Logische Cpu's zijn gebaseerd op Intel E5-2673 v4 (Broadwell) 2,3-GHz en Intel SP-8160-processors (Skylake), Fast NVMe SSD, Hyper-Threaded logische core en reken grootten tussen 4 en 80 kernen.

Vind meer informatie over het verschil tussen hardware-generaties in de [resource limieten voor beheerde exemplaren](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

> [!IMPORTANT]
> Nieuwe Gen4-data bases worden niet meer ondersteund in de Australië-oost-of Brazilië-zuid regio's.

## <a name="managed-instance-service-tiers"></a>Service lagen van beheerd exemplaar

Het beheerde exemplaar is beschikbaar in twee service lagen:

- **Algemeen gebruik**: ontworpen voor toepassingen met typische prestaties en i/o-latentie vereisten.
- **Bedrijfs kritiek**: ontworpen voor toepassingen met lage i/o-latentie vereisten en minimale impact van onderliggende onderhouds bewerkingen op de werk belasting.

Beide service lagen garanderen een Beschik baarheid van 99,99% en bieden u de mogelijkheid om de opslag grootte en de berekenings capaciteit onafhankelijk te selecteren. Zie [hoge Beschik baarheid en Azure SQL database](sql-database-high-availability.md)voor meer informatie over de architectuur met hoge Beschik baarheid van Azure SQL database.

### <a name="general-purpose-service-tier"></a>Service tier voor algemeen gebruik

De volgende lijst bevat een beschrijving van het sleutel kenmerk van de Algemeen servicelaag:

- Ontwerp voor het meren deel van zakelijke toepassingen met typische prestatie vereisten
- Azure Blob-opslag met hoge prestaties (8 TB)
- Ingebouwde [hoge Beschik baarheid](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) op basis van betrouw bare Azure Blob-opslag en [Azure service Fabric](../service-fabric/service-fabric-overview.md)

Zie [Storage Layer in algemeen doel tier](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) en [Best practices voor opslag prestaties en overwegingen voor beheerde instanties (algemeen gebruik)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/)voor meer informatie.

Meer informatie over het verschil tussen service lagen in de [resource limieten voor beheerde exemplaren](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Bedrijfskritiek servicelaag

Bedrijfskritiek servicelaag is gebouwd voor toepassingen met hoge i/o-vereisten. Het biedt de hoogste flexibiliteit voor storingen met behulp van verschillende geïsoleerde replica's.

De volgende lijst geeft een overzicht van de belangrijkste kenmerken van de Bedrijfskritiek servicelaag:

- Ontworpen voor zakelijke toepassingen met de meeste vereisten voor prestaties en HA
- Wordt geleverd met super snelle lokale SSD-opslag (Maxi maal 1 TB op Gen4 en Maxi maal 4 TB op GEN5)
- Ingebouwde [hoge Beschik baarheid](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) op basis van AlwaysOn- [beschikbaarheids groepen](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) en [Azure service Fabric](../service-fabric/service-fabric-overview.md).
- Ingebouwde aanvullende [alleen-lezen database replica](sql-database-read-scale-out.md) die kan worden gebruikt voor rapportage en andere alleen-lezen workloads
- [In-Memory OLTP](sql-database-in-memory.md) die kan worden gebruikt voor workloads met hoge prestatie vereisten  

Meer informatie over het verschil tussen service lagen in de [resource limieten voor beheerde exemplaren](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).


## <a name="managed-instance-management-operations"></a>Beheer bewerkingen voor beheerde exemplaren

Azure SQL Database biedt beheerbewerkingen die u kunt gebruiken om automatisch nieuwe beheerde exemplaren te implementeren, eigenschappen van exemplaren bij te werken en exemplaren te verwijderen als ze niet meer nodig zijn. Deze sectie bevat informatie over beheer bewerkingen en hun typische duur.

Voor de ondersteuning van [implementaties in virtuele netwerken van Azure (VNets)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) en voor het afschermen en beveiligen van klanten is een beheerd exemplaar afhankelijk van [virtuele clusters](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture), die een specifieke set geïsoleerde virtuele machines vertegenwoordigen die zijn geïmplementeerd in het subnet van het virtuele netwerk van de klant. In wezen resulteert elke implementatie van een beheerd exemplaar in een leeg subnet in een nieuw virtueel cluster buildout.

Volgende bewerkingen in geïmplementeerde beheerde instanties kunnen ook gevolgen hebben voor het onderliggende virtuele cluster. Dit is van invloed op de duur van beheer bewerkingen, zoals het implementeren van aanvullende virtuele machines wordt geleverd met een overhead die moet worden overwogen wanneer u nieuwe implementaties of updates voor bestaande beheerde exemplaren plant.

Alle beheerbewerkingen kunnen als volgt worden gecategoriseerd:

- Implementatie van instanties (nieuwe instantie maken). 
- Update van instantie (instantie-eigenschappen wijzigen, zoals vCores of gereserveerde opslag).
- Instantie verwijderen.

Normaal gesp roken hebben bewerkingen op virtuele clusters het langst. De duur van de bewerkingen op virtuele clusters verschilt: Hieronder staan de waarden die u doorgaans kunt verwachten, op basis van de bestaande telemetrie-gegevens van de service:

- Virtueel cluster maken. Dit is een synchrone stap in bewerkingen voor het beheer van exemplaren. **90% van de bewerkingen zijn voltooid over vier uur**.
- Grootte van het virtuele cluster wijzigen (uitbrei ding of krimpen). Uitbrei ding is een synchrone stap, terwijl het comprimeren wordt asynchroon uitgevoerd (zonder invloed op de duur van de beheer bewerkingen van het exemplaar). **90% van de cluster uitbreidingen eindigen in minder dan 2,5 uur**.
- Virtueel cluster wordt verwijderd. Verwijderen is een asynchrone stap, maar kan ook [hand matig worden gestart](sql-database-managed-instance-delete-virtual-cluster.md) op een leeg virtueel cluster. in dat geval wordt het synchroon uitgevoerd. **90% van verwijderde virtuele clusters is in 1,5 uur voltooid**.

Daarnaast kan het beheer van instanties ook een van de bewerkingen op gehoste data bases bevatten, die de duur van een langere periode in beslag neemt:

- Database bestanden van Azure Storage te koppelen. Dit is een synchrone stap, zoals Compute (vCore), of het omhoog of omlaag schalen van opslag in de laag Algemeen. **90% van deze bewerkingen zijn voltooid in 5 minuten**.
- AlwaysOn-beschikbaarheids groep is in seeding. Dit is een synchrone stap, zoals Compute (vCore), of opslag schalen in de laag Bedrijfskritiek en bij het wijzigen van de servicelaag van Algemeen in Bedrijfskritiek (of andersom). De duur van deze bewerking is evenredig met de totale database grootte en de huidige database activiteit (aantal actieve trans acties). Data base-activiteit bij het bijwerken van een exemplaar kan aanzienlijk verschillen veroorzaken in de totale duur. **90% van deze bewerkingen worden uitgevoerd om 220 GB/uur of hoger**.

De volgende tabel bevat een overzicht van de bewerkingen en typische totale duur:

|Category  |Bewerking  |Langlopend segment  |Geschatte duur  |
|---------|---------|---------|---------|
|**Implementatie** |Eerste instantie in een leeg subnet|Virtueel cluster maken|90% van de bewerkingen zijn voltooid in 4 uur|
|Implementatie |Eerste exemplaar van een andere hardware-generatie in een niet-leeg subnet (bijvoorbeeld eerste generatie 5-exemplaar in een subnet met exemplaren van de generatie 4)|Virtueel cluster maken *|90% van de bewerkingen zijn voltooid in 4 uur|
|Implementatie |Eerste instantie maken van 4 vCores, in een leeg of niet-leeg subnet|Virtueel cluster maken * *|90% van de bewerkingen zijn voltooid in 4 uur|
|Implementatie |Het maken van de volgende instantie binnen het niet-lege subnet (2e, 3e, enz.)|Verg Roten/verkleinen van virtueel cluster|90% van de bewerkingen zijn voltooid in 2,5 uur|
|**Update** |Wijziging van instantie-eigenschap (beheerders wachtwoord, AAD-aanmelding, Azure Hybrid Benefit vlag)|N/A|Maxi maal 1 minuut|
|Bijwerken |Opslag van exemplaren omhoog/omlaag schalen (Algemeen servicelaag)|-Verg Roten/verkleinen van virtueel cluster<br>-Database bestanden koppelen|90% van de bewerkingen zijn voltooid in 2,5 uur|
|Bijwerken |Opslag van exemplaren omhoog/omlaag schalen (Bedrijfskritiek servicelaag)|-Verg Roten/verkleinen van virtueel cluster<br>-Always on-beschikbaarheids groep seeding|90% van de bewerkingen zijn voltooid in 2,5 uur + tijd voor het seeden van alle data bases (220 GB/uur)|
|Bijwerken |VCores (instance Compute) omhoog en omlaag schalen (Algemeen)|-Verg Roten/verkleinen van virtueel cluster<br>-Database bestanden koppelen|90% van de bewerkingen zijn voltooid in 2,5 uur|
|Bijwerken |VCores (instance Compute) omhoog en omlaag schalen (Bedrijfskritiek)|-Verg Roten/verkleinen van virtueel cluster<br>-Always on-beschikbaarheids groep seeding|90% van de bewerkingen zijn voltooid in 2,5 uur + tijd voor het seeden van alle data bases (220 GB/uur)|
|Bijwerken |Exemplaar wordt omlaag geschaald naar 4 vCores (Algemeen)|-Het wijzigen van het formaat van het virtuele cluster (als dit voor de eerste keer wordt uitgevoerd, kan het maken van een virtueel cluster vereist zijn * *)<br>-Database bestanden koppelen|90% van de bewerkingen zijn voltooid in 4 uur 5 min * *|
|Bijwerken |Exemplaar wordt omlaag geschaald naar 4 vCores (Algemeen)|-Het wijzigen van het formaat van het virtuele cluster (als dit voor de eerste keer wordt uitgevoerd, kan het maken van een virtueel cluster vereist zijn * *)<br>-Always on-beschikbaarheids groep seeding|90% van de bewerkingen zijn voltooid in 4 uur + tijd voor het seeden van alle data bases (220 GB/uur)|
|Bijwerken |Wijziging van de instantie-servicelaag (Algemeen Bedrijfskritiek en omgekeerd)|-Verg Roten/verkleinen van virtueel cluster<br>-Always on-beschikbaarheids groep seeding|90% van de bewerkingen zijn voltooid in 2,5 uur + tijd voor het seeden van alle data bases (220 GB/uur)|
|**Bedoeld**|Verwijdering van exemplaar|Back-ups van staart vastleggen voor alle data bases|90% bewerkingen zijn Maxi maal 1 minuut voltooid.<br>Opmerking: als het laatste exemplaar van het subnet wordt verwijderd, wordt het verwijderen van het virtuele cluster na 12 uur door deze bewerking gepland.|
|Verwijdering|Virtueel cluster verwijderen (als door de gebruiker geïnitieerde bewerking)|Virtueel cluster verwijderen|90% van de bewerkingen zijn Maxi maal 1,5 uur voltooid|

\* virtuele cluster is gebouwd per generatie van hardware.

\*\* de implementatie optie voor 4 vCores is uitgebracht in juni 2019 en is een nieuwe versie van het virtuele cluster vereist. Als er instanties in het doel-subnet stonden die al zijn gemaakt vóór 12 juni, wordt er automatisch een nieuw virtueel cluster geïmplementeerd voor het hosten van 4 vCore-instanties.

\*\*\* 12 uur de huidige configuratie, maar dit kan in de toekomst veranderen, dus neem geen vaste afhankelijkheid op. Als u eerder een virtueel cluster moet verwijderen (als u het subnet bijvoorbeeld wilt vrijgeven), raadpleegt u [een subnet verwijderen na het verwijderen van een door Azure SQL database beheerd exemplaar](sql-database-managed-instance-delete-virtual-cluster.md).

### <a name="instance-availability-during-management"></a>Beschik baarheid exemplaar tijdens beheer

Beheerde exemplaren zijn niet beschikbaar voor client toepassingen tijdens implementatie-en verwijderings bewerkingen.

Er zijn beheerde exemplaren beschikbaar tijdens update bewerkingen, maar er is een korte downtime die wordt veroorzaakt door de failover die aan het einde van updates die doorgaans Maxi maal tien seconden duren.

> [!IMPORTANT]
> De duur van een failover kan aanzienlijk verschillen in het geval van langlopende trans acties die plaatsvinden op de data bases vanwege een [langdurige herstel tijd](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process). Daarom is het niet raadzaam om reken kracht of opslag van Azure SQL Database beheerde instantie te schalen of tegelijkertijd een service tier te wijzigen met de langlopende trans acties (gegevens importeren, gegevens verwerkings taken, index Rebuild, enzovoort). De data base-failover die aan het einde van de bewerking wordt uitgevoerd, annuleert lopende trans acties en resulteert in langdurige herstel tijd.

[Versneld database herstel](sql-database-accelerated-database-recovery.md) is momenteel niet beschikbaar voor Azure SQL database Managed instances. Wanneer deze functie is ingeschakeld, wordt de variabiliteit van de failover-tijd aanzienlijk verminderd, zelfs in het geval van langlopende trans acties.



## <a name="advanced-security-and-compliance"></a>Geavanceerde beveiliging en naleving van voorschriften

De implementatie optie Managed instance combineert geavanceerde beveiligings functies van Azure Cloud en SQL Server data base-engine.

### <a name="managed-instance-security-isolation"></a>Beveiligings isolatie van beheerd exemplaar

Een beheerd exemplaar biedt extra beveiligings isolatie van andere tenants in de Azure-Cloud. Beveiligings isolatie omvat:

- [Systeem eigen virtuele netwerk implementatie](sql-database-managed-instance-connectivity-architecture.md) en connectiviteit met uw on-premises omgeving met behulp van Azure Express Route of VPN gateway.
- In een standaard implementatie wordt SQL-eind punt alleen weer gegeven via een privé-IP-adres, waardoor er veilige connectiviteit mogelijk is vanuit persoonlijke Azure-of hybride netwerken.
- Eén Tenant met toegewezen onderliggende infra structuur (compute, Storage).

Het volgende diagram geeft een overzicht van de verschillende connectiviteits opties voor uw toepassingen:

![hoge Beschik baarheid](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Zie voor meer informatie over VNet-integratie en het afdwingen van het netwerk beleid op subnetniveau [vnet-architectuur voor beheerde instanties](sql-database-managed-instance-connectivity-architecture.md) en [Verbind uw toepassing met een beheerd exemplaar](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Plaats meerdere beheerde instanties in hetzelfde subnet, waar deze worden toegestaan door uw beveiligings vereisten, zodat u extra voor delen krijgt. Collocating-instanties in hetzelfde subnet vereenvoudigen het beheer van de netwerk infrastructuur aanzienlijk en beperken de inrichtings tijd, omdat lange inrichtings duur is gekoppeld aan de kosten voor het implementeren van het eerste beheerde exemplaar in een subnet.

### <a name="azure-sql-database-security-features"></a>Azure SQL Database beveiligings functies

Azure SQL Database biedt een aantal geavanceerde beveiligings functies die kunnen worden gebruikt voor het beveiligen van uw gegevens.

- [Controle van beheerde exemplaren](sql-database-managed-instance-auditing.md) houdt database gebeurtenissen bij en schrijft deze naar een audit logboek bestand dat in uw Azure Storage-account is geplaatst. Met controle kunt u de naleving van regelgeving, inzicht krijgen in database activiteiten en inzicht verkrijgen in verschillen en afwijkingen die kunnen wijzen op problemen met het bedrijf of vermoedelijke beveiligings schendingen.
- Gegevens versleuteling in beweging: een beheerd exemplaar beveiligt uw gegevens door versleuteling te bieden voor gegevens in beweging met behulp van Transport Layer Security. Naast de beveiliging van trans port Layer biedt de implementatie optie Managed instance beveiliging van gevoelige gegevens in vlucht, op rest en tijdens de verwerking van query's met [Always encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Het unieke Always Encrypted biedt ongeëvenaarde beveiliging tegen diefstal van kritieke gegevens. Met Always Encrypted worden creditcard nummers bijvoorbeeld in de data base versleuteld, zelfs tijdens de query verwerking, voor ontsleuteling op het punt van gebruik toegestaan door geautoriseerde mede werkers of toepassingen die deze gegevens moeten verwerken.
- [Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) is een aanvulling op de [controle](sql-database-managed-instance-auditing.md) door een extra laag van beveiligings informatie op te geven die is ingebouwd in de service en die ongebruikelijke en mogelijk schadelijke pogingen voor toegang tot of exploiten van data bases detecteert. U wordt gewaarschuwd over verdachte activiteiten, mogelijke beveiligings problemen en SQL-injectie aanvallen, evenals afwijkende database toegangs patronen. Geavanceerde beveiligings waarschuwingen voor bedreigingen kunnen worden weer gegeven in [Azure Security Center](https://azure.microsoft.com/services/security-center/) en geven details van verdachte activiteiten en aanbevolen actie voor het onderzoeken en oplossen van de dreiging.  
- [Dynamische gegevens maskering](/sql/relational-databases/security/dynamic-data-masking) beperkt de bloot stelling van gevoelige gegevens door deze te maskeren voor niet-bevoegde gebruikers. Dynamische gegevens maskering helpt onbevoegde toegang tot gevoelige gegevens te voor komen, doordat u kunt opgeven hoeveel gevoelige gegevens moeten worden weer gegeven met minimale gevolgen voor de toepassingslaag. Dit is een beveiligingsfunctie op basis van beleid. De gevoelige gegevens in de resultatenset van een query die is uitgevoerd op toegewezen databasevelden worden verborgen, terwijl de gegevens in de database niet worden gewijzigd.
- Met [beveiliging op rijniveau](/sql/relational-databases/security/row-level-security) kunt u de toegang tot rijen in een database tabel beheren op basis van de kenmerken van de gebruiker die een query uitvoert (bijvoorbeeld per groepslid maatschap of uitvoerings context). Beveiliging op rijniveau (RLS) vereenvoudigt het ontwerp en de code van de beveiliging in uw toepassing. Met RLS kunt u beperkingen instellen voor de toegang tot gegevens in rijen. Bijvoorbeeld, zodat werk nemers alleen toegang hebben tot de gegevens rijen die relevant zijn voor hun afdeling, of de toegang tot gegevens beperken tot alleen de relevante gegevens.
- [Transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) versleutelt gegevens bestanden van beheerde exemplaren, ook wel het versleutelen van gegevens in rust. TDE voert realtime-I/O-versleuteling en ontsleuteling van de gegevens en logboek bestanden uit. De versleuteling maakt gebruik van een database versleutelings sleutel (DEK), die is opgeslagen in de data base boot record voor Beschik baarheid tijdens het herstel. U kunt al uw data bases in een beheerd exemplaar beveiligen met transparante gegevens versleuteling. TDE SQL Server is een beproefde, op rest gebaseerde technologie die wordt vereist door veel nalevings standaarden om te beschermen tegen dief stal van opslag media.

Migratie van een versleutelde data base naar een beheerd exemplaar wordt ondersteund via de Azure Database Migration Service (DMS) of systeem eigen herstel. Als u van plan bent een versleutelde data base te migreren met behulp van systeem eigen herstel, is de migratie van het bestaande TDE-certificaat van de SQL Server on-premises of SQL Server in een virtuele machine naar een beheerd exemplaar een vereiste stap. Zie [SQL Server-exemplaar migratie naar een beheerd exemplaar](sql-database-managed-instance-migrate.md)voor meer informatie over migratie opties.

## <a name="azure-active-directory-integration"></a>Integratie van Azure Active Directory

De implementatie optie Managed instance ondersteunt traditionele SQL server data base engine-aanmeldingen en aanmeldingen die zijn geïntegreerd met Azure Active Directory (AAD). Azure AD server-principals (aanmeldingen) (**open bare preview**) zijn de Azure-Cloud versie van on-premises data base-aanmeldingen die u in uw on-premises omgeving gebruikt. Met Azure AD-server-principals (aanmeldingen) kunt u gebruikers en groepen van uw Azure Active Directory-Tenant opgeven als echte instanties met een exemplaar van het bereik, met inbegrip van query's voor meerdere data bases binnen dezelfde beheerde exemplaar.

Er is een nieuwe syntaxis geïntroduceerd voor het maken van Azure AD-server-principals (aanmeldingen) **van externe provider**. Raadpleeg voor meer informatie over de syntaxis <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">aanmelden maken</a>en lees het artikel [een Azure Active Directory beheerder inrichten voor uw beheerde exemplaar](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) .

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integratie en meervoudige verificatie

Met de implementatie optie Managed Instance kunt u de identiteiten van database gebruikers en andere micro soft-services centraal beheren met [Azure Active Directory-integratie](sql-database-aad-authentication.md). Deze mogelijkheid vereenvoudigt het beheer van machtigingen en verbetert de beveiliging. Azure Active Directory ondersteunt [Multi-Factor Authentication](sql-database-ssms-mfa-authentication-configure.md) (MFA) voor betere beveiliging van gegevens en toepassingen, en ondersteunt ook een proces voor eenmalige aanmelding (SSO).

### <a name="authentication"></a>Verificatie

Verificatie van beheerde exemplaren verwijst naar hoe gebruikers hun identiteit bewijzen wanneer ze verbinding maken met de data base. SQL Database ondersteunt twee typen verificatie:  

- **SQL-verificatie**:

  Deze verificatie methode maakt gebruik van een gebruikers naam en wacht woord.
- **Azure Active Directory-verificatie**:

  Deze verificatie methode maakt gebruik van identiteiten die worden beheerd door Azure Active Directory en wordt ondersteund voor beheerde en geïntegreerde domeinen. Gebruik [waar mogelijk](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode) Active Directory-verificatie (geïntegreerde beveiliging).

### <a name="authorization"></a>Autorisatie

Autorisatie verwijst naar wat een gebruiker kan doen binnen een Azure SQL Database en wordt beheerd door de databaserol lidmaatschappen van uw gebruikers account en machtigingen op object niveau. Een beheerd exemplaar heeft dezelfde autorisatie mogelijkheden als SQL Server 2017.

## <a name="database-migration"></a>Databasemigratie

De implementatie optie Managed instance streeft naar gebruikers scenario's met massale database migratie van on-premises of IaaS data base-implementaties. Beheerd exemplaar ondersteunt diverse opties voor database migratie:

### <a name="back-up-and-restore"></a>Back-ups maken en terugzetten  

De migratie benadering maakt gebruik van SQL-back-ups naar Azure Blob-opslag. Back-ups die zijn opgeslagen in een Azure Storage-BLOB kunnen rechtstreeks worden hersteld in een beheerd exemplaar met behulp van de [T-SQL-opdracht herstellen](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Zie [een back-upbestand herstellen naar een beheerd exemplaar](sql-database-managed-instance-get-started-restore.md)voor een Snelstartgids waarin wordt getoond hoe u de Wide World Importers herstelt: standaard back-upbestand voor data base. In deze Quick start ziet u dat u een back-upbestand moet uploaden naar Azure Blob Storage en het kunt beveiligen met behulp van een SAS-sleutel (Shared Access Signature).
- Zie [systeem eigen herstel van URL](sql-database-managed-instance-migrate.md#native-restore-from-url)voor meer informatie over het terugzetten van URL.

> [!IMPORTANT]
> Back-ups van een beheerd exemplaar kunnen alleen worden hersteld naar een ander beheerd exemplaar. Ze kunnen niet worden teruggezet naar een on-premises SQL Server of naar één data base/elastische pool.

### <a name="data-migration-service"></a>Gegevens migratie service

De Azure Database Migration Service is een volledig beheerde service die is ontworpen om naadloze migraties van meerdere database bronnen naar Azure-gegevens platforms mogelijk te maken met minimale downtime. Deze service stroomlijnt de taken die nodig zijn om bestaande derden en SQL Server data bases te verplaatsen naar Azure SQL Database (afzonderlijke data bases, gepoolde data bases in elastische Pools en data bases in een beheerd exemplaar) en SQL Server in azure VM. Zie [uw on-premises data base migreren naar een beheerd exemplaar met behulp van DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Ondersteunde SQL-functies

De implementatie optie Managed instance is erop gericht om te voorzien in een dichtheid van 100% surface area compatibiliteit met on-premises SQL Server die in fasen worden geïntroduceerd tot algemene Beschik baarheid van de service. Zie [SQL database functie vergelijking](sql-database-features.md)en voor een lijst met t-SQL-verschillen in beheerde exemplaren versus SQL Server voor een overzicht van de functies en de vergelijkings lijst [Managed instance T-SQL-verschillen van SQL Server](sql-database-managed-instance-transact-sql-information.md).

De implementatie optie Managed instance ondersteunt achterwaartse compatibiliteit met SQL 2008-data bases. Directe migratie van SQL 2005-database servers wordt ondersteund, het compatibiliteits niveau voor gemigreerde SQL 2005-data bases wordt bijgewerkt naar SQL 2008.
  
Het volgende diagram geeft een overzicht van surface area compatibiliteit in het beheerde exemplaar:  

![virtuelemachinemigratie](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>De belangrijkste verschillen tussen SQL Server on-premises en in een beheerd exemplaar

De implementatie van de Managed instance-optie voor delen is altijd up-to-date in de Cloud. Dit betekent dat sommige functies in on-premises SQL Server mogelijk verouderd, buiten gebruik worden gesteld of alternatieven hebben. Er zijn specifieke gevallen waarin hulpprogram ma's moeten herkennen dat een bepaalde functie op een iets andere manier werkt of dat de service niet volledig wordt beheerd:

- Hoge Beschik baarheid is ingebouwd in en vooraf geconfigureerd met technologie die vergelijkbaar is met de [beschikbaarheids groepen altijd](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Automatische back-ups en herstel naar een bepaald tijdstip. De klant kan `copy-only` back-ups initiëren die geen conflicten met de automatische back-upketen hebben.
- Het beheerde exemplaar staat niet toe dat de volledige fysieke paden worden opgegeven, zodat alle overeenkomende scenario's anders moeten worden ondersteund: Restore DB biedt geen ondersteuning voor het verplaatsen. CREATE DB staat geen fysieke paden toe, BULK INSERT werkt alleen met Azure-blobs, enzovoort.
- Beheerd exemplaar ondersteunt [Azure AD-verificatie](sql-database-aad-authentication.md) als Cloud alternatief voor Windows-verificatie.
- Beheerd exemplaar beheert automatisch de bestands groep en bestanden van XTP voor data bases die OLTP-objecten in het geheugen bevatten
- Managed instance ondersteunt SQL Server Integration Services (SSIS) en kan SSIS Catalog (SSISDB) die SSIS-pakketten opslaat, maar ze worden uitgevoerd op een beheerde Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF), Zie [Azure-SSIS IR maken in ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Zie [een Azure SQL database afzonderlijke Data Base, elastische pool en beheerde instantie vergelijken](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)om de SSIS-functies in SQL database te vergelijken.

### <a name="managed-instance-administration-features"></a>Beheer functies voor beheerde exemplaren

Met de implementatie optie Managed instance kan de systeem beheerder minder tijd best Eden aan beheer taken, omdat de SQL Database-Service deze taken voor u uitvoert of veel vereenvoudigt. Bijvoorbeeld [installatie en patching van het besturings systeem/RDBMS](sql-database-high-availability.md), [dynamische instantie grootte en-configuratie](sql-database-single-database-scale.md), [back-ups](sql-database-automated-backups.md), [database replicatie](replication-with-sql-database-managed-instance.md) (inclusief systeem databases), [configuratie van hoge Beschik baarheid](sql-database-high-availability.md)en configuratie van gegevens stromen voor de status en [prestaties bewaking](../azure-monitor/insights/azure-sql.md) .

> [!IMPORTANT]
> Zie [SQL database-functies](sql-database-features.md)voor een lijst met ondersteunde, gedeeltelijk ondersteunde en niet-ondersteunde functies. Zie voor een overzicht van de T-SQL-verschillen in beheerde exemplaren versus SQL Server [Managed instance T-SQL-verschillen van SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Een beheerd exemplaar programmatisch identificeren

De volgende tabel bevat verschillende eigenschappen, toegankelijk via Transact SQL, die u kunt gebruiken om te detecteren of uw toepassing werkt met een beheerd exemplaar en belang rijke eigenschappen op te halen.

|Eigenschap|Waarde|Opmerking|
|---|---|---|
|`@@VERSION`|Micro soft SQL Azure (RTM)-12.0.2000.8 2018-03-07 copyright (C) 2018 micro soft Corporation.|Deze waarde is hetzelfde als in SQL Database.|
|`SERVERPROPERTY ('Edition')`|SQL Azure|Deze waarde is hetzelfde als in SQL Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Deze waarde is een unieke aanduiding voor een beheerd exemplaar.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|De volledige DNS-naam van het exemplaar in de volgende indeling:`<instanceName>``<dnsPrefix>`. database.windows.net, waarbij `<instanceName>` de naam is van de klant, terwijl `<dnsPrefix>` automatisch wordt gegenereerd deel van de naam die de uniekheid van de globale DNS-naam garandeert (bijvoorbeeld ' wcus17662feb9ce98 ')|Voor beeld: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Volgende stappen

- Zie [Quick Start Guide (Engelstalig](sql-database-managed-instance-get-started.md)) voor meer informatie over het maken van uw eerste beheerde exemplaar.
- Zie [algemene SQL-functies](sql-database-features.md)voor een lijst met functies en vergelijkingen.
- Zie [vnet-configuratie voor beheerde exemplaren](sql-database-managed-instance-connectivity-architecture.md)voor meer informatie over vnet-configuratie.
- Zie [een beheerd exemplaar maken](sql-database-managed-instance-get-started.md)voor een Snelstartgids die een beheerd exemplaar maakt en een Data Base herstelt vanuit een back-upbestand.
- Zie [migratie van beheerde exemplaren met](../dms/tutorial-sql-server-to-managed-instance.md)behulp van DMS voor een zelf studie met behulp van de Azure database MIGRATION service (DMS) voor migratie.
- Zie [Azure SQL database bewaken met behulp van Azure SQL-analyse](../azure-monitor/insights/azure-sql.md)voor geavanceerde bewaking van beheerde exemplaar database prestaties met ingebouwde probleemoplossings informatie.
- Zie [prijzen van beheerde exemplaren SQL database](https://azure.microsoft.com/pricing/details/sql-database/managed/)voor prijs informatie.
