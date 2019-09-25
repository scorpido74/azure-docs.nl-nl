---
title: Azure-blauwdruk voor beveiliging en naleving-data warehouse voor FFIEC
description: Azure-blauwdruk voor beveiliging en naleving-data warehouse voor FFIEC
services: security
author: meladie
ms.assetid: eb841702-057b-4e48-8bc8-2873e155d10e
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: meladie
ms.openlocfilehash: faf4e79208ce4ccf682673e314f28ca7a00b8e55
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257228"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-ffiec-financial-services"></a>Azure-blauwdruk voor beveiliging en naleving: Data Warehouse voor FFIEC Financial Services

## <a name="overview"></a>Overzicht

Deze Azure-blauwdruk voor beveiliging en naleving biedt richt lijnen voor de implementatie van een Data Warehouse-architectuur in azure die geschikt is voor het verzamelen, opslaan en ophalen van financiële gegevens die worden geregeld door de Raad van de federale financiële instelling onderzocht (FFIEC).

Deze referentie architectuur, implementatie handleiding en bedreigings model bieden een basis voor klanten om te voldoen aan de FFIEC-vereisten. Deze oplossing biedt een basis lijn om klanten te helpen bij het implementeren van werk belastingen naar Azure, maar deze oplossing mag niet worden gebruikt in een productie omgeving, omdat aanvullende configuratie is vereist.

Voor het bereiken van FFIEC-naleving moeten gekwalificeerde Audi tors een productie klant oplossing certificeren. Audits zijn van toepassing op onderzoekers van de FFIEC-instanties van de lidstaten, met inbegrip van de Raad van beheer van het Federal Reserve System (FRB), de Federal deposito Insurance Corporation (FDIC), de National Reunion Administration (NCUA), het kantoor van de Comptroller van de valuta (OCC) en het CFPB (consumenten financiële Protection Bureau). Deze onderzoekers verklaren dat audits worden uitgevoerd door beoordelaars die onafhankelijk van de gecontroleerde instelling zorgen. Klanten zijn verantwoordelijk voor het uitvoeren van passende beveiligings-en nalevings beoordelingen van alle oplossingen die zijn gebouwd met behulp van deze architectuur, aangezien de vereisten kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

## <a name="architecture-diagram-and-components"></a>Architectuur diagram en onderdelen

