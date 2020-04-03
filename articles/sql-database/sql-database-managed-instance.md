---
title: SQL managed instance overzicht
description: In dit artikel wordt beschreven dat azure SQL Database beheerde instantie.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 04/02/2020
ms.openlocfilehash: 06242af6cb00e3adebbc80da722898fb8e348e36
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585360"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>Wat wordt azure SQL Database beheerd instantie?

Beheerde instantie is een nieuwe implementatieoptie van Azure SQL Database, die bijna 100% compatibel is met de nieuwste SQL Server on-premises (Enterprise Edition) Database Engine, die een native [virtual network (VNet)-implementatie](../virtual-network/virtual-networks-overview.md) biedt die algemene beveiligingsproblemen aanpakt, en een [bedrijfsmodel](https://azure.microsoft.com/pricing/details/sql-database/) dat gunstig is voor on-premises SQL Server-klanten. Met het beheerde implementatiemodel voor instance kunnen bestaande SQL Server-klanten hun on-premises toepassingen naar de cloud tillen en verplaatsen met minimale wijzigingen in toepassingen en database. Tegelijkertijd behoudt de beheerde optie voor het implementeren van instanties alle PaaS-mogelijkheden (automatische patching en versie-updates, [geautomatiseerde back-ups](sql-database-automated-backups.md), [hoge beschikbaarheid),](sql-database-high-availability.md) die de overhead van het beheer en TCO drastisch verminderen.

> [!IMPORTANT]
> Zie [Ondersteunde regio's](sql-database-managed-instance-resource-limits.md#supported-regions)voor een lijst met regio's waarin de optie voor beheerde instantie-implementatie momenteel beschikbaar is.

In het volgende diagram worden de belangrijkste kenmerken van beheerde instanties beschreven:

![belangrijkste functies](./media/sql-database-managed-instance/key-features.png)

Het beheerde implementatiemodel voor instanties is ontworpen voor klanten die een groot aantal apps willen migreren van on-premises of IaaS, zelfgebouwde of ISV-omgeving naar een volledig beheerde PaaS-cloudomgeving, met een zo laag mogelijke migratie-inspanning. Met behulp van de volledig geautomatiseerde [Data Migration Service (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) in Azure kunnen klanten hun on-premises SQL Server optillen en verschuiven naar een beheerde instantie die compatibiliteit biedt met SQL Server on-premises en volledige isolatie van klantexemplaren met native VNet-ondersteuning.  Met Software Assurance u uw bestaande licenties inruilen voor gereduceerde tarieven op een beheerde instantie met behulp van het [Azure Hybrid Benefit voor SQL Server.](https://azure.microsoft.com/pricing/hybrid-benefit/)  Een beheerde instantie is de beste migratiebestemming in de cloud voor SQL Server-exemplaren waarvoor hoge beveiliging en een rijk programmeeroppervlak vereist zijn.

De beheerde optie voor het implementeren van instanties biedt bijna 100% surface area-compatibiliteit met de nieuwste on-premises SQL Server-versie via een gefaseerd releaseplan.

Als u wilt kiezen tussen de implementatieopties van Azure SQL Database: één database, samengevoegde database en beheerde instantie en SQL Server die in een virtuele machine worden gehost, [raadpleegt u hoe u de juiste versie van SQL Server in Azure kiest.](sql-database-paas-vs-sql-server-iaas.md)

## <a name="key-features-and-capabilities"></a>Belangrijkste functies en mogelijkheden

Beheerde instantie combineert de beste functies die beschikbaar zijn in Azure SQL Database en SQL Server Database Engine.

> [!IMPORTANT]
> Een beheerde instantie wordt uitgevoerd met alle functies van de meest recente versie van SQL Server, inclusief onlinebewerkingen, automatische plancorrecties en andere verbeteringen in de bedrijfsprestaties. Een vergelijking van de beschikbare functies wordt uitgelegd in [Functievergelijking: Azure SQL Database versus SQL Server](sql-database-features.md).

| **PaaS voordelen** | **Bedrijfscontinuïteit** |
| --- | --- |
|Geen hardware-inkoop en -beheer <br>Geen beheeroverhead voor het beheer van de onderliggende infrastructuur <br>Snelle inrichting en serviceschalen <br>Geautomatiseerde patching en versie-upgrade <br>Integratie met andere PaaS-gegevensdiensten |99,99% uptime SLA  <br>Ingebouwde [hoge beschikbaarheid](sql-database-high-availability.md) <br>Gegevens beschermd met [geautomatiseerde back-ups](sql-database-automated-backups.md) <br>Klant configureerbare back-upbewaarperiode <br>Door de gebruiker gestarte [back-ups](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[Mogelijkheid voor het herstellen van gegevens](sql-database-recovery-using-backups.md#point-in-time-restore) in de tijd |
|**Beveiliging en naleving** | **Beheer**|
|Geïsoleerde omgeving ([VNet-integratie](sql-database-managed-instance-connectivity-architecture.md), single tenant service, dedicated compute en storage) <br>[Transparante gegevensversleuteling (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Azure AD-verificatie](sql-database-aad-authentication.md), ondersteuning voor één aanmelding <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Azure AD-serverprincipals (aanmeldingen)</a>  <br>Voldoet aan nalevingsnormen die hetzelfde zijn als azure SQL-database <br>[Controleren voor SQL](sql-database-managed-instance-auditing.md) <br>[Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) |Azure Resource Manager API voor het automatiseren van servicevoorziening en -schaling <br>Azure-portalfunctionaliteit voor handmatige servicevoorziening en -schaling <br>Data Migration Service

> [!IMPORTANT]
> Azure SQL Database (alle implementatieopties) is gecertificeerd volgens een aantal nalevingsstandaarden. Zie het Microsoft [Azure Trust Center,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) waar u de meest recente lijst met SQL Database-nalevingscertificeringen vinden voor meer informatie.

De belangrijkste kenmerken van beheerde instanties worden weergegeven in de volgende tabel:

|Functie | Beschrijving|
|---|---|
| SQL Server-versie / build | SQL Server Database Engine (nieuwste stal) |
| Beheerde geautomatiseerde back-ups | Ja |
| Ingebouwde instantie- en databasebewaking en -statistieken | Ja |
| Automatisch patchen van software | Ja |
| De nieuwste Database Engine-functies | Ja |
| Aantal gegevensbestanden (RIJEN) per database | Meerdere |
| Aantal logbestanden (LOG) per database | 1 |
| VNet - Azure Resource Manager-implementatie | Ja |
| VNet - Klassiek implementatiemodel | Nee |
| Portal-ondersteuning | Ja|
| Ingebouwde integratieservice (SSIS) | Nee - SSIS is een onderdeel van [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Ingebouwde analyseservice (SSAS) | Nee - SSAS is aparte [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Ingebouwde rapportageservice (SSRS) | Nee : gebruik in plaats daarvan [Power BI-paginarapporten](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) of host SSRS op Azure VM. Hoewel Managed Instance ssrs als service niet kan uitvoeren, kan het SSRS 2019-catalogusdatabases hosten voor een externe rapportageserver met SQL Server-verificatie. |
|||

## <a name="vcore-based-purchasing-model"></a>Aankoopmodel op basis van vCore

Het [op vCore gebaseerde inkoopmodel](sql-database-service-tiers-vcore.md) voor beheerde exemplaren biedt u flexibiliteit, controle, transparantie en een eenvoudige manier om on-premises workloadvereisten naar de cloud te vertalen. Met dit model u de gegevens, het geheugen en de opslag wijzigen op basis van uw werkbelastingbehoeften. Het vCore-model komt ook in aanmerking voor besparingen tot 55 procent met het [Azure Hybrid Benefit voor SQL Server.](https://azure.microsoft.com/pricing/hybrid-benefit/)

In het vCore-model u kiezen tussen generaties hardware.

- **Gen4 Gen4** Logische CPU's zijn gebaseerd op Intel E5-2673 v3 (Haswell) 2,4-GHz processors, aangesloten SSD, fysieke cores, 7-GB RAM per core en rekenformaten tussen 8 en 24 vCores.
- **Gen5 Gen5** Logische CPU's zijn gebaseerd op Intel E5-2673 v4 (Broadwell) 2,3-GHz en Intel SP-8160 (Skylake) processors, snelle NVMe SSD, hyper-threaded logische core en compute maten tussen 4 en 80 cores.

Meer informatie over het verschil tussen hardwaregeneraties in [beheerde instantieresourcelimieten](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Servicelagen voor beheerde exemplaren

Beheerde instantie is beschikbaar in twee servicelagen:

- **Algemeen doel**: Ontworpen voor toepassingen met typische prestatie- en IO-latentievereisten.
- **Bedrijfskritisch:** ontworpen voor toepassingen met lage IO-latentievereisten en minimale impact van onderliggende onderhoudsbewerkingen op de werkbelasting.

Beide servicelagen garanderen 99,99% beschikbaarheid en stellen u in staat om onafhankelijk van elkaar de opslaggrootte en rekencapaciteit te selecteren. Zie [Hoge beschikbaarheid en Azure SQL Database](sql-database-high-availability.md)voor meer informatie over de architectuur met hoge beschikbaarheid van Azure SQL Database.

### <a name="general-purpose-service-tier"></a>Servicelaag voor algemeen gebruik

In de volgende lijst wordt het belangrijkste kenmerk van de servicelaag voor algemeen gebruik beschreven:

- Ontwerp voor de meeste bedrijfstoepassingen met typische prestatievereisten
- Krachtige Azure Blob-opslag (8 TB)
- Ingebouwde hoge beschikbaarheid op basis van betrouwbare Azure [Blob-opslag](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) en [Azure Service Fabric](../service-fabric/service-fabric-overview.md)

Zie [opslaglaag in best practices](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) en [overwegingen voor beheerde instanties (algemeen doel) voor](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/)meer informatie.

Meer informatie over het verschil tussen servicelagen in [beheerde instantieresourcelimieten](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Bedrijfskritieke servicelaag

Business Critical service tier is gebouwd voor toepassingen met hoge IO-vereisten. Het biedt de hoogste veerkracht voor storingen met behulp van verschillende geïsoleerde replica's.

In de volgende lijst worden de belangrijkste kenmerken van de servicelaag Bedrijfskritieke service beschreven:

- Ontworpen voor zakelijke toepassingen met de hoogste prestaties en HA-vereisten
- Wordt geleverd met supersnelle lokale SSD-opslag (tot 1 TB op Gen4 en tot 4 TB op Gen5)
- Ingebouwde [hoge beschikbaarheid](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) op basis [van Always On Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) en Azure Service [Fabric.](../service-fabric/service-fabric-overview.md)
- Ingebouwde extra [alleen-lezen databasereplica](sql-database-read-scale-out.md) die kan worden gebruikt voor rapportage en andere alleen-lezen workloads
- [In-Memory OLTP](sql-database-in-memory.md) die kan worden gebruikt voor workload met hoge prestatie-eisen  

Meer informatie over het verschil tussen servicelagen in [beheerde instantieresourcelimieten](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).


## <a name="managed-instance-management-operations"></a>Beheerde beheerbewerkingen voor instance

Azure SQL Database biedt beheerbewerkingen die u kunt gebruiken om automatisch nieuwe beheerde exemplaren te implementeren, eigenschappen van exemplaren bij te werken en exemplaren te verwijderen als ze niet meer nodig zijn. In deze sectie vindt u informatie over beheeractiviteiten en de typische duur ervan.

Om [implementaties binnen Azure Virtual Networks (VNets)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) te ondersteunen en isolatie en beveiliging te bieden aan klanten, is de beheerde instantie afhankelijk van [virtuele clusters](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture), die een speciale set geïsoleerde virtuele machines vertegenwoordigen die zijn geïmplementeerd in het virtuele netwerksubnet van de klant. In wezen resulteert elke beheerde instantie-implementatie in een leeg subnet in een nieuwe virtuele clusterbuildout.

Latere bewerkingen op geïmplementeerde beheerde exemplaren kunnen ook gevolgen hebben voor het onderliggende virtuele cluster. Dit is van invloed op de duur van beheerbewerkingen, omdat het implementeren van extra virtuele machines een overhead met zich meebrengt die moet worden overwogen wanneer u nieuwe implementaties of updates plant voor bestaande beheerde exemplaren.

Alle beheerbewerkingen kunnen als volgt worden gecategoriseerd:

- Instantieimplementatie (nieuwe instantiecreatie). 
- Instantie-update (instantie-eigenschappen wijzigen, zoals vCores of gereserveerde opslag.
- Instantie verwijderen.

Doorgaans duren bewerkingen op virtuele clusters het langst. Duur van de bewerkingen op virtuele clusters variëren - hieronder zijn de waarden die u normaal gesproken verwachten, op basis van bestaande service telemetriegegevens:

- Virtuele clustercreatie. Dit is een synchrone stap in instantiebeheerbewerkingen. **90% van de operaties zijn in 4 uur voltooid.**
- Grootte van virtuele cluster (uitbreiding of krimpen). Uitbreiding is een synchrone stap, terwijl krimpen asynchroon wordt uitgevoerd (zonder invloed op de duur van instantiebeheerbewerkingen). **90% van de clusteruitbreidingen eindigen in minder dan 2,5 uur**.
- Verwijdering van virtuele cluster. Verwijdering is een asynchrone stap, maar kan ook handmatig worden [gestart](sql-database-managed-instance-delete-virtual-cluster.md) op een leeg virtueel cluster, in welk geval het synchroon wordt uitgevoerd. **90% van de virtuele clusterverwijderingen is voltooid in 1,5 uur.**

Bovendien kan het beheer van instanties ook een van de bewerkingen in gehoste databases omvatten, wat resulteert in langere duur:

- Databasebestanden toevoegen vanuit Azure Storage. Dit is een synchrone stap, zoals compute (vCore) of opslag die wordt opgeschaald of omlaag in de servicelaag Algemeen doel. **90% van deze bewerkingen zijn in 5 minuten voltooid.**
- Always On beschikbaarheid groep zaaien. Dit is een synchrone stap, zoals compute (vCore) of opslagschaling in de servicelaag Bedrijfskritieke en bij het wijzigen van de servicelaag van Algemeen doel naar Bedrijfskritisch (of vice versa). De duur van deze bewerking is evenredig aan de totale databasegrootte en de huidige databaseactiviteit (aantal actieve transacties). Databaseactiviteit bij het bijwerken van een instantie kan leiden tot aanzienlijke variantie van de totale duur. **90% van deze bewerkingen worden uitgevoerd op 220 GB / uur of hoger**.

In de volgende tabel worden bewerkingen en typische algemene duur samengevat:

|Categorie  |Bewerking  |Langlopend segment  |Geschatte duur  |
|---------|---------|---------|---------|
|**Implementatie** |Eerste instantie in een leeg subnet|Virtuele clustercreatie|90% van de operaties is voltooid in 4 uur|
|Implementatie |Eerste instantie van een andere hardwaregeneratie in een niet-leeg subnet (bijvoorbeeld eerste exemplaar gen 5 in een subnet met Gen 4-exemplaren)|Virtuele clustercreatie*|90% van de operaties is voltooid in 4 uur|
|Implementatie |Eerste instantie creatie van 4 vCores, in een leeg of niet-leeg subnet|Virtueel cluster maken**|90% van de operaties is voltooid in 4 uur|
|Implementatie |Volgende instantie creatie binnen het niet-lege subnet (2e, 3e, etc. instantie)|Grootte van virtuele clusterwijzigen|90% van de operaties is in 2,5 uur klaar|
|**Update** |Wijziging van de instantieeigenschap (beheerderswachtwoord, AAD-login, Azure Hybrid Benefit-vlag)|N.v.t.|Maximaal 1 minuut|
|Update |Op-/neerschalen van instantieopslag (servicelaag voor algemeen gebruik)|Databasebestanden koppelen|90% van de bewerkingen zijn voltooid in 5 minuten|
|Update |Op-/neerschalen van instantieopslag (Bedrijfskritieke servicelaag)|- Formaat van virtuele cluster<br>- Always On beschikbaarheid groep zaaien|90% van de bewerkingen eindigen in 2,5 uur + tijd om alle databases te zaaien (220 GB / uur)|
|Update |Instance compute (vCores) op en neer schalen (Algemeen doel)|- Formaat van virtuele cluster<br>- Databasebestanden bijvoegen|90% van de operaties is in 2,5 uur klaar|
|Update |Instance compute (vCores) op en neer schalen (Business Critical)|- Formaat van virtuele cluster<br>- Always On beschikbaarheid groep zaaien|90% van de bewerkingen eindigen in 2,5 uur + tijd om alle databases te zaaien (220 GB / uur)|
|Update |Instantie schaal naar 4 vCores (Algemeen doel)|- Het formaat van virtuele clusteren wijzigen (als dit voor de eerste keer wordt gedaan, moet mogelijk virtuele clustercreatie**)<br>- Databasebestanden bijvoegen|90% van de operaties eindigen in 4 uur 5 min**|
|Update |Instantie schalen naar 4 vCores (Bedrijfskritiek)|- Het formaat van virtuele clusteren wijzigen (als dit voor de eerste keer wordt gedaan, moet mogelijk virtuele clustercreatie**)<br>- Always On beschikbaarheid groep zaaien|90% van de bewerkingen eindigen in 4 uur + tijd om alle databases te zaaien (220 GB / uur)|
|Update |Wijziging van de instantieservicelaag (algemeen doel naar bedrijfskritisch en vice versa)|- Formaat van virtuele cluster<br>- Always On beschikbaarheid groep zaaien|90% van de bewerkingen eindigen in 2,5 uur + tijd om alle databases te zaaien (220 GB / uur)|
|**Verwijdering**|Verwijdering van exemplaar|Logboekstaartback-up voor alle databases|90% operaties eindigen in maximaal 1 minuut.<br>Opmerking: als laatste instantie in het subnet wordt verwijderd, wordt deze bewerking gepland voor het verwijderen van virtuele clusterna 12 uur***|
|Verwijdering|Verwijdering van virtuele cluster (als door de gebruiker geïnitieerde bewerking)|Verwijdering van virtuele cluster|90% van de bewerkingen wordt voltooid in maximaal 1,5 uur|

\*Virtueel cluster wordt gebouwd per hardwaregeneratie.

\*\*De implementatieoptie 4 vCores is uitgebracht in juni 2019 en vereist een nieuwe virtuele clusterversie. Als u exemplaren in het doelsubnet had die allemaal vóór 12 juni zijn gemaakt, wordt een nieuw virtueel cluster automatisch geïmplementeerd om 4 vCore-exemplaren te hosten.

\*\*\*12 uur is de huidige configuratie, maar dat kan veranderen in de toekomst, dus neem geen harde afhankelijkheid van. Als u een virtueel cluster eerder moet verwijderen (om bijvoorbeeld het subnet vrij te geven), raadpleegt u [Een subnet verwijderen nadat u een beheerde instantie van Azure SQL Database hebt verwijderd.](sql-database-managed-instance-delete-virtual-cluster.md)

### <a name="instance-availability-during-management"></a>Beschikbaarheid van instance's tijdens het beheer

Beheerde exemplaren zijn niet beschikbaar voor clienttoepassingen tijdens implementatie- en verwijderingsbewerkingen.

Beheerde exemplaren zijn beschikbaar tijdens updatebewerkingen, maar er is een korte downtime veroorzaakt door de failover die plaatsvindt aan het einde van updates die doorgaans tot 10 seconden duren. De uitzondering hierop is het bijwerken van de gereserveerde opslagruimte in de servicelaag voor algemeen gebruik, die geen failover ondergaat of de beschikbaarheid van bijvoorbeelden beïnvloedt.

> [!IMPORTANT]
> De duur van een failover kan aanzienlijk variëren in het geval van langlopende transacties die plaatsvinden in de databases als gevolg van [langdurige hersteltijd](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process). Daarom wordt het niet aanbevolen om compute of storage van Azure SQL Database managed instance te schalen of om de servicelaag tegelijkertijd te wijzigen met de langlopende transacties (gegevensimport, gegevensverwerkingstaken, indexreconstructie, enz.). Database failover die zal worden uitgevoerd aan het einde van de bewerking zal annuleren lopende transacties en resulteren in langdurige hersteltijd.

> [!TIP]
> Het bijwerken van de gereserveerde opslagruimte in de servicelaag voor algemeen gebruik heeft geen failover of geen invloed op de beschikbaarheid van bijvoorbeelden.

[Versneld databaseherstel](sql-database-accelerated-database-recovery.md) is momenteel niet beschikbaar voor azure SQL Database-beheerde exemplaren. Eenmaal ingeschakeld, zal deze functie de variabiliteit van failovertijd aanzienlijk verminderen, zelfs in het geval van langlopende transacties.

### <a name="canceling-management-operations"></a>Beheerbewerkingen annuleren

In de volgende tabel wordt een overzicht van de mogelijkheid om specifieke beheerbewerkingen en typische algemene duur te annuleren:

Categorie  |Bewerking  |Annuleren kan  |Geschatte annuleringsduur  |
|---------|---------|---------|---------|
|Implementatie |Instantie maken |Nee |  |
|Update |Opschalen/omlaagschalen van instantieopslag (algemeen doel) |Nee |  |
|Update |Opschalen/omlaag schalen van instantieopslag (Bedrijfskritiek) |Ja |90% van de bewerkingen zijn voltooid in 5 minuten |
|Update |Instance compute (vCores) op en neer schalen (Algemeen doel) |Ja |90% van de bewerkingen zijn voltooid in 5 minuten |
|Update |Instance compute (vCores) op en neer schalen (Business Critical) |Ja |90% van de bewerkingen zijn voltooid in 5 minuten |
|Update |Wijziging van de instantieservicelaag (algemeen doel naar bedrijfskritisch en vice versa) |Ja |90% van de bewerkingen zijn voltooid in 5 minuten |
|Verwijderen |Verwijdering van exemplaar |Nee |  |
|Verwijderen |Verwijdering van virtuele cluster (als door de gebruiker geïnitieerde bewerking) |Nee |  |

Om de beheerbewerking te annuleren, gaat u naar het overzichtsblad en klikt u op het meldingsvak van de lopende bewerking. Vanaf de rechterkant verschijnt een scherm met doorlopende werking en is er een knop voor het annuleren van de werking. Na de eerste klik wordt u gevraagd opnieuw te klikken en te bevestigen dat u de bewerking wilt annuleren.

[![](./media/sql-database-managed-instance/canceling-operation.png)](./media/sql-database-managed-instance/canceling-operation.png#lightbox)

Nadat de aanvraag voor annuleren is ingediend en verwerkt, ontvangt u een melding als de indiening annuleren is geslaagd of niet. 

In geval van annulering van succes, zal het beheer operatie worden geannuleerd in een paar minuten resulteert in een storing.

![resultaat van de geannuleerde bewerking annuleren](./media/sql-database-managed-instance/canceling-operation-result.png)

Als de knop Annuleren mislukt of annuleren niet actief is, betekent dit dat de beheerbewerking niet geannuleerde status heeft ingevoerd en dat deze binnen enkele minuten is voltooid. De beheerbewerking wordt voortgezet totdat deze is voltooid.

> [!IMPORTANT]
> De annuleringsbewerking wordt momenteel alleen ondersteund in Portal.

## <a name="advanced-security-and-compliance"></a>Geavanceerde beveiliging en naleving van voorschriften

De beheerde optie voor het implementeren van instance combineert geavanceerde beveiligingsfuncties van Azure cloud en SQL Server Database Engine.

### <a name="managed-instance-security-isolation"></a>Beheerde beveiligingisolatie van bijvoorbeeld

Een beheerde instantie biedt extra beveiligingsisolatie van andere tenants in de Azure-cloud. Beveiliging isolatie omvat:

- [Native virtuele netwerkimplementatie](sql-database-managed-instance-connectivity-architecture.md) en connectiviteit met uw on-premises omgeving met Azure Express Route of VPN Gateway.
- In een standaardimplementatie wordt SQL-eindpunt alleen weergegeven via een privé-IP-adres, waardoor veilige connectiviteit mogelijk is van particuliere Azure- of hybridenetwerken.
- Single-tenant met specifieke onderliggende infrastructuur (compute, storage).

In het volgende diagram worden verschillende connectiviteitsopties voor uw toepassingen beschreven:

![hoge beschikbaarheid](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Zie [VNet-architectuur voor beheerde instanties](sql-database-managed-instance-connectivity-architecture.md) en Sluit uw toepassing aan op [een beheerde instantie voor](sql-database-managed-instance-connect-app.md)meer informatie over vNet-integratie- en netwerkbeleidshandhaving op subnetniveau.

> [!IMPORTANT]
> Plaats meerdere beheerde exemplaren in hetzelfde subnet, waar dat is toegestaan door uw beveiligingsvereisten, want dat brengt u extra voordelen. Het samenvoegen van instanties in hetzelfde subnet zal het onderhoud van netwerkinfrastructuur aanzienlijk vereenvoudigen en de inprovisioningstijd van instanties verkorten, aangezien de lange inrichtingsduur is gekoppeld aan de kosten van het implementeren van de eerste beheerde instantie in een subnet.

### <a name="azure-sql-database-security-features"></a>Beveiligingsfuncties azure SQL-database

Azure SQL Database biedt een reeks geavanceerde beveiligingsfuncties die kunnen worden gebruikt om uw gegevens te beschermen.

- [Beheerde instantiecontrole](sql-database-managed-instance-auditing.md) houdt databasegebeurtenissen bij en schrijft deze naar een controlelogboekbestand dat in uw Azure-opslagaccount is geplaatst. Auditing kan helpen bij het handhaven van de naleving van de regelgeving, inzicht krijgen in databaseactiviteiten en inzicht krijgen in discrepanties en afwijkingen die kunnen duiden op zakelijke problemen of vermoedelijke beveiligingsschendingen.
- Gegevensversleuteling in beweging - een beheerde instantie beveiligt uw gegevens door versleuteling te bieden voor gegevens in beweging met Transport Layer Security. Naast de beveiliging van transportlagen biedt de beheerde instantie-implementatieoptie bescherming van gevoelige gegevens tijdens de vlucht, in rust en tijdens queryverwerking met [Always Encrypted.](/sql/relational-databases/security/encryption/always-encrypted-database-engine) Het unieke Always Encrypted biedt ongeëvenaarde beveiliging tegen diefstal van kritieke gegevens. Bijvoorbeeld, met Always Encrypted, credit card nummers worden versleuteld opgeslagen in de database altijd, zelfs tijdens query verwerking, waardoor decryptie op het punt van gebruik door geautoriseerde medewerkers of toepassingen die nodig hebben om die gegevens te verwerken.
- [Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) vormt een aanvulling op [auditing](sql-database-managed-instance-auditing.md) door een extra laag beveiligingsinformatie te bieden die in de service is ingebouwd en die ongebruikelijke en mogelijk schadelijke pogingen detecteert om toegang te krijgen tot databases of deze te exploiteren. U wordt gewaarschuwd over verdachte activiteiten, potentiële kwetsbaarheden en SQL-injectieaanvallen, evenals afwijkende databasetoegangspatronen. Advanced Threat Protection alerts kunnen worden bekeken vanuit [Azure Security Center](https://azure.microsoft.com/services/security-center/) en details van verdachte activiteiten geven en actie aanbevelen voor het onderzoeken en beperken van de dreiging.  
- [Dynamische gegevensmaskering](/sql/relational-databases/security/dynamic-data-masking) beperkt de blootstelling aan gevoelige gegevens door deze te maskeren aan niet-bevoorrechte gebruikers. Dynamische gegevensmaskering helpt ongeautoriseerde toegang tot gevoelige gegevens te voorkomen door u in staat te stellen aan te geven hoeveel van de gevoelige gegevens moeten worden weergegeven met minimale impact op de toepassingslaag. Dit is een beveiligingsfunctie op basis van beleid. De gevoelige gegevens in de resultatenset van een query die is uitgevoerd op toegewezen databasevelden worden verborgen, terwijl de gegevens in de database niet worden gewijzigd.
- [Met beveiliging op rijniveau](/sql/relational-databases/security/row-level-security) u de toegang tot rijen in een databasetabel beheren op basis van de kenmerken van de gebruiker die een query uitvoert (bijvoorbeeld op groepslidmaatschap of uitvoeringscontext). Beveiliging op rijniveau (RLS) vereenvoudigt het ontwerp en de code van de beveiliging in uw toepassing. Met RLS kunt u beperkingen instellen voor de toegang tot gegevens in rijen. Zorg er bijvoorbeeld voor dat werknemers alleen toegang hebben tot de gegevensrijen die relevant zijn voor hun afdeling, of een gegevenstoegang beperken tot alleen de relevante gegevens.
- [Transparent data encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) versleutelt beheerde instantie-gegevensbestanden, bekend als het versleutelen van gegevens in rust. TDE voert real-time I/O encryptie en decryptie van de gegevens en log bestanden. De versleuteling maakt gebruik van een databaseencryptiesleutel (DEK), die wordt opgeslagen in de databasebootrecord voor beschikbaarheid tijdens het herstel. U al uw databases in een beheerde instantie beveiligen met transparante gegevensversleuteling. TDE is sql server's bewezen encryptie-at-rest-technologie die vereist is door vele nalevingsnormen om te beschermen tegen diefstal van opslagmedia.

Migratie van een versleutelde database naar een beheerde instantie wordt ondersteund via de Azure Database Migration Service (DMS) of native restore. Als u van plan bent een versleutelde database te migreren met native restore, is migratie van het bestaande TDE-certificaat van de on-premises SQL Server of SQL Server in een virtuele machine naar een beheerde instantie een vereiste stap. Zie [SQL Server-instantiemigratie naar beheerde instantie voor](sql-database-managed-instance-migrate.md)meer informatie over migratieopties.

## <a name="azure-active-directory-integration"></a>Azure Active Directory-integratie

De beheerde optie voor het implementeren van instance ondersteunt traditionele aanmeldingen en aanmeldingen van SQL-servers Database engine die zijn geïntegreerd met Azure Active Directory (AAD). Azure AD-serverprincipals (aanmeldingen)**(openbare preview)** zijn Azure-cloudversie van on-premises databaseaanmeldingen die u gebruikt in uw on-premises omgeving. Met Azure AD-serverprincipals (aanmeldingen) u gebruikers en groepen van uw Azure Active Directory-tenant opgeven als true instance-scoped principals, die in staat zijn om elke bewerking op instantieniveau uit te voeren, inclusief cross-databasequery's binnen hetzelfde beheerde exemplaar.

Er wordt een nieuwe syntaxis geïntroduceerd om Azure AD-serverprincipals (aanmeldingen) **te maken, VAN EXTERNE PROVIDER**. Zie <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">AANMELDING MAKEN</a>en raadpleeg de [Azure Active Directory-beheerder inrichten voor uw beheerde instantieartikel](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) voor meer informatie over de syntaxis.

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Azure Active Directory-integratie en meervoudige verificatie

Met de optie beheerde instantieimplementatie u de identiteit van databasegebruikers en andere Microsoft-services centraal beheren met [Azure Active Directory-integratie.](sql-database-aad-authentication.md) Deze mogelijkheid vereenvoudigt het beheer van machtigingen en verbetert de beveiliging. Azure Active Directory ondersteunt [Multi-Factor Authentication](sql-database-ssms-mfa-authentication-configure.md) (MFA) voor betere beveiliging van gegevens en toepassingen, en ondersteunt ook een proces voor eenmalige aanmelding (SSO).

### <a name="authentication"></a>Verificatie

Beheerde instantieverificatie verwijst naar hoe gebruikers hun identiteit bewijzen wanneer ze verbinding maken met de database. SQL Database ondersteunt twee typen verificatie:  

- **SQL-verificatie:**

  Deze verificatiemethode gebruikt een gebruikersnaam en wachtwoord.
- **Azure Active Directory-verificatie:**

  Deze verificatiemethode maakt gebruik van identiteiten die worden beheerd door Azure Active Directory en wordt ondersteund voor beheerde en geïntegreerde domeinen. Gebruik active directory-verificatie (geïntegreerde beveiliging) [waar mogelijk.](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode)

### <a name="authorization"></a>Autorisatie

Autorisatie verwijst naar wat een gebruiker kan doen binnen een Azure SQL-database en wordt beheerd door de databaserollidmaatschappen van uw gebruikersaccount en machtigingen op objectniveau. Een beheerde instantie heeft dezelfde autorisatiemogelijkheden als SQL Server 2017.

## <a name="database-migration"></a>Databasemigratie

De optie beheerde instantieimplementatie is gericht op gebruikersscenario's met massale databasemigratie van on-premises of IaaS-database-implementaties. Beheerde instantie ondersteunt verschillende opties voor databasemigratie:

### <a name="back-up-and-restore"></a>Back-up en herstel  

De migratiebenadering maakt gebruik van SQL-back-ups naar Azure Blob-opslag. Back-ups die zijn opgeslagen in Azure-opslagblob, kunnen rechtstreeks worden hersteld naar een beheerde instantie met de [opdracht T-SQL RESTORE.](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current)

- Zie [Een back-upbestand herstellen naar een beheerde instantie voor](sql-database-managed-instance-get-started-restore.md)een snelle start met het herstellen van het wide world-bestand voor importeurs in de hele wereld - Standaarddatabaseback-up. Deze quickstart laat zien dat u een back-upbestand moet uploaden naar Azure blob-opslag en deze moet beveiligen met een SAS-sleutel (Shared Access Signature).
- Zie Native RESTORE van [URL](sql-database-managed-instance-migrate.md#native-restore-from-url)voor informatie over herstellen vanuit URL .

> [!IMPORTANT]
> Back-ups van een beheerde instantie kunnen alleen worden hersteld naar een andere beheerde instantie. Ze kunnen niet worden hersteld naar een on-premises SQL Server of naar één database/elastische groep.

### <a name="data-migration-service"></a>Data Migration Service

De Azure Database Migration Service is een volledig beheerde service die is ontworpen om naadloze migraties van meerdere databasebronnen naar Azure Data-platforms mogelijk te maken met minimale downtime. Deze service stroomlijnt de taken die nodig zijn om bestaande externe en SQL Server-databases naar Azure SQL Database (afzonderlijke databases, samengevoegde databases in elastische groepen en instantiedatabases in een beheerde instantie) en SQL Server in Azure VM te verplaatsen. Zie [Hoe u uw on-premises database migreert naar beheerde instantie met DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>SQL-functies ondersteund

De optie beheerde instantieimplementatie is bedoeld om bijna 100% surface area-compatibiliteit te bieden met on-premises SQL Server die gefaseerd beschikbaar komt totdat de service algemeen beschikbaar is. Zie [SQL Database-functievergelijking](sql-database-features.md)voor een lijst met functies en vergelijking met de vergelijking van sql-database en voor een lijst met T-SQL-verschillen in beheerde instanties versus SQL Server, zie [beheerde instantie T-SQL-verschillen van SQL Server](sql-database-managed-instance-transact-sql-information.md).

De beheerde optie voor het implementeren van instance ondersteunt achterwaartse compatibiliteit met SQL 2008-databases. Directe migratie van SQL 2005-databaseservers wordt ondersteund, compatibiliteitsniveau voor gemigreerde SQL 2005-databases wordt bijgewerkt naar SQL 2008.
  
In het volgende diagram wordt de compatibiliteit van het oppervlak in beheerde instantie beschreven:  

![Migratie](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Belangrijkste verschillen tussen sql server on-premises en in een beheerde instantie

De beheerde optie voor het implementeren van bijvoorbeeld wordt gegarandeerd, omdat deze altijd up-to-date is in de cloud, wat betekent dat sommige functies in on-premises SQL Server verouderd, verouderd of alternatieven kunnen hebben. Er zijn specifieke gevallen waarin hulpprogramma's moeten erkennen dat een bepaalde functie op een iets andere manier werkt of dat de service wordt uitgevoerd in een omgeving die u niet volledig beheert:

- Hoge beschikbaarheid is ingebouwd en vooraf geconfigureerd met technologie die vergelijkbaar is met [Always On Availability Groups.](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
- Geautomatiseerde back-ups en punt in tijd te herstellen. De klant `copy-only` kan back-ups starten die de automatische back-upketen niet verstoren.
- Beheerde instantie staat het opgeven van volledige fysieke paden niet toe, dus alle bijbehorende scenario's moeten anders worden ondersteund: RESTORE DB biedt geen ondersteuning voor MOVE, CREATE DB staat geen fysieke paden toe, BULK INSERT werkt alleen met Azure Blobs, enz.
- Beheerde instantie ondersteunt [Azure AD-verificatie](sql-database-aad-authentication.md) als cloudalternatief voor Windows-verificatie.
- Beheerde instantie beheert automatisch XTP-bestandsgroep en bestanden voor databases met OlTP-objecten in geheugengeheugen
- Beheerde instantie ondersteunt SQL Server Integration Services (SSIS) en kan ssis-catalogus (SSISDB) hosten waarmee SSIS-pakketten worden opgeslagen, maar ze worden uitgevoerd op een beheerde Azure-SSIS Integration Runtime (IR) in Azure Data Factory (ADF), zie [Azure-SSIS IR maken in ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Zie Een enkele database van Azure [SQL Database, een elastische pool en beheerde instantie vergelijken](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance)om de SSIS-functies in SQL Database te vergelijken.

### <a name="managed-instance-administration-features"></a>Beheerfuncties voor beheer van beheervan beheerbeheer beheersonderdelen voor beheer

Met de optie beheerde instantieimplementatie kan systeembeheerder minder tijd besteden aan administratieve taken omdat de SQL Database-service deze voor u uitvoert of deze taken sterk vereenvoudigt. Bijvoorbeeld, [OS / RDBMS installatie en patching,](sql-database-high-availability.md) [dynamische instantie resizing en configuratie,](sql-database-single-database-scale.md) [back-ups,](sql-database-automated-backups.md) [database replicatie](replication-with-sql-database-managed-instance.md) (inclusief systeem databases), hoge beschikbaarheid [configuratie,](sql-database-high-availability.md)en configuratie van de gezondheid en [prestatiebewaking](../azure-monitor/insights/azure-sql.md) datastromen.

> [!IMPORTANT]
> Zie [SQL Database-functies](sql-database-features.md)voor een lijst met ondersteunde, gedeeltelijk ondersteunde en niet-ondersteunde functies. Zie [beheerde instantie T-SQL-verschillen van SQL Server](sql-database-managed-instance-transact-sql-information.md) voor een lijst met T-SQL-verschillen in beheerde instanties versus SQL Server

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Een beheerde instantie programmatisch identificeren

In de volgende tabel worden verschillende eigenschappen weergegeven die toegankelijk zijn via Transact SQL, die u gebruiken om te detecteren dat uw toepassing werkt met beheerde instantie en belangrijke eigenschappen op te halen.

|Eigenschap|Waarde|Opmerking|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) - 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Deze waarde is hetzelfde als in SQL Database. Dit **duidt niet** op SQL engine versie 12 (SQL Server 2014). Beheerde instantie draait altijd de nieuwste stabiele SQL-engine-versie, die gelijk is aan of hoger is dan de laatst beschikbare RTM-versie van SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Deze waarde is hetzelfde als in SQL Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Deze waarde identificeert op unieke wijze een beheerde instantie.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Dns-naam voor volledige instantie`<instanceName>`in de volgende indeling: . `<dnsPrefix>`.database.windows.net, `<instanceName>` waar is de naam `<dnsPrefix>` die door de klant, terwijl is autogenerated deel van de naam die globale DNS naam uniciteit ("wcus17662feb9ce98", bijvoorbeeld)|Voorbeeld: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Volgende stappen

- Zie [Quickstart-handleiding](sql-database-managed-instance-get-started.md)voor meer informatie over het maken van uw eerste beheerde instantie.
- Zie [ALGEMENE SQL-functies voor](sql-database-features.md)een lijst met functies en vergelijkingsfuncties .
- Zie [VNet-configuratie beheerde instantie voor](sql-database-managed-instance-connectivity-architecture.md)meer informatie over vNet-configuratie.
- Zie [een beheerde instantie maken](sql-database-managed-instance-get-started.md)voor een snellestart waarmee een beheerde instantie wordt gemaakt en een database wordt hersteld uit een back-upbestand.
- Zie [Beheerde instantiemigratie met DMS](../dms/tutorial-sql-server-to-managed-instance.md)voor een zelfstudie met de Azure Database Migration Service (DMS) voor migratie.
- Zie Azure SQL Database met [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md)controleren voor geavanceerde bewaking van beheerde instantiedatabaseprestaties met ingebouwde informatie over probleemoplossing.
- Zie [SQL Database managed instance pricing](https://azure.microsoft.com/pricing/details/sql-database/managed/)voor prijsinformatie voor prijsinformatie .
