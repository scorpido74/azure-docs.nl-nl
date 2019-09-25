---
title: Azure-blauwdruk voor beveiliging en naleving-data warehouse voor NIST SP 800-171
description: Azure-blauwdruk voor beveiliging en naleving-data warehouse voor NIST SP 800-171
services: security
author: jomolesk
ms.assetid: dbc9cafe-115d-4965-b0d4-fcf235a064c8
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: d5c7ab3cc8d4de788da5d18f31c6aacb31b5d551
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259886"
---
# <a name="azure-security-and-compliance-blueprint---data-warehouse-for-nist-sp-800-171"></a>Azure-blauwdruk voor beveiliging en naleving-data warehouse voor NIST SP 800-171

## <a name="overview"></a>Overzicht
De [800-171 speciale](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) cui van het NIST bevat richt lijnen voor het beveiligen van de beheerde niet-geclassificeerde informatie, die zich in niet-federale informatie systemen en organisaties bevindt. NIST SP 800-171 brengt 14 beveiligings vereisten tot stand voor het beschermen van de vertrouwelijkheid van CUI.

Deze Azure-blauwdruk voor beveiliging en naleving biedt richt lijnen om klanten te helpen bij het implementeren van een Data Warehouse-architectuur in azure, waarmee een subset van de Controls van het NIST-besturings systeem 800-171 wordt geïmplementeerd. Deze oplossing laat zien hoe klanten kunnen voldoen aan specifieke vereisten voor beveiliging en naleving. Het fungeert ook als basis voor klanten om hun eigen data warehouse-oplossingen te bouwen en te configureren in Azure.

Deze referentie architectuur, de bijbehorende implementatie handleiding en het bedreigings model zijn bedoeld om te fungeren als basis voor klanten om aan hun specifieke behoeften aan te passen. Ze mogen niet worden gebruikt als-in een productie omgeving. Klanten zijn verantwoordelijk voor het uitvoeren van passende beveiligings-en nalevings beoordelingen van alle oplossingen die zijn gebouwd met behulp van deze architectuur. De vereisten kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

