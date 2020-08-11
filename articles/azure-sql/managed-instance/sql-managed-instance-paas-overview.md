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
ms.date: 06/25/2020
ms.openlocfilehash: 7194e4553386c25691bb3ede8096da7fb63c5dc0
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88055199"
---
# <a name="what-is-azure-sql-managed-instance"></a>Wat is Azure SQL Managed instance?
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Azure SQL Managed instance is de intelligente, schaal bare Cloud database service die de ruime SQL Server data base-engine compatibiliteit combineert met alle voor delen van een volledig beheerd en groen wordende platform als een service. SQL Managed instance heeft bijna 100% compatibel met de nieuwste data base-engine van SQL Server (Enter prise Edition), waarbij een [VNet-implementatie (native Virtual Network](../../virtual-network/virtual-networks-overview.md) ) wordt geboden waarmee veelvoorkomende beveiligings problemen worden opgelost en een [bedrijfs model](https://azure.microsoft.com/pricing/details/sql-database/) kan worden gebruikt voor bestaande SQL Server klanten. Met SQL Managed instance kunnen bestaande SQL Server klanten hun on-premises toepassingen naar de Cloud tillen en naar een andere data base verplaatsen met minimale toepassings-en database wijzigingen. Tegelijkertijd behoudt SQL Managed instance alle PaaS-mogelijkheden (automatische patches en versie-updates, [geautomatiseerde back-ups](../database/automated-backups-overview.md), [hoge Beschik baarheid](../database/high-availability-sla.md)) waarmee de overhead en TCO van het beheer drastisch worden verminderd.

> [!IMPORTANT]
> Zie [ondersteunde regio's](resource-limits.md#supported-regions)voor een lijst met REGIO'S waar SQL Managed Instance momenteel beschikbaar is.

In het volgende diagram worden de belangrijkste functies van SQL Managed instance beschreven:

![Belangrijke functies](./media/sql-managed-instance-paas-overview/key-features.png)

Azure SQL Managed instance is ontworpen voor klanten die een groot aantal apps willen migreren van een on-premises of IaaS, zelfgebouwde of ISV-geleverde omgeving naar een volledig beheerde PaaS-cloud omgeving, met zo weinig mogelijk migratie inspanningen. Met de volledig geautomatiseerde [Azure Data Migration-service](../../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance)kunnen klanten hun bestaande SQL Server exemplaar naar SQL Managed instance verplaatsen en verschuiven, wat compatibel is met SQL Server en volledige isolatie van klant instanties biedt met systeem eigen VNet-ondersteuning.  Met Software Assurance kunt u uw bestaande licenties uitwisselen voor kortings tarieven op een SQL-beheerd exemplaar met behulp [van de Azure Hybrid Benefit voor SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/). SQL Managed instance is de beste migratie bestemming in de Cloud voor SQL Server instanties waarvoor een hoge beveiliging en een uitgebreid programmeerbaar Opper vlak zijn vereist.

## <a name="key-features-and-capabilities"></a>Belangrijkste functies en mogelijkheden

SQL Managed instance combineert de beste functies die beschikbaar zijn in Azure SQL Database en de SQL Server data base-engine.

> [!IMPORTANT]
> SQL Managed instance wordt uitgevoerd met alle functies van de meest recente versie van SQL Server, waaronder online bewerkingen, Automatische plannings correcties en andere verbeteringen voor bedrijfs prestaties. Een vergelijking van de beschik bare functies wordt uitgelegd in [functie vergelijking: Azure SQL Managed instance versus SQL Server](../database/features-comparison.md).

| **PaaS-voor delen** | **Bedrijfscontinuïteit** |
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
| Ingebouwde Reporting service (SSRS) | Geen gebruik [Power bi gepagineerde rapporten](https://docs.microsoft.com/power-bi/paginated-reports/paginated-reports-report-builder-power-bi) of host SSRS op een virtuele machine van Azure. SQL Managed instance kan geen SSRS als service uitvoeren, maar kan [SSRS-catalogus databases](https://docs.microsoft.com/sql/reporting-services/install-windows/ssrs-report-server-create-a-report-server-database#database-server-version-requirements) hosten voor een rapport server die is geïnstalleerd op een virtuele Azure-machine met behulp van SQL Server-verificatie. |
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

## <a name="management-operations"></a>Beheerbewerkingen

Azure SQL Managed instance biedt beheer bewerkingen die u kunt gebruiken om automatisch nieuwe beheerde instanties te implementeren, exemplaar-eigenschappen bij te werken en instanties te verwijderen wanneer ze niet meer nodig zijn. Gedetailleerde uitleg van beheer bewerkingen vindt u op de pagina [overzicht van beheer bewerkingen voor beheerde exemplaren](management-operations-overview.md) .

## <a name="advanced-security-and-compliance"></a>Geavanceerde beveiliging en naleving van voorschriften

SQL Managed instance wordt geleverd met geavanceerde beveiligings functies die worden geleverd door het Azure-platform en de SQL Server data base-engine.

### <a name="security-isolation"></a>Beveiligingsisolatie

SQL Managed instance biedt extra beveiligings isolatie van andere tenants op het Azure-platform. Beveiligings isolatie omvat:

- [Systeem eigen virtuele netwerk implementatie](connectivity-architecture-overview.md) en connectiviteit met uw on-premises omgeving met behulp van Azure ExpressRoute of VPN gateway.
- In een standaard implementatie wordt het SQL-eind punt alleen weer gegeven via een privé-IP-adres, waardoor er veilige connectiviteit mogelijk is vanuit persoonlijke Azure-of hybride netwerken.
- Eén Tenant met toegewezen onderliggende infra structuur (compute, Storage).

Het volgende diagram geeft een overzicht van de verschillende connectiviteits opties voor uw toepassingen:

![Maximale beschikbaarheid](./media/sql-managed-instance-paas-overview/application-deployment-topologies.png)  

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

Azure Database Migration Service is een volledig beheerde service die is ontworpen voor naadloze migratie van meerdere databasebronnen naar Azure-gegevensplatforms met slechts minimale downtime. Deze service stroomlijnt de taken die nodig zijn voor het verplaatsen van bestaande data bases van derden en SQL Server naar Azure SQL Database, Azure SQL Managed instance en SQL Server op Azure VM. Zie [de migratie van uw on-premises Data Base naar een met SQL beheerd exemplaar met behulp van database Migration service](https://aka.ms/migratetoMIusingDMS).

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
