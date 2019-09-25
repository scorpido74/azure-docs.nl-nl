---
title: Azure-blauwdruk voor beveiliging en naleving-gegevens analyse voor UK NHS
description: Azure-blauwdruk voor beveiliging en naleving-gegevens analyse voor UK NHS
services: security
author: jomolesk
ms.assetid: 103dff31-e262-44a6-9b50-3b3467c0cb3a
ms.service: security
ms.topic: article
ms.date: 06/15/2018
ms.author: jomolesk
ms.openlocfilehash: ad42bb7738e483b6bba6fa601a054e466b8d87c6
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259582"
---
# <a name="azure-security-and-compliance-blueprint-data-analytics-for-uk-nhs"></a>Azure-blauwdruk voor beveiliging en naleving: Gegevens analyse voor UK NHS

## <a name="overview"></a>Overzicht

Deze Azure-blauwdruk voor beveiliging en naleving biedt een referentie architectuur en richt lijnen voor een oplossing voor gegevens analyse die geschikt is voor het verzamelen, opslaan, analyseren en ophalen van gezondheids gegevens. Deze oplossing laat zien hoe klanten kunnen voldoen aan de richt lijnen die zijn opgenomen in de [hand leiding voor de Cloud beveiliging](https://digital.nhs.uk/data-and-information/looking-after-information/data-security-and-information-governance/nhs-and-social-care-data-off-shoring-and-the-use-of-public-cloud-services/health-and-social-care-cloud-security-good-practice-guide) die wordt gepubliceerd door [NHS Digital](https://digital.nhs.uk/), een partner van het Verenigd Konink rijk (UK) Department of Health and Social Care (DHSC). De hand leiding Cloud Security good practices is gebaseerd op de 14 [beveiligings principes](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) voor de cloud die worden gepubliceerd door het UK National Cyber Security Center (NCSC).

Deze referentie architectuur, implementatie handleiding en bedreigings model zijn bedoeld om te fungeren als basis voor klanten om aan hun specifieke behoeften aan te passen en mogen niet worden gebruikt als-in een productie omgeving zonder extra configuratie. Klanten zijn verantwoordelijk voor het uitvoeren van passende beveiligings-en nalevings beoordelingen van alle oplossingen die zijn gebouwd met behulp van deze architectuur, aangezien de vereisten kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

## <a name="architecture-diagram-and-components"></a>Architectuur diagram en onderdelen

Deze oplossing biedt een analyse platform waarop klanten hun eigen analyse hulpprogramma's kunnen bouwen. In de referentie architectuur wordt een algemene use-case beschreven waarbij klanten gegevens invoeren via bulksgewijze gegevens import door de SQL/gegevens beheerder of door middel van operationele gegevens updates via een operationele gebruiker. Beide werk stromen bevatten Azure Functions voor het importeren van gegevens in Azure SQL Database en voor alle externe verbindingen is TLSv 1.2 vereist. Azure Functions moet door de klant worden geconfigureerd via de Azure Portal voor het afhandelen van de import taken die uniek zijn voor de analyse vereisten van de klant.

Azure biedt een aantal rapportage-en analyse Services voor de klant. deze oplossing bevat echter Azure Analysis Services in combi natie met Azure SQL Database om snel door gegevens te bladeren en sneller resultaten te leveren via een slimmere model lering van klant gegevens. Azure Analytics Services is een vorm van machine learning die is bedoeld om de query snelheden te verhogen door nieuwe relaties tussen gegevens sets te detecteren. Zodra de gegevens zijn getraind met verschillende statistische functies, kunnen Maxi maal zeven extra query Pools (8 met inbegrip van de klant server) worden gesynchroniseerd met dezelfde tabellaire modellen om de werk belasting van query's te spreiden en de reactie tijden te verminderen.

Azure SQL-data bases kunnen worden geconfigureerd met column Store-indexen voor uitgebreide analyse en rapportage. Zowel Azure Analytics Services als Azure SQL-data bases kunnen omhoog of omlaag worden geschaald of volledig worden afgesloten in reactie op het gebruik van de klant. Alle SQL-verkeer wordt versleuteld met SSL via het opnemen van zelfondertekende certificaten. Als best practice, raadt Azure het gebruik aan van een vertrouwde certificerings instantie voor verbeterde beveiliging.

Zodra de gegevens zijn geüpload naar de Azure SQL Database en getraind door Azure Analysis Services, wordt deze door de operationele gebruiker en SQL/data-beheerder met Power BI gedigesteerd. Met Power BI worden gegevens intuïtief weer gegeven en worden gegevens in meerdere gegevens sets verzameld om meer inzicht te krijgen. Dankzij de hoge mate van aanpassing en eenvoudige integratie met Azure SQL Database zorgt u ervoor dat klanten deze kunnen configureren voor het afhandelen van een breed scala aan scenario's zoals vereist door hun bedrijfs behoeften.

De oplossing maakt gebruik van Azure Storage accounts, die klanten kunnen configureren om Storage Service Encryption te gebruiken om de vertrouwelijkheid van gegevens in rust te houden. In Azure worden drie kopieën van gegevens in het Data Center van de klant voor tolerantie opgeslagen. Geografisch redundante opslag zorgt ervoor dat gegevens worden gerepliceerd naar een secundair Data Center honderden kilo meters en worden opgeslagen als drie kopieën binnen dat Data Center, waardoor een nadelige gebeurtenis op het primaire Data Center van de klant wordt voor komen, wat leidt tot een verlies van gegevens.

Voor een betere beveiliging worden alle resources in deze oplossing als resource groep beheerd via Azure Resource Manager. Azure Active Directory op rollen gebaseerd toegangs beheer wordt gebruikt voor het beheren van de toegang tot geïmplementeerde resources, inclusief de bijbehorende sleutels in Azure Key Vault. Systeem status wordt bewaakt via Azure Security Center en Azure Monitor. Klanten configureren beide bewakings Services voor het vastleggen van Logboeken en het weer geven van de systeem status in één eenvoudig navigeerbaar-dash board.

Azure SQL Database wordt doorgaans beheerd via SQL Server Management Studio, dat wordt uitgevoerd vanaf een lokale computer die is geconfigureerd voor toegang tot de Azure SQL Database via een beveiligde VPN-of ExpressRoute-verbinding. U **wordt aangeraden een VPN-of ExpressRoute-verbinding te configureren voor beheer en het importeren van gegevens in de resource groep voor de referentie architectuur**.

![Analyse voor het NHS-diagram voor UK-referentie architectuur](images/uknhs-analytics-architecture.png?raw=true "Analyse voor het NHS-diagram voor UK-referentie architectuur")

Deze oplossing maakt gebruik van de volgende Azure-Services. Details van de implementatie architectuur vindt u in de sectie [implementatie architectuur](#deployment-architecture) .

- Azure Active Directory
- Azure Analysis Service
- Azure Automation
- Azure Data Catalog
- Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- Azure Key Vault
- Azure Monitor
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1)/16 netwerk
    - (2)/24 netwerken
    - (2) netwerk beveiligings groepen
- Power BI Dashboard

## <a name="deployment-architecture"></a>Implementatie architectuur

De volgende sectie bevat informatie over de implementatie-en implementatie-elementen.

**Azure functions**: [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) is een compute-service waarmee gebruikers code op aanvraag kunnen uitvoeren zonder expliciet een infra structuur in te richten of te beheren. Met Azure Functions kunt u een script of een stukje code uitvoeren in reactie op diverse gebeurtenissen.

**Azure Analysis Service**: [Azure Analysis Service](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) biedt bedrijfsgegevens modellering en-integratie met Azure data platform-Services. Azure Analysis Service versnelt het bladeren door enorme hoeveel heden gegevens door gegevens uit meerdere bronnen te combi neren in één gegevens model.

### <a name="virtual-network"></a>Virtueel netwerk

De architectuur definieert een particulier virtueel netwerk met een adres ruimte van 10.200.0.0/16.

**Netwerk beveiligings groepen**: [Netwerk beveiligings groepen](../../virtual-network/virtual-network-vnet-plan-design-arm.md) bevatten toegangs beheer lijsten waarmee verkeer binnen een virtueel netwerk wordt toegestaan of geweigerd. Netwerk beveiligings groepen kunnen worden gebruikt om verkeer op een subnet of op een afzonderlijke virtuele-machine niveau te beveiligen. De volgende netwerk beveiligings groepen bestaan:

  - 1 netwerk beveiligings groep voor Active Directory
  - 1 netwerk beveiligings groep voor de werk belasting

Voor elk van de netwerk beveiligings groepen zijn specifieke poorten en protocollen geopend, zodat de oplossing veilig en goed werkt. Daarnaast zijn de volgende configuraties ingeschakeld voor elke netwerk beveiligings groep:

- [Diagnostische logboeken en gebeurtenissen](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) worden ingeschakeld en opgeslagen in een opslag account
- Azure Monitor-logboeken zijn verbonden met de [netwerk&#39;beveiligings groep s Diagnostische logboeken](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Subnetten**: Elk subnet is gekoppeld aan de bijbehorende netwerk beveiligings groep.

### <a name="data-in-transit"></a>Actieve gegevens

Azure versleutelt standaard alle communicatie naar en van Azure-data centers. Alle trans acties die via de Azure Portal worden Azure Storage, vindt plaats via HTTPS.

### <a name="data-at-rest"></a>Data-at-rest

De architectuur beveiligt gegevens op rest door versleuteling, database controle en andere metingen.

**Azure Storage**: Om te voldoen aan de versleutelde gegevens op rest-vereisten, gebruikt alle [Azure Storage](https://azure.microsoft.com/services/storage/) [Storage service Encryption](../../storage/common/storage-service-encryption.md). Dit helpt bij het beschermen en beschermen van gegevens ter ondersteuning van organisatie beveiligings verplichtingen en nalevings vereisten die zijn gedefinieerd door NHS Digital.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) maakt gebruik van de BitLocker-functie van Windows om volume versleuteling voor gegevens schijven te bieden. De oplossing kan worden geïntegreerd met Azure Key Vault om de versleutelings sleutels voor de schijf te controleren en te beheren.

**Azure SQL Database**: Het Azure SQL Database exemplaar maakt gebruik van de volgende data base Security-maat eenheden:

- Met [Active Directory verificatie en autorisatie](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) kan identiteits beheer van database gebruikers en andere micro soft-Services op één centrale locatie worden beheerd.
- Met [SQL database controle](../../sql-database/sql-database-auditing.md) worden database gebeurtenissen bijgehouden en naar een audit logboek in een Azure-opslag account geschreven.
- Azure SQL Database is geconfigureerd voor het gebruik van [transparante gegevens versleuteling](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), waarmee in realtime versleuteling en ontsleuteling van de data base, gekoppelde back-ups en transactie logboek bestanden worden uitgevoerd om informatie te beveiligen. Transparent Data Encryption biedt zekerheid dat opgeslagen gegevens niet zijn onderworpen aan onbevoegde toegang.
- [Firewall regels](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor komen dat alle toegang tot database servers tot de juiste machtigingen worden verleend. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt.
- Met de [detectie van SQL-bedreigingen](../../sql-database/sql-database-threat-detection.md) kunnen mogelijke dreigingen worden gedetecteerd en gereageerd als ze optreden door beveiligings waarschuwingen te bieden voor verdachte database activiteiten, potentiële kwetsbaar heden, SQL-injectie aanvallen en afwijkende database toegangs patronen.
- [Versleutelde kolommen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zorgen ervoor dat gevoelige gegevens nooit als tekst zonder opmaak in het database systeem worden weer gegeven. Na het inschakelen van gegevens versleuteling, hebben alleen client toepassingen of toepassings servers met toegang tot de sleutels toegang tot tekst zonder opmaak.
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
- Alle sleutels in Key Vault worden beveiligd door gespecialiseerde hardware security modules. Het sleutel type is een met Hardware Security module beveiligde 2048-bits RSA-sleutel.
- Aan alle gebruikers en identiteiten worden mini maal vereiste machtigingen verleend met toegangs beheer op basis van rollen.
- Diagnostische logboeken voor Key Vault zijn ingeschakeld met een Bewaar periode van ten minste 365 dagen.
- Toegestane cryptografische bewerkingen voor sleutels zijn beperkt tot de vereisten die nodig zijn.

**Azure Security Center**: Met [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)kunnen klanten centraal beveiligings beleid Toep assen en beheren voor werk belastingen, bloot stelling aan bedreigingen beperken en aanvallen detecteren en erop reageren. Daarnaast Azure Security Center de bestaande configuraties van Azure-Services openen om aanbevelingen voor de configuratie en service te bieden voor het verbeteren van de beveiliging postuur en het beveiligen van gegevens.

Azure Security Center maakt gebruik van diverse detectie mogelijkheden om klanten te waarschuwen over mogelijke aanvallen die zijn gericht op hun omgeving. Deze waarschuwingen bevatten waardevolle informatie over de trigger van de waarschuwing, de betrokken resources en de bron van de aanval. Azure Security Center heeft een reeks [vooraf gedefinieerde beveiligings waarschuwingen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), die worden geactiveerd wanneer een bedreiging of verdachte activiteit plaatsvindt. Met [aangepaste waarschuwings regels](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) in azure Security Center kunnen klanten nieuwe beveiligings waarschuwingen definiëren op basis van gegevens die al zijn verzameld uit hun omgeving.

Azure Security Center biedt beveiligings waarschuwingen en-incidenten met prioriteit, waardoor klanten eenvoudiger mogelijke beveiligings problemen kunnen detecteren en oplossen. Er wordt een [Threat Intelligence-rapport](https://docs.microsoft.com/azure/security-center/security-center-threat-report) voor elke gedetecteerde bedreiging gegenereerd om de incidenten te helpen bij het onderzoeken en oplossen van bedreigingen.

### <a name="logging-and-auditing"></a>Logboekregistratie en bewaking

Azure-Services registreren systeem-en gebruikers activiteiten uitvoerig, evenals systeem status:
- **Activiteiten logboeken**: [Activiteiten logboeken](../../azure-monitor/platform/activity-logs-overview.md) bieden inzicht in bewerkingen die worden uitgevoerd op resources in een abonnement. Activiteiten logboeken kunnen helpen bij het bepalen van de initiator, het tijdstip van de gebeurtenis en de status van een bewerking.
- **Diagnostische logboeken**: [Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md) bevatten alle logboeken die elke resource heeft verzonden. Deze logboeken bevatten Windows-gebeurtenis systeem logboeken, Azure Storage logboeken, Key Vault controle logboeken en Application Gateway toegang en firewall Logboeken. Alle Diagnostische logboeken schrijven naar een gecentraliseerd en versleuteld Azure Storage-account voor archivering. De retentie kan door de gebruiker worden geconfigureerd, tot 730 dagen, om te voldoen aan de specifieke vereisten voor het bewaren van een organisatie.

**Azure monitor logboeken**: Deze logboeken worden samengevoegd in [Azure monitor logboeken](https://azure.microsoft.com/services/log-analytics/) voor verwerking, opslag en dashboard rapportage. Zodra de gegevens zijn verzameld, worden ze georganiseerd in aparte tabellen voor elk gegevenstype, zodat alle gegevens samen kunnen worden geanalyseerd, ongeacht de oorspronkelijke bron. Bovendien kan Azure Security Center worden geïntegreerd met Azure Monitor-logboeken, zodat klanten Kusto-query's kunnen gebruiken om toegang te krijgen tot de gegevens van de beveiligings gebeurtenis en deze te combi neren met gegevens uit andere services.

De volgende Azure- [bewakings oplossingen](../../monitoring/monitoring-solutions.md) zijn opgenomen als onderdeel van deze architectuur:
-   [Active Directory-evaluatie](../../azure-monitor/insights/ad-assessment.md): De Active Directory Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval en biedt een lijst met aanbevelingen die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [SQL-evaluatie](../../azure-monitor/insights/sql-assessment.md): De SQL Health Check-oplossing evalueert het risico en de status van de server omgevingen met een regel matig interval en biedt klanten een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [Status van agent](../../monitoring/monitoring-solution-agenthealth.md): De Status van agent oplossing meldt hoeveel agents zijn geïmplementeerd en wat hun geografische distributie zijn, en hoeveel agents niet reageren en het aantal agents dat operationele gegevens verzendt.
-   [Analyse van activiteitenlogboek](../../azure-monitor/platform/collect-activity-logs.md): De Analyse van activiteitenlogboek oplossing helpt bij het analyseren van de activiteiten logboeken van Azure in alle Azure-abonnementen voor een klant.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) winkels, uitvoeren en beheren van runbooks. In deze oplossing helpen runbooks bij het verzamelen van logboeken van Azure SQL Database. Met de oplossing Automation [Wijzigingen bijhouden](../../automation/change-tracking.md) kunnen klanten eenvoudig wijzigingen in de omgeving identificeren.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) helpt gebruikers bij het volgen van prestaties, het onderhouden van de beveiliging en het identificeren van trends door organisaties in te scha kelen, waarschuwingen te maken en gegevens te archiveren, met inbegrip van tracking-API-aanroepen in hun Azure-resources.

## <a name="threat-model"></a>Bedreigings model

Het gegevensstroom diagram voor deze referentie architectuur is beschikbaar voor [downloaden](https://aka.ms/uknhs-analytics-tm) of kan hieronder worden weer gegeven. Dit model kan klanten helpen om inzicht te krijgen in de punten van potentieel risico in de systeem infrastructuur bij het aanbrengen van wijzigingen.

![Analytics voor UK NHS Threat model](images/uknhs-analytics-threat-model.png?raw=true "Analytics voor UK NHS Threat model")

## <a name="compliance-documentation"></a>Documentatie voor naleving

De [Azure-blauwdruk voor beveiliging en naleving-NHS: UK matrix van de klant verantwoordelijkheid](https://aka.ms/uknhs-crm) bevat alle beveiligings principes die vereist zijn voor UK NHS. Deze matrix geeft aan of de implementatie van elk principe de verantwoordelijkheid is van micro soft, de klant of het gedeeld tussen de twee.

De [Azure-blauwdruk voor beveiliging en naleving-UK NHS Data Analytics-implementatie matrix](https://aka.ms/uknhs-analytics-cim) bevat informatie over welke UK NHS-vereisten worden behandeld door de architectuur voor gegevens analyse, met inbegrip van gedetailleerde beschrijvingen van de manier waarop de implementatie voldoet aan de vereisten van elk gedekt principe.


## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen

### <a name="vpn-and-expressroute"></a>VPN-en ExpressRoute

Een beveiligde VPN-tunnel of [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) moet worden geconfigureerd om een veilige verbinding tot stand te brengen met de resources die zijn geïmplementeerd als onderdeel van deze referentie architectuur voor gegevens analyse. Door een VPN-of ExpressRoute in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

Als u een beveiligde VPN-tunnel met Azure implementeert, kan een virtuele particuliere verbinding tussen een on-premises netwerk en een virtueel Azure-netwerk worden gemaakt. Deze verbinding vindt plaats via internet en biedt klanten de mogelijkheid om &quot;gegevens&quot; in een versleutelde koppeling tussen het&#39;netwerk van de klant en Azure veilig te tunnelen. Site-naar-site-VPN is een veilige, rijpere technologie die is geïmplementeerd door ondernemingen van elke omvang voor tien tallen. De [IPSec-tunnel modus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wordt in deze optie als een versleutelings mechanisme gebruikt.

Omdat verkeer binnen de VPN-tunnel via internet met een site-naar-site-VPN gaat, biedt micro soft een andere, nog veiliger verbindings optie. Azure ExpressRoute is een specifieke WAN-verbinding tussen Azure en een on-premises locatie of een Exchange-hosting provider. Omdat ExpressRoute-verbindingen niet via internet werken, bieden deze verbindingen meer betrouw baarheid, hogere snelheden, lagere latenties en een betere beveiliging dan typische verbindingen via internet. Omdat dit een directe verbinding is tussen de telecommunicatie&#39;provider van de klant, worden de gegevens niet via internet verzonden en worden ze daarom niet blootgesteld aan de service.

Aanbevolen procedures voor het implementeren van een veilig hybride netwerk dat een on-premises netwerk uitbreidt naar Azure, zijn [beschikbaar](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Extractie-trans formatie-laad proces

[Poly base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kan gegevens in Azure SQL database laden zonder dat er een afzonderlijk hulp programma voor extra heren, transformeren, laden of importeren nodig is. Poly Base biedt toegang tot gegevens via T-SQL-query's. De business intelligence-en analyse stack van micro soft, en hulpprogram ma's van derden die compatibel zijn met SQL Server, kunnen worden gebruikt met poly base.

### <a name="azure-active-directory-setup"></a>Azure Active Directory instellen
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) is essentieel voor het beheren van de implementatie en het inrichten van de toegang tot mede werkers met de omgeving. Een bestaande Windows Server-Active Directory kan in [vier klikken](../../active-directory/hybrid/how-to-connect-install-express.md)worden geïntegreerd met Azure Active Directory. Klanten kunnen de geïmplementeerde Active Directory-infra structuur (domein controllers) ook koppelen aan een bestaande Azure Active Directory door de geïmplementeerde Active Directory infra structuur een subdomein van een Azure Active Directory forest te maken.

## <a name="disclaimer"></a>Vrijwaring

 - Dit document is alleen ter informatie bedoeld. MICRO SOFT BIEDT GEEN ENKELE GARANTIE, UITDRUKKELIJK, IMPLICIET OF WETTELIJK, MET BETREKKING TOT DE INFORMATIE IN DIT DOCUMENT. Dit document wordt in de as-is opgenomen. Informatie en weer gaven in dit document, inclusief URL'S en andere website verwijzingen, kunnen zonder kennisgeving worden gewijzigd. Klanten die dit document lezen, hebben het risico van het gebruik ervan.
 - Dit document biedt klanten geen juridische rechten voor intellectueel eigendom in een micro soft-product of-oplossingen.
 - Klanten kunnen dit document kopiëren en gebruiken voor interne referentie doeleinden.
 - Bepaalde aanbevelingen in dit document kunnen leiden tot meer gegevens-, netwerk-of COMPUTE-resource gebruik in azure, en kunnen de kosten van de Azure-licentie of het abonnement van de klant verhogen.
 - Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.
 - Dit document is ontwikkeld als referentie en mag niet worden gebruikt voor het definiëren van de manier waarop een klant kan voldoen aan specifieke nalevings vereisten en-voor Schriften. Klanten moeten juridische ondersteuning van hun organisatie zoeken op goedgekeurde klant implementaties.