Deze oplossing biedt een referentie architectuur waarmee een hoogwaardig en veilig data warehouse in de Cloud wordt geïmplementeerd. Deze architectuur bevat twee afzonderlijke gegevens lagen: een waar gegevens worden geïmporteerd, opgeslagen en gefaseerd in een geclusterde SQL-omgeving worden geplaatst, en een andere voor de Azure SQL Data Warehouse waar de gegevens worden geladen met behulp van een extractie, trans formatie, laad programma (bijvoorbeeld [poly base ](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)T-SQL-query's) voor verwerking. Wanneer de gegevens zijn opgeslagen in Azure SQL Data Warehouse, kan Analytics op grote schaal worden uitgevoerd.

Azure biedt een aantal rapportage-en analyse Services voor de klant. Deze oplossing bevat SQL Server Reporting Services (SSRS) voor het snel maken van rapporten van de Azure SQL Data Warehouse. Alle SQL-verkeer wordt versleuteld met SSL via het opnemen van zelfondertekende certificaten. Als best practice, raadt Azure het gebruik aan van een vertrouwde certificerings instantie voor verbeterde beveiliging.

Gegevens in de Azure SQL Data Warehouse worden opgeslagen in relationele tabellen met kolom opslag, een indeling waarmee de kosten voor de gegevens opslag aanzienlijk worden verminderd en de query prestaties worden verbeterd. Afhankelijk van de gebruiks vereisten kunnen Azure SQL Data Warehouse Compute-resources omhoog of omlaag worden geschaald of volledig worden afgesloten als er geen actieve processen zijn die reken resources vereisen.

Een SQL-Load Balancer beheert SQL-verkeer, waardoor hoge prestaties worden gegarandeerd. Alle virtuele machines in deze referentie architectuur implementeren in een beschikbaarheidsset met SQL Server exemplaren die zijn geconfigureerd in een AlwaysOn-beschikbaarheids groep voor hoge Beschik baarheid en mogelijkheden voor herstel na een nood geval.

Deze referentie architectuur voor data warehouses bevat ook een Active Directory-laag voor het beheer van resources binnen de architectuur. Met het Active Directory subnet kunt u eenvoudig een grotere Active Directory forest-structuur maken, waardoor de omgeving voortdurend kan worden geactiveerd, zelfs wanneer toegang tot het grotere forest niet beschikbaar is. Alle virtuele machines zijn gekoppeld aan de laag Active Directory en gebruiken Active Directory groeps beleid om configuraties voor beveiliging en naleving af te dwingen op het niveau van het besturings systeem.

De oplossing maakt gebruik van Azure Storage accounts, die klanten kunnen configureren om Storage Service Encryption te gebruiken om de vertrouwelijkheid van gegevens in rust te houden. In Azure worden drie kopieën van gegevens opgeslagen in het geselecteerde Data Center van de klant voor tolerantie. Geografisch redundante opslag zorgt ervoor dat gegevens worden gerepliceerd naar een secundair Data Center honderden kilo meters en worden opgeslagen als drie kopieën binnen dat Data Center, waardoor een nadelige gebeurtenis op het primaire Data Center van de klant wordt voor komen, wat leidt tot een verlies van gegevens.

Voor een betere beveiliging worden alle resources in deze oplossing als resource groep beheerd via Azure Resource Manager. Azure Active Directory op rollen gebaseerd toegangs beheer wordt gebruikt voor het beheren van de toegang tot geïmplementeerde resources, inclusief de bijbehorende sleutels in Azure Key Vault. Systeem status wordt bewaakt via Azure Security Center en Azure Monitor. Klanten configureren beide bewakings Services voor het vastleggen van Logboeken en het weer geven van de systeem status in één eenvoudig navigeerbaar-dash board.

Een virtuele machine fungeert als een management bastion-host en biedt een beveiligde verbinding voor beheerders om toegang te krijgen tot geïmplementeerde bronnen. De gegevens worden in het faserings gebied geladen via deze beheer bastion-host. **U wordt aangeraden een VPN-of Azure ExpressRoute-verbinding te configureren voor beheer en gegevens import in het subnet met de referentie architectuur.**

![Data Warehouse voor FFIEC-referentie architectuur diagram](images/ffiec-dw-architecture.png "Data Warehouse voor FFIEC-referentie architectuur diagram")

Deze oplossing maakt gebruik van de volgende Azure-Services. Details van de implementatie architectuur vindt u in de sectie [implementatie architectuur](#deployment-architecture) .

- Beschikbaarheidssets
    - Active Directory-domein controllers
    - SQL-cluster knooppunten en Witness
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

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) is een edw (Enter prise Data Warehouse) dat gebruikmaakt van enorme parallelle verwerking (MPP) om snel complexe query's uit te voeren op PETA bytes van gegevens, waardoor gebruikers efficiënt financiële gegevens kunnen identificeren. Gebruikers kunnen eenvoudige poly Base T-SQL-query's gebruiken om big data te importeren in de SQL Data Warehouse en gebruik de kracht van MPP voor het uitvoeren van analyses met hoge prestaties.

**SQL Server Reporting Services (SSRS)** : Met [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) kunt u snel rapporten maken met tabellen, grafieken, kaarten, meters, matrices en meer voor Azure SQL Data Warehouse.

**Data Catalog**: [Data Catalog](../../data-catalog/overview.md) zorgt ervoor dat gegevens bronnen gemakkelijk kunnen worden gedetecteerd en begrijpelijk zijn voor de gebruikers die de gegevens beheren. Algemene gegevens bronnen kunnen worden geregistreerd, gelabeld en doorzocht op financiële gegevens. De gegevens blijven op de bestaande locatie, maar een kopie van de meta gegevens wordt toegevoegd aan Data Catalog, samen met een verwijzing naar de locatie van de gegevens bron. Deze metagegevens worden ook geïndexeerd zodat elke gegevensbron gemakkelijk kan worden gedetecteerd via zoekopdrachten, en begrijpelijk is voor gebruikers die de gegevensbron detecteren.

**Bastion-host**: De bastion-host is het enige toegangs punt dat gebruikers in staat stelt om toegang te krijgen tot de geïmplementeerde resources in deze omgeving. De bastion-host biedt een beveiligde verbinding met geïmplementeerde bronnen door alleen extern verkeer van open bare IP-adressen in een veilige lijst toe te staan. Als u RDP-verkeer (extern bureau blad) wilt toestaan, moet de bron van het verkeer worden gedefinieerd in de netwerk beveiligings groep.

Met deze oplossing maakt u een virtuele machine als een aan een domein gekoppelde bastion-host met de volgende configuraties:
-   [Uitbrei ding voor antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure Diagnostics extensie](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) Azure Key Vault gebruiken
-   Een [beleid voor automatisch afsluiten](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) om het verbruik van resources van de virtuele machine te verminderen wanneer het niet wordt gebruikt
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) is ingeschakeld, zodat referenties en andere geheimen worden uitgevoerd in een beveiligde omgeving die is geïsoleerd van het besturings systeem dat wordt uitgevoerd.