## <a name="architecture-diagram-and-components"></a>Architectuur diagram en onderdelen
Deze oplossing biedt een referentie architectuur waarmee een hoogwaardige en veilige Cloud-Data Warehouse wordt geïmplementeerd. Er zijn twee afzonderlijke gegevens lagen in deze architectuur. De ene laag is waar gegevens worden geïmporteerd, opgeslagen en klaargezet in een geclusterde SQL-omgeving. Een andere laag is voor SQL Data Warehouse. Met deze laag worden de gegevens geladen met behulp van een ETL-hulp programma ((bijvoorbeeld [poly base](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase) T-SQL-query's) voor verwerking. Nadat de gegevens zijn opgeslagen in SQL Data Warehouse, kan Analytics op grote schaal worden uitgevoerd.

Azure biedt een aantal rapportage-en analyse Services voor de klant. Deze oplossing bevat SQL Server Reporting Services voor het snel maken van rapporten uit het SQL Data Warehouse. Alle SQL-verkeer wordt versleuteld met SSL via het opnemen van zelfondertekende certificaten. Als best practice wordt u aangeraden een vertrouwde certificerings instantie te gebruiken voor verbeterde beveiliging.

Azure SQL Data Warehouse worden gegevens opgeslagen in relationele tabellen met kolom opslag. Met deze indeling worden de kosten voor gegevens opslag aanzienlijk verminderd terwijl de query prestaties worden verbeterd. Afhankelijk van de gebruiks vereisten kunnen SQL Data Warehouse Compute-resources omhoog of omlaag worden geschaald of volledig worden afgesloten als er voor geen actieve processen reken resources zijn vereist.

Een SQL Server load balancer het SQL-verkeer beheert om hoge prestaties te garanderen. Alle virtuele machines (Vm's) in deze referentie architectuur implementeren in een beschikbaarheidsset met SQL Server exemplaren die in een always on-beschikbaarheids groep zijn geconfigureerd. Deze configuratie biedt mogelijkheden voor hoge Beschik baarheid en herstel na een nood geval.

Deze referentie architectuur voor data warehouses bevat ook een Active Directory-laag voor het beheer van resources binnen de architectuur. Met het Active Directory subnet kunt u eenvoudig een grotere Active Directory forest-structuur maken. Op deze manier kan de omgeving continu werken, zelfs wanneer toegang tot het grotere forest niet beschikbaar is. Alle Vm's zijn gekoppeld aan de laag Active Directory. Ze gebruiken Active Directory groeps beleid voor het afdwingen van beveiligings-en nalevings configuraties op het niveau van het besturings systeem.

De oplossing maakt gebruik van Azure Storage accounts, die klanten kunnen configureren om Storage Service Encryption te gebruiken om de vertrouwelijkheid van gegevens in rust te houden. In Azure worden drie kopieën van gegevens opgeslagen in het geselecteerde Data Center van de klant voor tolerantie. Geografisch redundante opslag zorgt ervoor dat gegevens worden gerepliceerd naar een secundair Data Center honderden kilo meters en worden opgeslagen als drie kopieën binnen dat Data Center. Deze regeling voor komt dat een nadelige gebeurtenis op het primaire Data Center van de klant leidt tot verlies van gegevens.

Voor een betere beveiliging worden alle resources in deze oplossing als resource groep beheerd via Azure Resource Manager. Op rollen gebaseerd toegangs beheer (RBAC) van Azure Active Directory (Azure AD) wordt gebruikt om de toegang tot geïmplementeerde resources te beheren. Deze resources omvatten klant sleutels in Azure Key Vault. Systeem status wordt bewaakt via Azure Security Center en Azure Monitor. Klanten configureren beide bewakings Services voor het vastleggen van Logboeken. De systeem status wordt weer gegeven in één dash board dat eenvoudig te gebruiken is.

Een VM fungeert als een beheer bastion-host. Het biedt een beveiligde verbinding voor beheerders om toegang te krijgen tot geïmplementeerde bronnen. De gegevens worden in het faserings gebied geladen via deze beheer bastion-host. *U wordt aangeraden een VPN-of Azure ExpressRoute-verbinding te configureren voor beheer en gegevens import in het subnet met de referentie architectuur.*

![Diagram van data warehouse voor 800-171 NIST-referentie architectuur](images/nist171-dw-architecture.png "Diagram van data warehouse voor 800-171 NIST-referentie architectuur")

Deze oplossing maakt gebruik van de volgende Azure-Services. Zie de sectie [implementatie architectuur](#deployment-architecture) voor meer informatie.

- Beschikbaarheidssets
    - Active Directory domein controllers
    - Cluster knooppunten en Witness SQL Server
- Azure Active Directory
- Azure Data Catalog
- Azure Key Vault
- Azure Monitor (Logboeken)
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure Virtual Machines
    - (1) bastion-host
    - (2) Active Directory domein controller
    - (2) SQL Server cluster knooppunt
    - (1) SQL Server Witness
- Azure Virtual Network
    - (1)/16 netwerk
    - (4)/24 netwerken
    - (4) netwerk beveiligings groepen
- Recovery Services-kluis
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Implementatie architectuur
De volgende sectie bevat informatie over de implementatie-en implementatie-elementen.

**Azure SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) is een Enter prise data warehouse dat gebruikmaakt van een zeer parallelle verwerking om snel complexe query's uit te voeren op PETA bytes gegevens. Gebruikers kunnen eenvoudige poly Base T-SQL-query's gebruiken om big data te importeren in het SQL Data Warehouse en de kracht van zeer parallelle verwerking te gebruiken voor het uitvoeren van analyses met hoge prestaties.

**SQL Server Reporting Services**: Met [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) kunt u snel rapporten maken met tabellen, grafieken, kaarten, meters, matrices en meer voor SQL Data Warehouse.

**Azure Data Catalog**: [Data Catalog](../../data-catalog/overview.md) maakt het eenvoudig om gegevens bronnen te detecteren en te begrijpen door de gebruikers die de gegevens beheren. U kunt algemene gegevens bronnen registreren, labelen en zoeken naar gegevens. De gegevens blijven op de bestaande locatie, maar een kopie van de meta gegevens wordt toegevoegd aan Data Catalog. Er wordt een verwijzing naar de locatie van de gegevens bron opgenomen. De meta gegevens worden geïndexeerd zodat elke gegevens bron gemakkelijk kan worden gedetecteerd via een zoek opdracht. Indexering maakt ook inzicht in de gebruikers die de app hebben gedetecteerd.

**Bastion-host**: De bastion-host is het enige toegangs punt dat gebruikers kunnen gebruiken om toegang te krijgen tot de geïmplementeerde resources in deze omgeving. De bastion-host biedt een beveiligde verbinding met geïmplementeerde bronnen door alleen extern verkeer van open bare IP-adressen op een veilige lijst toe te staan. Om extern bureau blad-verkeer toe te staan, moet de bron van het verkeer worden gedefinieerd in de netwerk beveiligings groep.

Met deze oplossing wordt een VM gemaakt als een aan een domein gekoppelde bastion-host met de volgende configuraties:
-   [Uitbrei ding van antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Azure Diagnostics extensie](../../virtual-machines/windows/extensions-diagnostics-template.md).
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) met behulp van Key Vault.
-   Een [beleid voor automatisch afsluiten](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) om het verbruik van VM-resources te verminderen wanneer het niet wordt gebruikt.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) is ingeschakeld, zodat referenties en andere geheimen worden uitgevoerd in een beveiligde omgeving die is geïsoleerd van het actieve besturings systeem.

### <a name="virtual-network"></a>Virtueel netwerk
Deze referentie architectuur definieert een particulier virtueel netwerk met een adres ruimte van 10.0.0.0/16.

**Netwerk beveiligings groepen**: [Netwerk beveiligings groepen](../../virtual-network/virtual-network-vnet-plan-design-arm.md) (Nsg's) bevatten toegangs beheer lijsten waarmee verkeer binnen een virtueel netwerk wordt toegestaan of geweigerd. Nsg's kan worden gebruikt om verkeer op een subnet of een afzonderlijk VM-niveau te beveiligen. De volgende Nsg's bestaan:
  - Een NSG voor de gegevenslaag (SQL Server clusters, SQL Server Witness en SQL load balancer)
  - Een NSG voor de bastion-host van het beheer
  - Een NSG voor Active Directory
  - Een NSG voor SQL Server Reporting Services

Voor elk van de Nsg's zijn specifieke poorten en protocollen geopend, zodat de oplossing veilig en goed werkt. Daarnaast zijn de volgende configuraties ingeschakeld voor elke NSG:
  - [Diagnostische logboeken en gebeurtenissen](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) worden ingeschakeld en opgeslagen in een opslag account.
  - Azure Monitor-Logboeken is verbonden met de [Diagnostische gegevens van de NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Subnetten**: Elk subnet is gekoppeld aan de bijbehorende NSG.

### <a name="data-at-rest"></a>Data-at-rest
De architectuur beveiligt gegevens in rust door meerdere metingen. Deze metingen omvatten versleuteling en database controle.

**Azure Storage**: Om te voldoen aan de vereisten voor versleutelde gegevens in rust, gebruikt alle [opslag](https://azure.microsoft.com/services/storage/) [Storage service Encryption](../../storage/common/storage-service-encryption.md). Deze functie helpt bij het beschermen en beschermen van gegevens ter ondersteuning van organisatie beveiligings verplichtingen en nalevings vereisten.

**Azure Disk Encryption**: [Schijf versleuteling](../azure-security-disk-encryption-overview.md) maakt gebruik van de BitLocker-functie van Windows om volume versleuteling te bieden voor besturings systeem en gegevens schijven. De oplossing kan worden geïntegreerd met Key Vault om de versleutelings sleutels voor de schijf te controleren en te beheren.

**Azure SQL Database**: Het SQL Database exemplaar maakt gebruik van de volgende data base Security-maat eenheden:
-   Met [Active Directory verificatie en autorisatie](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) kan identiteits beheer van database gebruikers en andere micro soft-Services op één centrale locatie worden beheerd.
-   Met [SQL database controle](../../sql-database/sql-database-auditing.md) worden database gebeurtenissen bijgehouden en naar een audit logboek in een Azure-opslag account geschreven.
-   SQL Database is geconfigureerd voor het gebruik van [transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). De data base, gekoppelde back-ups en transactie logboek bestanden worden in realtime versleuteld en ontsleuteld om informatie op rest te beveiligen. Transparent Data Encryption biedt zekerheid dat opgeslagen gegevens niet zijn onderworpen aan onbevoegde toegang.
-   [Firewall regels](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor komen dat alle toegang tot database servers tot de juiste machtigingen worden verleend. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt.
-   Met de [SQL-bedreigings detectie](../../sql-database/sql-database-threat-detection.md) kan de detectie en het antwoord op mogelijke dreigingen worden uitgevoerd. Het biedt beveiligings waarschuwingen voor verdachte database activiteiten, potentiële kwetsbaar heden, SQL-injectie aanvallen en afwijkende patronen voor database toegang.
-   [Versleutelde kolommen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zorgen ervoor dat gevoelige gegevens nooit als tekst zonder opmaak in het database systeem worden weer gegeven. Nadat de gegevens versleuteling is ingeschakeld, hebben alleen client toepassingen of app-servers met toegang tot de sleutels toegang tot tekst zonder opmaak.
- [Uitgebreide eigenschappen](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) kunnen worden gebruikt om de verwerking van gegevens houders te stoppen. Gebruikers kunnen aangepaste eigenschappen toevoegen aan database objecten. Ze kunnen ook gegevens als ' stopgezette ' labelen om toepassings logica te ondersteunen om te voor komen dat gekoppelde financiële gegevens worden verwerkt.
- Met [beveiliging op rijniveau](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kunnen gebruikers beleid definiëren om de toegang tot gegevens te beperken om de verwerking te stoppen.
- [SQL database dynamische gegevens maskering](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) beperkt de bloot stelling van gevoelige gegevens door de gegevens te maskeren voor gebruikers of toepassingen die niet zijn gemachtigd. Er kunnen mogelijk gevoelige gegevens automatisch worden gedetecteerd en de juiste maskers worden voorgesteld om toe te passen. Dynamische gegevens maskering helpt de toegang te beperken, zodat gevoelige gegevens de data base niet sluiten via onbevoegde toegang. *Klanten zijn verantwoordelijk voor het aanpassen van de instellingen om te voldoen aan hun database schema.*

### <a name="identity-management"></a>Identiteitsbeheer
De volgende technologieën bieden mogelijkheden voor het beheren van toegang tot gegevens in de Azure-omgeving:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) is de micro soft multi tenant-Cloud Directory en Identity Management-service. Alle gebruikers voor deze oplossing worden gemaakt in azure AD en bevatten de gebruikers die toegang hebben tot de SQL database.
-   Verificatie voor de toepassing wordt uitgevoerd met behulp van Azure AD. Zie [toepassingen integreren met Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)voor meer informatie. De database kolom versleuteling maakt ook gebruik van Azure AD om de toepassing te verifiëren voor SQL Database. Zie voor meer informatie hoe u [gevoelige gegevens in SQL database kunt beveiligen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) kan door beheerders worden gebruikt voor het definiëren van verfijnde toegangs machtigingen. Hiermee kunnen ze alleen de hoeveelheid toegang verlenen die gebruikers nodig hebben om hun taken uit te voeren. In plaats van elke gebruiker onbeperkte toegang voor Azure-resources te geven, kunnen beheerders alleen bepaalde acties toestaan voor het openen van bronnen en gegevens. Abonnements toegang is beperkt tot de abonnements beheerder.
- [Azure Active Directory privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) kunnen door klanten worden gebruikt om het aantal gebruikers dat toegang heeft tot bepaalde informatie, zoals gegevens, te minimaliseren. Beheerders kunnen Azure AD Privileged Identity Management gebruiken om bevoegde identiteiten en hun toegang tot bronnen te detecteren, beperken en controleren. Deze functie kan ook worden gebruikt voor het afdwingen van alleen-in-time-beheer toegang op aanvraag wanneer dit nodig is.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecteert mogelijke beveiligings problemen die van invloed zijn op de identiteiten van een organisatie. Hiermee configureert u automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op de identiteiten van een organisatie. Er worden ook verdachte incidenten onderzocht om de juiste maat regelen te nemen om deze op te lossen.

### <a name="security"></a>Beveiliging
**Beheer van geheimen**: De oplossing maakt gebruik van [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) voor het beheer van sleutels en geheimen. Key Vault helpt bij het beveiligen van cryptografische sleutels en geheimen die worden gebruikt door Cloud toepassingen en-services. Met de volgende Key Vault mogelijkheden kunnen klanten gegevens beveiligen:
- Geavanceerd toegangs beleid wordt geconfigureerd op basis van behoefte.
- Key Vault toegangs beleid wordt gedefinieerd met mini maal vereiste machtigingen voor sleutels en geheimen.
- Alle sleutels en geheimen in Key Vault hebben verloop datums.
- Alle sleutels in Key Vault worden beveiligd door gespecialiseerde hardware security modules. Het sleutel type is een met Hardware Security module beveiligde 2048-bits RSA-sleutel.
- Aan alle gebruikers en identiteiten worden mini maal vereiste machtigingen verleend met behulp van RBAC.
- Diagnostische logboeken voor Key Vault zijn ingeschakeld met een Bewaar periode van ten minste 365 dagen.
- Toegestane cryptografische bewerkingen voor sleutels zijn beperkt tot de vereisten die nodig zijn.

**Patch beheer**: Windows-Vm's die zijn geïmplementeerd als onderdeel van deze referentie architectuur, worden standaard geconfigureerd voor het ontvangen van automatische updates van Windows Update-service. Deze oplossing omvat ook de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) -service waarmee bijgewerkte implementaties kunnen worden gemaakt om zo nodig vm's te patchen.

**Bescherming tegen schadelijke software**: [Micro soft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) voor vm's biedt real-time beschermings mogelijkheden waarmee u virussen, spyware en andere schadelijke software kunt identificeren en verwijderen. Klanten kunnen waarschuwingen configureren die genereren wanneer bekende of ongewenste software probeert te installeren of uit te voeren op beveiligde Vm's.

**Azure Security Center**: Met [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)kunnen klanten centraal beveiligings beleid Toep assen en beheren voor werk belastingen, bloot stelling aan bedreigingen beperken en aanvallen detecteren en erop reageren. Security Center heeft ook toegang tot bestaande configuraties van Azure-Services om aanbevelingen voor de configuratie en service te bieden voor het verbeteren van de beveiliging postuur en het beschermen van gegevens.

Security Center maakt gebruik van diverse detectie mogelijkheden om klanten te waarschuwen over mogelijke aanvallen die gericht zijn op hun omgeving. Deze waarschuwingen bevatten waardevolle informatie over de trigger van de waarschuwing, de betrokken resources en de bron van de aanval. Security Center heeft een reeks [vooraf gedefinieerde beveiligings waarschuwingen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) die worden geactiveerd wanneer een bedreiging of verdachte activiteit plaatsvindt. Klanten kunnen [aangepaste waarschuwings regels](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) gebruiken om nieuwe beveiligings waarschuwingen te definiëren op basis van gegevens die al zijn verzameld uit hun omgeving.

Security Center biedt prioriteiten voor beveiligings waarschuwingen en incidenten. Security Center maakt het eenvoudiger voor klanten om potentiële beveiligings problemen te detecteren en op te lossen. Er wordt een [Threat Intelligence-rapport](https://docs.microsoft.com/azure/security-center/security-center-threat-report) gegenereerd voor elke gedetecteerde bedreiging. Incident response teams kunnen de rapporten gebruiken bij het onderzoeken en oplossen van bedreigingen.

Deze referentie architectuur maakt ook gebruik van de functie voor [evaluatie van beveiligings problemen](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) in Security Center. Nadat deze is geconfigureerd, rapporteert een partner agent (bijvoorbeeld Qualys) gegevens over het beveiligings probleem in het beheer platform van de partner. Het beheerplatform van de partner rapporteert op zijn beurt weer controlegegevens over beveiligingsproblemen en status naar Security Center. Klanten kunnen deze informatie gebruiken om snel kwets bare Vm's te identificeren.

### <a name="business-continuity"></a>Bedrijfscontinuïteit
**Hoge beschikbaarheid**: Server werkbelastingen worden gegroepeerd in een [beschikbaarheidsset](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) om te zorgen voor een hoge Beschik baarheid van Vm's in Azure. Er is ten minste één virtuele machine beschikbaar tijdens een geplande of niet-geplande onderhouds gebeurtenis die voldoet aan de 99,95% Azure SLA.

**Recovery Services kluis**: De [Recovery Services kluis](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) maakt back-upgegevens en beveiligt alle configuraties van vm's in deze architectuur. Met een Recovery Services kluis kunnen klanten bestanden en mappen herstellen vanaf een IaaS-VM zonder de hele virtuele machine te herstellen. Dit proces versnelt de herstel tijden.

### <a name="logging-and-auditing"></a>Logboekregistratie en bewaking

Azure-Services registreren systeem-en gebruikers activiteiten uitvoerig, evenals systeem status:
- **Activiteiten logboeken**: [Activiteiten logboeken](../../azure-monitor/platform/activity-logs-overview.md) bieden inzicht in bewerkingen die worden uitgevoerd op resources in een abonnement. Activiteiten logboeken kunnen helpen bij het bepalen van de initiator, het tijdstip van de gebeurtenis en de status van een bewerking.
- **Diagnostische logboeken**: [Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md) bevatten alle logboeken die elke resource heeft verzonden. Deze logboeken bevatten Windows-gebeurtenis systeem logboeken, opslag logboeken, Key Vault controle logboeken en Azure-toepassing gateway toegang en firewall Logboeken. Alle Diagnostische logboeken schrijven naar een gecentraliseerd en versleuteld Azure Storage-account voor archivering. Gebruikers kunnen de Bewaar periode tot 730 dagen configureren om te voldoen aan de specifieke vereisten.

**Azure monitor logboeken**: Deze logboeken worden samengevoegd in [Azure monitor logboeken](https://azure.microsoft.com/services/log-analytics/) voor verwerking, opslag en dashboard rapportage. Nadat de gegevens zijn verzameld, worden deze in verschillende tabellen ingedeeld voor elk gegevens type binnen Log Analytics werk ruimten. Op deze manier kunnen alle gegevens samen worden geanalyseerd, ongeacht de oorspronkelijke bron. Security Center integreert met Azure Monitor-Logboeken. Klanten kunnen Kusto-query's gebruiken om toegang te krijgen tot de gegevens van de beveiligings gebeurtenis en deze te combi neren met gegevens uit andere services.

De volgende Azure- [bewakings oplossingen](../../monitoring/monitoring-solutions.md) zijn opgenomen als onderdeel van deze architectuur:
-   [Active Directory beoordeling](../../azure-monitor/insights/ad-assessment.md): De Active Directory Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval. Het bevat een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [SQL-evaluatie](../../azure-monitor/insights/sql-assessment.md): De SQL Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval. Het biedt klanten een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [Status van agent](../../monitoring/monitoring-solution-agenthealth.md): De Status van agent oplossing meldt hoeveel agents er zijn geïmplementeerd en wat hun geografische distributie is. Ook wordt gerapporteerd hoeveel agents niet reageren en hoeveel agents operationele gegevens indienen.
-   [Analyse van activiteitenlogboek](../../azure-monitor/platform/collect-activity-logs.md): De Analyse van activiteitenlogboek oplossing helpt bij het analyseren van de activiteiten logboeken van Azure in alle Azure-abonnementen voor een klant.

**Azure Automation**: Met [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) worden runbooks opgeslagen, uitgevoerd en beheerd. In deze oplossing helpen runbooks bij het verzamelen van logboeken van SQL Database. Klanten kunnen de oplossing Automation [Wijzigingen bijhouden](../../automation/change-tracking.md) gebruiken om eenvoudig wijzigingen in de omgeving te identificeren.

**Azure monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) helpt gebruikers bij het volgen van prestaties, het onderhouden van de beveiliging en het identificeren van trends. Organisaties kunnen deze gebruiken om gegevens te controleren, te maken en te archiveren. Ze kunnen ook API-aanroepen volgen in hun Azure-resources.

## <a name="threat-model"></a>Bedreigings model

Het gegevens stroom diagram voor deze referentie architectuur is beschikbaar voor [downloaden](https://aka.ms/nist171-dw-tm) of kan hier worden gevonden. Dit model kan klanten helpen om inzicht te krijgen in de punten van potentieel risico in de systeem infrastructuur wanneer deze wijzigingen aanbrengen.

![Data Warehouse voor het NIST SP 800-171 Threat model](images/nist171-dw-threat-model.png "Data Warehouse voor het NIST SP 800-171 Threat model")

## <a name="compliance-documentation"></a>Documentatie voor naleving
In de matrix voor het [Azure-blauwdruk voor beveiliging en naleving-NIST sp 800-171-gebruikers verantwoordelijkheid](https://aka.ms/nist171-crm) worden alle beveiligings controles vermeld die zijn vereist voor het NIST SP 800-171. Deze matrix geeft aan of de implementatie van elk besturings element de verantwoordelijkheid is van micro soft, de klant of het gedeeld tussen de twee.

De [implementatie matrix van het Azure-blauwdruk voor beveiliging en naleving-NIST sp 800-171 Data Warehouse-besturings element](https://aka.ms/nist171-dw-cim) bevat informatie over welke systemen van het NIST-SP 800-171 worden gedekt door de architectuur van het Data Warehouse. Het bevat gedetailleerde beschrijvingen van de manier waarop de implementatie voldoet aan de vereisten van elk gedekte besturings element.

## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen

### <a name="vpn-and-expressroute"></a>VPN-en ExpressRoute
Een beveiligde VPN-tunnel of [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) moet worden geconfigureerd om een veilige verbinding tot stand te brengen met de resources die zijn geïmplementeerd als onderdeel van deze referentie architectuur van het Data Warehouse. Door een VPN-of ExpressRoute in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

Als u een beveiligde VPN-tunnel met Azure implementeert, kan een virtuele particuliere verbinding tussen een on-premises netwerk en een virtueel Azure-netwerk worden gemaakt. Deze verbinding vindt plaats via internet. Klanten kunnen deze verbinding gebruiken voor het veilig ' tunnel ' binnen een versleutelde koppeling tussen het netwerk van de klant en Azure. Site-naar-site-VPN is een veilige, rijpere technologie die is geïmplementeerd door ondernemingen van elke omvang voor tien tallen. De [IPSec-tunnel modus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wordt in deze optie als een versleutelings mechanisme gebruikt.

Omdat verkeer in de VPN-tunnel via internet met een site-naar-site-VPN passeert, biedt micro soft nog een nog veiliger verbindings optie. ExpressRoute is een specifieke WAN-verbinding tussen Azure en een on-premises locatie of een Exchange-hosting provider. ExpressRoute-verbindingen worden rechtstreeks verbonden met de telecommunicatie provider van de klant. Als gevolg hiervan worden de gegevens niet via internet verzonden en niet beschikbaar gemaakt. Deze verbindingen bieden meer betrouw baarheid, hogere snelheden, lagere wacht tijden en hogere beveiliging dan typische verbindingen.

Aanbevolen procedures voor het implementeren van een veilig hybride netwerk dat een on-premises netwerk uitbreidt naar Azure, zijn [beschikbaar](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Extractie-trans formatie-laad proces
[Poly base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan gegevens in SQL Data Warehouse laden zonder dat hiervoor een afzonderlijk ETL-of import programma nodig is. Poly Base biedt toegang tot gegevens via T-SQL-query's. De micro soft business intelligence-en analyse-stack en hulpprogram ma's van derden die compatibel zijn met SQL Server kunnen worden gebruikt in combi natie met poly base.

### <a name="azure-ad-setup"></a>Setup van Azure AD
[Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) is essentieel voor het beheren van de implementatie en het inrichten van de toegang tot mede werkers met de omgeving. On-premises Active Directory kan in [vier klikken](../../active-directory/hybrid/how-to-connect-install-express.md)worden geïntegreerd met Azure AD. Klanten kunnen ook de geïmplementeerde Active Directory-infra structuur (domein controllers) koppelen aan Azure AD. Als u dit wilt doen, maakt u de geïmplementeerde Active Directory-infra structuur een subdomein van een Azure AD-forest.

### <a name="optional-services"></a>Optionele services
Azure biedt diverse services die u kunnen helpen bij de opslag en het klaarzetten van geformatteerde en niet-opgemaakte gegevens. De volgende services kunnen worden toegevoegd aan deze referentie architectuur op basis van klant vereisten:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) is een beheerde Cloud service die is gebouwd voor complexe hybride ETL-, uitpak-load-en gegevens integratie projecten. Data Factory heeft mogelijkheden om gegevens te traceren en te vinden. Visualisatie-en controle hulpprogramma's bepalen wanneer de gegevens zijn aangekomen en waar ze vandaan komen. Klanten kunnen gegevensgestuurde werk stromen (pijp lijnen genoemd) maken en plannen die gegevens uit verschillende gegevens archieven opnemen. Ze kunnen de pijp lijnen gebruiken om gegevens van interne en externe bronnen op te nemen. Klanten kunnen vervolgens de gegevens verwerken en transformeren voor uitvoer naar gegevens archieven, zoals SQL Data Warehouse.
- Klanten kunnen ongestructureerde gegevens in [Azure data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) faseren voor het vastleggen van gegevens van elke grootte, type en opname snelheid op één locatie voor operationele en experimentele analyses. Azure Data Lake heeft mogelijkheden die het uitpakken en converteren van gegevens mogelijk maken. Data Lake Store is compatibel met de meeste open source-onderdelen in het Hadoop-ecosysteem. Het integreert ook mooi met andere Azure-Services, zoals SQL Data Warehouse.

## <a name="disclaimer"></a>Vrijwaring

 - Dit document is alleen ter informatie bedoeld. MICRO SOFT BIEDT GEEN ENKELE GARANTIE, UITDRUKKELIJK, IMPLICIET OF WETTELIJK, MET BETREKKING TOT DE INFORMATIE IN DIT DOCUMENT. Dit document wordt in de as-is opgenomen. Informatie en weer gaven in dit document, inclusief URL'S en andere website verwijzingen, kunnen zonder kennisgeving worden gewijzigd. Klanten die dit document lezen, hebben het risico van het gebruik ervan.
 - Dit document biedt klanten geen juridische rechten voor intellectueel eigendom in een micro soft-product of-oplossingen.
 - Klanten kunnen dit document kopiëren en gebruiken voor interne referentie doeleinden.
 - Bepaalde aanbevelingen in dit document kunnen leiden tot meer gegevens-, netwerk-of COMPUTE-resource gebruik in azure, en kunnen de kosten van de Azure-licentie of het abonnement van de klant verhogen.
 - Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.
 - Dit document is ontwikkeld als referentie en mag niet worden gebruikt voor het definiëren van de manier waarop een klant kan voldoen aan specifieke nalevings vereisten en-voor Schriften. Klanten moeten juridische ondersteuning van hun organisatie zoeken op goedgekeurde klant implementaties.