### <a name="virtual-network"></a>Virtueel netwerk

Deze referentie architectuur definieert een particulier virtueel netwerk met een adres ruimte van 10.0.0.0/16.

**Netwerk beveiligings groepen**: [Netwerk beveiligings groepen](../../virtual-network/virtual-network-vnet-plan-design-arm.md) bevatten toegangs beheer lijsten waarmee verkeer binnen een virtueel netwerk wordt toegestaan of geweigerd. Netwerk beveiligings groepen kunnen worden gebruikt om verkeer op een subnet of op een afzonderlijke virtuele-machine niveau te beveiligen. De volgende netwerk beveiligings groepen bestaan:

  - Een netwerk beveiligings groep voor de gegevenslaag (SQL Server clusters, SQL Server Witness en SQL Load Balancer)
  - Een netwerk beveiligings groep voor de Management bastion-host
  - Een netwerk beveiligings groep voor Active Directory
  - Een netwerk beveiligings groep voor SQL Server Reporting Services

Voor elk van de netwerk beveiligings groepen zijn specifieke poorten en protocollen geopend, zodat de oplossing veilig en goed werkt. Daarnaast zijn de volgende configuraties ingeschakeld voor elke netwerk beveiligings groep:

- [Diagnostische logboeken en gebeurtenissen](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) worden ingeschakeld en opgeslagen in een opslag account
- Azure Monitor-logboeken zijn verbonden met de [netwerk&#39;beveiligings groep s Diagnostische logboeken](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Subnetten**: Elk subnet is gekoppeld aan de bijbehorende netwerk beveiligings groep.

### <a name="data-at-rest"></a>Data-at-rest

De architectuur beveiligt gegevens in rust door meerdere metingen, inclusief versleuteling en database controle.

**Azure Storage**: Om te voldoen aan de versleutelde gegevens op rest-vereisten, gebruikt alle [Azure Storage](https://azure.microsoft.com/services/storage/) [Storage service Encryption](../../storage/common/storage-service-encryption.md). Dit helpt bij het beveiligen en beschermen van gegevens ter ondersteuning van organisatie beveiligings verplichtingen en nalevings vereisten die zijn gedefinieerd door FFIEC.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) maakt gebruik van de BitLocker-functie van Windows om volume versleuteling voor gegevens schijven te bieden. De oplossing kan worden geïntegreerd met Azure Key Vault om de versleutelings sleutels voor de schijf te controleren en te beheren.

**Azure SQL Database**: Het Azure SQL Database exemplaar maakt gebruik van de volgende data base Security-maat eenheden:

- Met [Active Directory verificatie en autorisatie](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) kan identiteits beheer van database gebruikers en andere micro soft-Services op één centrale locatie worden beheerd.
- Met [SQL database controle](../../sql-database/sql-database-auditing.md) worden database gebeurtenissen bijgehouden en naar een audit logboek in een Azure-opslag account geschreven.
- Azure SQL Database is geconfigureerd voor het gebruik van [transparante gegevens versleuteling](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), waarmee in realtime versleuteling en ontsleuteling van de data base, gekoppelde back-ups en transactie logboek bestanden worden uitgevoerd om informatie te beveiligen. Transparent Data Encryption biedt zekerheid dat opgeslagen gegevens niet zijn onderworpen aan onbevoegde toegang.
- [Firewall regels](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor komen dat alle toegang tot database servers tot de juiste machtigingen worden verleend. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt.
- Met de [detectie van SQL-bedreigingen](../../sql-database/sql-database-threat-detection.md) kunnen mogelijke dreigingen worden gedetecteerd en gereageerd als ze optreden door beveiligings waarschuwingen te bieden voor verdachte database activiteiten, potentiële kwetsbaar heden, SQL-injectie aanvallen en afwijkende database toegangs patronen.
- [Versleutelde kolommen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zorgen ervoor dat gevoelige gegevens nooit als tekst zonder opmaak in het database systeem worden weer gegeven. Na het inschakelen van gegevens versleuteling, hebben alleen client toepassingen of toepassings servers met toegang tot de sleutels toegang tot tekst zonder opmaak.
- [Uitgebreide eigenschappen](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) kunnen worden gebruikt om de verwerking van gegevens houders te stoppen, omdat hiermee aangepaste eigenschappen kunnen worden toegevoegd aan database objecten en gegevens worden gelabeld als ' stopgezet ' ter ondersteuning van toepassings logica om te voor komen dat de gekoppelde financiële gegevens worden verwerkt. gegevens.
- Met [beveiliging op rijniveau](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kunnen gebruikers beleid definiëren om de toegang tot gegevens te beperken om de verwerking te stoppen.
- [SQL database dynamische gegevens maskering](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) beperkt de bloot stelling van gevoelige gegevens door de gegevens te maskeren voor niet-gemachtigde gebruikers of toepassingen. Met dynamische gegevens maskering kunnen mogelijk gevoelige gegevens automatisch worden gedetecteerd en worden de juiste maskers voorgesteld om te worden toegepast. Zo kunt u de toegang tot gegevens identificeren en verminderen, zodat de data base niet wordt afgesloten via onbevoegde toegang. Klanten zijn verantwoordelijk voor het aanpassen van instellingen voor dynamische gegevens maskering om te voldoen aan hun database schema.

### <a name="identity-management"></a>Identiteitsbeheer

De volgende technologieën bieden mogelijkheden voor het beheren van toegang tot gegevens in de Azure-omgeving:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) is een&#39;op de cloud gebaseerde Directory-en identiteits beheer service van micro soft s met meerdere tenants. Alle gebruikers voor deze oplossing worden gemaakt in Azure Active Directory, met inbegrip van gebruikers die toegang hebben tot de Azure SQL Database.
- Verificatie voor de toepassing wordt uitgevoerd met behulp van Azure Active Directory. Zie [toepassingen integreren met Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)voor meer informatie. Daarnaast maakt de database kolom versleuteling gebruik van Azure Active Directory om de toepassing te verifiëren voor Azure SQL Database. Zie voor meer informatie hoe u [gevoelige gegevens in Azure SQL database kunt beveiligen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Met op [rollen gebaseerd toegangs beheer van Azure](../../role-based-access-control/role-assignments-portal.md) kunnen beheerders verfijnde toegangs machtigingen definiëren om alleen de hoeveelheid toegang te verlenen die gebruikers nodig hebben om hun taken uit te voeren. In plaats van elke gebruiker onbeperkte machtiging voor Azure-resources geven, kunnen beheerders alleen bepaalde acties toestaan voor toegang tot gegevens. Abonnements toegang is beperkt tot de abonnements beheerder.
- Met [Azure Active Directory privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) kunnen klanten het aantal gebruikers dat toegang heeft tot bepaalde informatie minimaliseren. Beheerders kunnen Azure Active Directory Privileged Identity Management gebruiken om bevoegde identiteiten en hun toegang tot bronnen te detecteren, beperken en controleren. Deze functie kan ook worden gebruikt voor het afdwingen van alleen-in-time-beheer toegang op aanvraag als dat nodig is.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecteert mogelijke beveiligings problemen die&#39;van invloed zijn op een organisatie s-identiteiten, configureert automatische antwoorden op&#39;gedetecteerde verdachte acties die betrekking hebben op de identiteit van een organisatie en onderzoekt verdachte incidenten om deze problemen op te lossen.

### <a name="security"></a>Beveiliging

**Beheer van geheimen**: De oplossing maakt gebruik van [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) voor het beheer van sleutels en geheimen. Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Met de volgende Azure Key Vault mogelijkheden kunnen klanten dergelijke gegevens beveiligen en benaderen:

- Geavanceerd toegangs beleid wordt geconfigureerd op basis van behoefte.
- Key Vault toegangs beleid wordt gedefinieerd met mini maal vereiste machtigingen voor sleutels en geheimen.
- Alle sleutels en geheimen in Key Vault hebben verloop datums.
- Alle sleutels in Key Vault worden beveiligd door gespecialiseerde hardware security modules. Het sleutel type is een met HSM beschermde 2048-bits RSA-sleutel.
- Aan alle gebruikers en identiteiten worden mini maal vereiste machtigingen verleend met toegangs beheer op basis van rollen.
- Diagnostische logboeken voor Key Vault zijn ingeschakeld met een Bewaar periode van ten minste 365 dagen.
- Toegestane cryptografische bewerkingen voor sleutels zijn beperkt tot de vereisten die nodig zijn.

**Patch beheer**: Virtuele Windows-machines die zijn geïmplementeerd als onderdeel van deze referentie architectuur, worden standaard geconfigureerd voor het ontvangen van automatische updates van Windows Update-service. Deze oplossing omvat ook de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) -service waarmee bijgewerkte implementaties kunnen worden gemaakt om virtuele machines te patchen wanneer dat nodig is.

**Bescherming tegen schadelijke software**: [Micro soft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) voor virtuele machines biedt real-time beschermings mogelijkheden waarmee u virussen, spyware en andere schadelijke software kunt identificeren en verwijderen, met Configureer bare waarschuwingen wanneer bekende of ongewenste software probeert om op beveiligde virtuele machines installeren of uitvoeren.

**Azure Security Center**: Met [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)kunnen klanten centraal beveiligings beleid Toep assen en beheren voor werk belastingen, bloot stelling aan bedreigingen beperken en aanvallen detecteren en erop reageren. Daarnaast Azure Security Center de bestaande configuraties van Azure-Services openen om aanbevelingen voor de configuratie en service te bieden voor het verbeteren van de beveiliging postuur en het beveiligen van gegevens.

Azure Security Center maakt gebruik van diverse detectie mogelijkheden om klanten te waarschuwen over mogelijke aanvallen die zijn gericht op hun omgeving. Deze waarschuwingen bevatten waardevolle informatie over de trigger van de waarschuwing, de betrokken resources en de bron van de aanval. Azure Security Center heeft een reeks [vooraf gedefinieerde beveiligings waarschuwingen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), die worden geactiveerd wanneer een bedreiging of verdachte activiteit plaatsvindt. Met [aangepaste waarschuwings regels](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) in azure Security Center kunnen klanten nieuwe beveiligings waarschuwingen definiëren op basis van gegevens die al zijn verzameld uit hun omgeving.

Azure Security Center biedt beveiligings waarschuwingen en-incidenten met prioriteit, waardoor klanten eenvoudiger mogelijke beveiligings problemen kunnen detecteren en oplossen. Er wordt een [Threat Intelligence-rapport](https://docs.microsoft.com/azure/security-center/security-center-threat-report) voor elke gedetecteerde bedreiging gegenereerd om de incidenten te helpen bij het onderzoeken en oplossen van bedreigingen.

### <a name="business-continuity"></a>Bedrijfscontinuïteit

**Hoge beschikbaarheid**: Server werkbelastingen worden gegroepeerd in een [beschikbaarheidsset](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) om te zorgen voor een hoge Beschik baarheid van virtuele machines in Azure. Er is ten minste één virtuele machine beschikbaar tijdens een geplande of niet-geplande onderhouds gebeurtenis die voldoet aan de 99,95% Azure SLA.

**Recovery Services kluis**: De [Recovery Services kluis](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) maakt back-upgegevens en beveiligt alle configuraties van virtuele Azure-machines in deze architectuur. Met een Recovery Services kluis kunnen klanten bestanden en mappen herstellen vanaf een virtuele IaaS-machine zonder de volledige virtuele machine te herstellen, waardoor de herstel tijd sneller verloopt.

### <a name="logging-and-auditing"></a>Logboekregistratie en bewaking

Azure-Services registreren systeem-en gebruikers activiteiten uitvoerig, evenals systeem status:
- **Activiteiten logboeken**: [Activiteiten logboeken](../../azure-monitor/platform/activity-logs-overview.md) bieden inzicht in bewerkingen die worden uitgevoerd op resources in een abonnement. Activiteiten logboeken kunnen helpen bij het bepalen van de initiator, het tijdstip van de gebeurtenis en de status van een bewerking.
- **Diagnostische logboeken**: [Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md) bevatten alle logboeken die elke resource heeft verzonden. Deze logboeken bevatten Windows-gebeurtenis systeem logboeken, Azure Storage logboeken, Key Vault controle logboeken en Application Gateway toegang en firewall Logboeken. Alle Diagnostische logboeken schrijven naar een gecentraliseerd en versleuteld Azure Storage-account voor archivering. De retentie kan door de gebruiker worden geconfigureerd, tot 730 dagen, om te voldoen aan de specifieke vereisten voor het bewaren van een organisatie.

**Azure monitor logboeken**: Deze logboeken worden samengevoegd in [Azure monitor logboeken](https://azure.microsoft.com/services/log-analytics/) voor verwerking, opslag en dashboard rapportage. Zodra de gegevens zijn verzameld, worden deze in verschillende tabellen ingedeeld voor elk gegevens type binnen Log Analytics werk ruimten, waardoor alle gegevens kunnen worden geanalyseerd, ongeacht de oorspronkelijke bron. Bovendien kan Azure Security Center worden geïntegreerd met Azure Monitor-logboeken, zodat klanten Kusto-query's kunnen gebruiken om toegang te krijgen tot de gegevens van de beveiligings gebeurtenis en deze te combi neren met gegevens uit andere services.

De volgende Azure- [bewakings oplossingen](../../monitoring/monitoring-solutions.md) zijn opgenomen als onderdeel van deze architectuur:
-   [Active Directory-evaluatie](../../azure-monitor/insights/ad-assessment.md): De Active Directory Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval en biedt een lijst met aanbevelingen die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [SQL-evaluatie](../../azure-monitor/insights/sql-assessment.md): De SQL Health Check-oplossing evalueert het risico en de status van de server omgevingen met een regel matig interval en biedt klanten een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [Status van agent](../../monitoring/monitoring-solution-agenthealth.md): De Status van agent oplossing meldt hoeveel agents zijn geïmplementeerd en wat hun geografische distributie zijn, en hoeveel agents niet reageren en het aantal agents dat operationele gegevens verzendt.
-   [Analyse van activiteitenlogboek](../../azure-monitor/platform/collect-activity-logs.md): De Analyse van activiteitenlogboek oplossing helpt bij het analyseren van de activiteiten logboeken van Azure in alle Azure-abonnementen voor een klant.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) winkels, uitvoeren en beheren van runbooks. In deze oplossing helpen runbooks bij het verzamelen van logboeken van Azure SQL Database. Met de oplossing Automation [Wijzigingen bijhouden](../../automation/change-tracking.md) kunnen klanten eenvoudig wijzigingen in de omgeving identificeren.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) helpt gebruikers bij het volgen van prestaties, het onderhouden van de beveiliging en het identificeren van trends door organisaties in te scha kelen, waarschuwingen te maken en gegevens te archiveren, met inbegrip van tracking-API-aanroepen in hun Azure-resources.

## <a name="threat-model"></a>Bedreigings model

Het gegevensstroom diagram voor deze referentie architectuur is beschikbaar voor [downloaden](https://aka.ms/ffiec-dw-tm/) of kan hieronder worden weer gegeven. Dit model kan klanten helpen om inzicht te krijgen in de punten van potentieel risico in de systeem infrastructuur bij het aanbrengen van wijzigingen.

![Data Warehouse voor FFIEC Threat model](images/ffiec-dw-threat-model.png "Data Warehouse voor FFIEC Threat model")

## <a name="compliance-documentation"></a>Documentatie voor naleving

De [FFIEC-matrix van de klant verantwoordelijkheid](https://aka.ms/ffiec-crm) van de Azure-blauwdruk voor beveiliging en naleving bevat alle doel stellingen die zijn vereist voor FFIEC. Deze matrix geeft aan of de implementatie van elke doel stelling de verantwoordelijkheid is van micro soft, de klant of het gedeeld tussen de twee.

De [Azure-blauwdruk voor beveiliging en naleving – FFIEC Data Warehouse-implementatie matrix](https://aka.ms/ffiec-dw-cim) bevat informatie over welke FFIEC-doel stellingen worden verholpen door de Data Warehouse-architectuur, met inbegrip van gedetailleerde beschrijvingen van de manier waarop de implementatie voldoet aan de vereisten van elke gedekte doel stelling.

## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen

### <a name="vpn-and-expressroute"></a>VPN-en ExpressRoute

Een beveiligde VPN-tunnel of [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) moet worden geconfigureerd om een veilige verbinding tot stand te brengen met de resources die zijn geïmplementeerd als onderdeel van deze referentie architectuur van het Data Warehouse. Door een VPN-of ExpressRoute in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

Door een beveiligde VPN-tunnel te implementeren met Azure, kan een virtuele particuliere verbinding tussen een on-premises netwerk en een Azure-Virtual Network worden gemaakt. Deze verbinding vindt plaats via internet en biedt klanten de mogelijkheid om &quot;gegevens&quot; in een versleutelde koppeling tussen het&#39;netwerk van de klant en Azure veilig te tunnelen. Site-naar-site-VPN is een veilige, rijpere technologie die is geïmplementeerd door ondernemingen van elke omvang voor tien tallen. De [IPSec-tunnel modus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wordt in deze optie als een versleutelings mechanisme gebruikt.

Omdat verkeer binnen de VPN-tunnel via internet met een site-naar-site-VPN gaat, biedt micro soft een andere, nog veiliger verbindings optie. Azure ExpressRoute is een specifieke WAN-verbinding tussen Azure en een on-premises locatie of een Exchange-hosting provider. Omdat ExpressRoute-verbindingen niet via internet werken, bieden deze verbindingen meer betrouw baarheid, hogere snelheden, lagere latenties en een betere beveiliging dan typische verbindingen via internet. Omdat dit een directe verbinding is tussen de telecommunicatie&#39;provider van de klant, worden de gegevens niet via internet verzonden en worden ze daarom niet blootgesteld aan de service.

Aanbevolen procedures voor het implementeren van een veilig hybride netwerk dat een on-premises netwerk uitbreidt naar Azure, zijn [beschikbaar](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Extractie-trans formatie-laad proces

[Poly base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan gegevens in Azure SQL Data Warehouse laden zonder dat er een afzonderlijk hulp programma voor extra heren, transformeren, laden of importeren nodig is. Poly Base biedt toegang tot gegevens via T-SQL-query's. De business intelligence-en analyse stack van micro soft, en hulpprogram ma's van derden die compatibel zijn met SQL Server, kunnen worden gebruikt met poly base.

### <a name="azure-active-directory-setup"></a>Azure Active Directory instellen

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) is essentieel voor het beheren van de implementatie en het inrichten van de toegang tot mede werkers met de omgeving. Een bestaande Windows Server-Active Directory kan in [vier klikken](../../active-directory/hybrid/how-to-connect-install-express.md)worden geïntegreerd met Azure Active Directory. Klanten kunnen de geïmplementeerde Active Directory-infra structuur (domein controllers) ook koppelen aan een bestaande Azure Active Directory door de geïmplementeerde Active Directory infra structuur een subdomein van een Azure Active Directory forest te maken.

### <a name="optional-services"></a>Optionele services

Azure biedt diverse services die u kunnen helpen bij de opslag en het klaarzetten van geformatteerde en niet-opgemaakte gegevens. De volgende services kunnen worden toegevoegd aan deze referentie architectuur, afhankelijk van de vereisten van de klant:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) is een beheerde Cloud service die is gebouwd voor complexe Hybrid extract-trans formatie-load-en gegevens integratie projecten. Azure Data Factory heeft mogelijkheden om financiële gegevens te traceren en te lokaliseren, inclusief visualisatie-en controle hulpprogramma's om te bepalen wanneer de gegevens zijn aangekomen en waar deze vandaan komen. Klanten kunnen met behulp van Azure Data Factory gegevensgestuurde werk stromen maken en plannen die gegevens uit verschillende gegevens archieven opnemen. Met deze pijp lijnen kunnen klanten gegevens opnemen van zowel interne als externe bronnen. Klanten kunnen vervolgens de gegevens verwerken en transformeren voor uitvoer naar gegevens archieven zoals Azure SQL Data Warehouse.
- Klanten kunnen ongestructureerde gegevens in [Azure data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview)faseren, waardoor het vastleggen van gegevens van elke grootte, type en opname snelheid op één plek mogelijk is voor operationele en experimentele analyses.  Azure Data Lake heeft mogelijkheden die het uitpakken en converteren van gegevens mogelijk maken. Azure Data Lake Store is compatibel met de meeste open source-onderdelen in het Hadoop-ecosysteem en integreert mooi met andere Azure-Services, zoals Azure SQL Data Warehouse.

## <a name="disclaimer"></a>Vrijwaring

 - Dit document is alleen ter informatie bedoeld. MICRO SOFT BIEDT GEEN ENKELE GARANTIE, UITDRUKKELIJK, IMPLICIET OF WETTELIJK, MET BETREKKING TOT DE INFORMATIE IN DIT DOCUMENT. Dit document wordt in de as-is opgenomen. Informatie en weer gaven in dit document, inclusief URL'S en andere website verwijzingen, kunnen zonder kennisgeving worden gewijzigd. Klanten die dit document lezen, hebben het risico van het gebruik ervan.
 - Dit document biedt klanten geen juridische rechten voor intellectueel eigendom in een micro soft-product of-oplossingen.
 - Klanten kunnen dit document kopiëren en gebruiken voor interne referentie doeleinden.
 - Bepaalde aanbevelingen in dit document kunnen leiden tot meer gegevens-, netwerk-of COMPUTE-resource gebruik in azure, en kunnen de kosten van de Azure-licentie of het abonnement van de klant verhogen.
 - Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.
 - Dit document is ontwikkeld als referentie en mag niet worden gebruikt voor het definiëren van de manier waarop een klant kan voldoen aan specifieke nalevings vereisten en-voor Schriften. Klanten moeten juridische ondersteuning van hun organisatie zoeken op goedgekeurde klant implementaties.
