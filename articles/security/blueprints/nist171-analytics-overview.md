---
title: Azure-blauwdruk voor beveiliging en naleving-gegevens analyse voor NIST SP 800-171
description: Azure-blauwdruk voor beveiliging en naleving-gegevens analyse voor NIST SP 800-171
services: security
author: jomolesk
ms.assetid: ca75d2a8-4e20-489b-9a9f-3a61e74b032d
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 2bd2510b3b7aa72ac5e66ac9910f1c941f276564
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259901"
---
# <a name="azure-security-and-compliance-blueprint---data-analytics-for-nist-sp-800-171"></a>Azure-blauwdruk voor beveiliging en naleving-gegevens analyse voor NIST SP 800-171

## <a name="overview"></a>Overzicht
De [800-171 speciale](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) cui van het NIST bevat richt lijnen voor het beveiligen van de beheerde niet-geclassificeerde informatie, die zich in niet-federale informatie systemen en organisaties bevindt. NIST SP 800-171 brengt 14 beveiligings vereisten tot stand voor het beschermen van de vertrouwelijkheid van CUI.

Deze Azure-blauwdruk voor beveiliging en naleving biedt hulp bij het implementeren van een architectuur voor gegevens analyse in azure waarmee een subset van de Controls van het NIST-besturings systeem 800-171 wordt geïmplementeerd. Deze oplossing laat zien hoe klanten kunnen voldoen aan specifieke vereisten voor beveiliging en naleving. Het fungeert ook als basis voor klanten om hun eigen gegevens analyse oplossingen te bouwen en te configureren in Azure.

Deze referentie architectuur, de bijbehorende implementatie handleiding en het bedreigings model zijn bedoeld om te fungeren als basis voor klanten om aan hun specifieke behoeften aan te passen. Ze mogen niet worden gebruikt als-in een productie omgeving. Het implementeren van deze architectuur zonder aanpassing is niet voldoende om volledig te voldoen aan de vereisten van NIST SP 800-171. Klanten zijn verantwoordelijk voor het uitvoeren van passende beveiligings-en nalevings evaluaties van een oplossing die is gebouwd op basis van deze architectuur. De vereisten kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

## <a name="architecture-diagram-and-components"></a>Architectuur diagram en onderdelen
Deze oplossing biedt een analyse platform waarop klanten hun eigen analyse hulpprogramma's kunnen bouwen. In de referentie architectuur wordt een algemene use-case beschreven. Klanten kunnen dit gebruiken om gegevens in te voeren via bulksgewijze gegevens import door de SQL/gegevens beheerder. Ze kunnen dit ook gebruiken voor het invoeren van gegevens met behulp van operationele gegevens updates via een operationele gebruiker. Beide workstreams bevatten Azure Functions voor het importeren van gegevens in Azure SQL Database. Azure Functions moet door de klant worden geconfigureerd via de Azure Portal voor het afhandelen van de import taken die uniek zijn voor de analyse vereisten van de klant.

Azure biedt een aantal rapportage-en analyse Services voor de klant. Deze oplossing maakt gebruik van Azure Machine Learning Services en SQL Database om snel gegevens te doorzoeken en snellere resultaten te leveren door middel van een slimmere modellering van gegevens. Machine Learning is bedoeld om query snelheden te verhogen door nieuwe relaties tussen gegevens sets te detecteren. In eerste instantie worden gegevens getraind via verschillende statistische functies. Daarna kunnen Maxi maal zeven extra query groepen worden gesynchroniseerd met dezelfde tabellaire modellen om de werk belasting van query's te spreiden en de reactie tijden te verminderen. De klant server brengt het totaal van de query Pools op acht.

SQL Database kan worden geconfigureerd met column Store-indexen voor uitgebreide analyse en rapportage. Machine Learning en SQL Database kunnen omhoog of omlaag worden geschaald of volledig worden afgesloten in reactie op het gebruik van de klant. Alle SQL-verkeer wordt versleuteld met SSL via het opnemen van zelfondertekende certificaten. Als best practice wordt u aangeraden een vertrouwde certificerings instantie te gebruiken voor verbeterde beveiliging.

Nadat de gegevens zijn geüpload naar de SQL database en getraind door Machine Learning, wordt deze door de operationele gebruiker en SQL/data-beheerder met Power BI gedigesteerd. Met Power BI worden gegevens intuïtief weer gegeven en worden gegevens in meerdere gegevens sets verzameld om meer inzicht te krijgen. Power BI heeft een hoge mate van aanpassing en kan eenvoudig worden geïntegreerd met SQL Database. Klanten kunnen het configureren om een breed scala aan scenario's te verwerken die vereist zijn voor hun bedrijfs behoeften.

De volledige oplossing is gebaseerd op Azure Storage, die klanten configureren van de Azure Portal. Opslag versleutelt alle gegevens met Storage Service Encryption om de vertrouwelijkheid van de gegevens in rust te behouden. Geografisch redundante opslag zorgt ervoor dat een nadelige gebeurtenis op het primaire Data Center van de klant geen verlies van gegevens oplevert. Een tweede kopie wordt opgeslagen op een afzonderlijke locatie, honderden kilo meters.

Voor een betere beveiliging worden alle resources in deze oplossing als resource groep beheerd via Azure Resource Manager. Op rollen gebaseerd toegangs beheer (RBAC) van Azure Active Directory (Azure AD) wordt gebruikt om de toegang tot geïmplementeerde resources te beheren. Deze resources omvatten klant sleutels in Azure Key Vault. Systeem status wordt bewaakt via Azure Security Center en Azure Monitor. Klanten configureren beide bewakings Services voor het vastleggen van Logboeken. De systeem status wordt weer gegeven in één dash board dat eenvoudig te gebruiken is.

SQL Database wordt vaak beheerd via SQL Server Management Studio. Het wordt uitgevoerd vanaf een lokale computer die is geconfigureerd voor toegang tot de SQL database via een beveiligde VPN-of Azure ExpressRoute-verbinding. *U wordt aangeraden een VPN-of ExpressRoute-verbinding te configureren voor beheer en het importeren van gegevens in de resource groep*.

![Diagram van gegevens analyse voor het NIST SP 800-171-referentie architectuur](images/nist171-analytics-architecture.png "Diagram van gegevens analyse voor het NIST SP 800-171-referentie architectuur")

Deze oplossing maakt gebruik van de volgende Azure-Services. Zie de sectie [implementatie architectuur](#deployment-architecture) voor meer informatie.

- Application Insights
- Azure Active Directory
- Azure Data Catalog
- Azure Disk Encryption
- Azure Event Grid
- Azure Functions
- Azure Key Vault
- Azure Machine Learning
- Azure Monitor (Logboeken)
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

**Azure Event grid**: Met [Event grid](https://docs.microsoft.com/azure/event-grid/overview)kunnen klanten eenvoudig toepassingen bouwen met op gebeurtenissen gebaseerde architecturen. Gebruikers selecteren de Azure-resource waarop ze zich willen abonneren. Vervolgens geven ze de gebeurtenis-handler of webhook een eind punt om de gebeurtenis naar te verzenden. Klanten kunnen webhook-eind punten beveiligen door query parameters toe te voegen aan de webhook-URL wanneer ze een gebeurtenis abonnement maken. Event Grid ondersteunt alleen HTTPS-webhook-eind punten. Met Event Grid kunnen klanten het toegangs niveau voor verschillende gebruikers bepalen om verschillende beheer bewerkingen uit te voeren. Gebruikers kunnen gebeurtenis abonnementen vermelden, nieuwe maken en sleutels genereren. Event Grid maakt gebruik van Azure RBAC.

**Azure functions**: [Azure functions](https://docs.microsoft.com/azure/azure-functions/functions-overview) is een serverloze compute-service die code op aanvraag uitvoert. U hoeft de infra structuur niet expliciet in te richten of te beheren. Met Azure Functions kunt u een script of een stukje code uitvoeren in reactie op diverse gebeurtenissen.

**Azure machine learning-service**: [Machine learning](https://docs.microsoft.com/azure/machine-learning/service/) is een techniek voor gegevens wetenschap waarmee computers bestaande gegevens kunnen gebruiken om toekomstige gedragingen, resultaten en trends te voors pellen.

**Azure Data Catalog**: [Data Catalog](../../data-catalog/overview.md) maakt het eenvoudig om gegevens bronnen te detecteren en te begrijpen door de gebruikers die de gegevens beheren. U kunt algemene gegevens bronnen registreren, labelen en zoeken naar gegevens. De gegevens blijven op de bestaande locatie, maar een kopie van de meta gegevens wordt toegevoegd aan Data Catalog. Er wordt een verwijzing naar de locatie van de gegevens bron opgenomen. De meta gegevens worden geïndexeerd zodat elke gegevens bron gemakkelijk kan worden gedetecteerd via een zoek opdracht. Indexering maakt ook inzicht in de gebruikers die de app hebben gedetecteerd.

### <a name="virtual-network"></a>Virtueel netwerk
Deze referentie architectuur definieert een particulier virtueel netwerk met een adres ruimte van 10.0.0.0/16.

**Netwerk beveiligings groepen**: [Netwerk beveiligings groepen](../../virtual-network/virtual-network-vnet-plan-design-arm.md) (Nsg's) bevatten toegangs beheer lijsten waarmee verkeer binnen een virtueel netwerk wordt toegestaan of geweigerd. Nsg's kan worden gebruikt om verkeer op een subnet of op een afzonderlijke virtuele-machine niveau te beveiligen. De volgende Nsg's bestaan:
  - Een NSG voor Active Directory
  - Een NSG voor de workload

Voor elk van de Nsg's zijn specifieke poorten en protocollen geopend, zodat de oplossing veilig en goed werkt. Daarnaast zijn de volgende configuraties ingeschakeld voor elke NSG:
  - [Diagnostische logboeken en gebeurtenissen](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) worden ingeschakeld en opgeslagen in een opslag account
  - Azure Monitor-logboeken zijn verbonden met de [Diagnostische gegevens van de NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Subnetten**: Elk subnet is gekoppeld aan de bijbehorende NSG.

### <a name="data-in-transit"></a>Actieve gegevens
Azure versleutelt standaard alle communicatie naar en van Azure-data centers. Alle trans acties voor opslag via de Azure Portal vinden plaats via HTTPS.

### <a name="data-at-rest"></a>Data-at-rest

De architectuur beveiligt gegevens op rest door versleuteling, database controle en andere metingen.

**Azure Storage**: Om te voldoen aan de vereisten voor versleutelde gegevens in rust, gebruikt alle [opslag](https://azure.microsoft.com/services/storage/) [Storage service Encryption](../../storage/common/storage-service-encryption.md). Deze functie helpt bij het beschermen en beschermen van gegevens ter ondersteuning van organisatie beveiligings verplichtingen en nalevings vereisten die zijn gedefinieerd door NIST SP 800-171.

**Azure Disk Encryption**: [Schijf versleuteling](../azure-security-disk-encryption-overview.md) maakt gebruik van de BitLocker-functie van Windows om volume versleuteling voor gegevens schijven te bieden. De oplossing kan worden geïntegreerd met Azure Key Vault om de versleutelings sleutels voor de schijf te controleren en te beheren.

**Azure SQL Database**: Het SQL Database exemplaar maakt gebruik van de volgende data base Security-maat eenheden:
-   Met [Active Directory verificatie en autorisatie](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) kan identiteits beheer van database gebruikers en andere micro soft-Services op één centrale locatie worden beheerd.
-   Met [SQL database controle](../../sql-database/sql-database-auditing.md) worden database gebeurtenissen bijgehouden en naar een audit logboek in een Azure-opslag account geschreven.
-   SQL Database is geconfigureerd voor het gebruik van [transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). De data base, gekoppelde back-ups en transactie logboek bestanden worden in realtime versleuteld en ontsleuteld om informatie op rest te beveiligen. Transparent Data Encryption biedt zekerheid dat opgeslagen gegevens niet zijn onderworpen aan onbevoegde toegang.
-   [Firewall regels](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor komen dat alle toegang tot database servers tot de juiste machtigingen worden verleend. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt.
-   Met de [SQL-bedreigings detectie](../../sql-database/sql-database-threat-detection.md) kan de detectie en het antwoord op mogelijke dreigingen worden uitgevoerd. Het biedt beveiligings waarschuwingen voor verdachte database activiteiten, potentiële kwetsbaar heden, SQL-injectie aanvallen en afwijkende patronen voor database toegang.
-   [Versleutelde kolommen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zorgen ervoor dat gevoelige gegevens nooit als tekst zonder opmaak in het database systeem worden weer gegeven. Nadat de gegevens versleuteling is ingeschakeld, hebben alleen client toepassingen of toepassings servers met toegang tot de sleutels toegang tot onbewerkte-tekst gegevens.
- [SQL database dynamische gegevens maskering](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) beperkt de bloot stelling van gevoelige gegevens door de gegevens te maskeren voor gebruikers of toepassingen die niet zijn gemachtigd. Er kunnen mogelijk gevoelige gegevens automatisch worden gedetecteerd en de juiste maskers worden voorgesteld om toe te passen. Dynamische gegevens maskering helpt de toegang te beperken, zodat gevoelige gegevens de data base niet sluiten via onbevoegde toegang. *Klanten zijn verantwoordelijk voor het aanpassen van de instellingen om te voldoen aan hun database schema.*

### <a name="identity-management"></a>Identiteitsbeheer
De volgende technologieën bieden mogelijkheden voor het beheren van toegang tot gegevens in de Azure-omgeving:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) is de micro soft multi tenant-Cloud Directory en Identity Management-service. Alle gebruikers voor deze oplossing worden gemaakt in azure AD en bevatten de gebruikers die toegang hebben tot SQL Database.
-   Verificatie voor de toepassing wordt uitgevoerd met behulp van Azure AD. Zie [toepassingen integreren met Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)voor meer informatie. De database kolom versleuteling maakt ook gebruik van Azure AD om de toepassing te verifiëren voor SQL Database. Zie voor meer informatie hoe u [gevoelige gegevens in SQL database kunt beveiligen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) kan door beheerders worden gebruikt voor het definiëren van verfijnde toegangs machtigingen. Hiermee kunnen ze alleen de hoeveelheid toegang verlenen die gebruikers nodig hebben om hun taken uit te voeren. In plaats van elke gebruiker onbeperkte machtigingen voor Azure-resources geven, kunnen beheerders alleen bepaalde acties toestaan voor toegang tot gegevens. Abonnements toegang is beperkt tot de abonnements beheerder.
- [Azure Active Directory privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) kunnen door klanten worden gebruikt om het aantal gebruikers dat toegang heeft tot bepaalde informatie, zoals gegevens, te minimaliseren. Beheerders kunnen Azure AD Privileged Identity Management gebruiken om bevoegde identiteiten en hun toegang tot bronnen te detecteren, beperken en controleren. Deze functie kan ook worden gebruikt voor het afdwingen van alleen-in-time-beheer toegang op aanvraag wanneer dit nodig is.
-   [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecteert mogelijke beveiligings problemen die van invloed zijn op de identiteiten van een organisatie. Hiermee configureert u automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op de identiteiten van een organisatie. Er worden ook verdachte incidenten onderzocht om de juiste maat regelen te nemen om deze op te lossen.

### <a name="security"></a>Beveiliging
**Beheer van geheimen**: De oplossing maakt gebruik van [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) voor het beheer van sleutels en geheimen. Key Vault helpt bij het beveiligen van cryptografische sleutels en geheimen die worden gebruikt door Cloud toepassingen en-services. Met de volgende Key Vault mogelijkheden kunnen klanten gegevens beveiligen:
- Geavanceerd toegangs beleid wordt geconfigureerd op basis van behoefte.
- Key Vault toegangs beleid wordt gedefinieerd met mini maal vereiste machtigingen voor sleutels en geheimen.
- Alle sleutels en geheimen in Key Vault hebben verloop datums.
- Alle sleutels in Key Vault worden beveiligd door gespecialiseerde hardware security modules. Het sleutel type is een met Hardware Security module beveiligde 2048-bits RSA-sleutel.
- Aan alle gebruikers en identiteiten worden mini maal vereiste machtigingen verleend met behulp van RBAC.
- Diagnostische logboeken voor Key Vault zijn ingeschakeld met een Bewaar periode van ten minste 365 dagen.
- Toegestane cryptografische bewerkingen voor sleutels zijn beperkt tot de vereisten die nodig zijn.

**Azure Security Center**: Met [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)kunnen klanten centraal beveiligings beleid Toep assen en beheren voor werk belastingen, bloot stelling aan bedreigingen beperken en aanvallen detecteren en erop reageren. Security Center heeft ook toegang tot bestaande configuraties van Azure-Services om aanbevelingen voor de configuratie en service te bieden voor het verbeteren van de beveiliging postuur en het beschermen van gegevens.

 Security Center maakt gebruik van diverse detectie mogelijkheden om klanten te waarschuwen over mogelijke aanvallen die gericht zijn op hun omgeving. Deze waarschuwingen bevatten waardevolle informatie over de trigger van de waarschuwing, de betrokken resources en de bron van de aanval. Security Center heeft een reeks [vooraf gedefinieerde beveiligings waarschuwingen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) die worden geactiveerd wanneer een bedreiging of verdachte activiteit plaatsvindt. Klanten kunnen [aangepaste waarschuwings regels](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) gebruiken om nieuwe beveiligings waarschuwingen te definiëren op basis van gegevens die al zijn verzameld uit hun omgeving.

 Security Center biedt prioriteiten voor beveiligings waarschuwingen en incidenten. Security Center maakt het eenvoudiger voor klanten om potentiële beveiligings problemen te detecteren en op te lossen. Er wordt een [Threat Intelligence-rapport](https://docs.microsoft.com/azure/security-center/security-center-threat-report) gegenereerd voor elke gedetecteerde bedreiging. Incident response teams kunnen de rapporten gebruiken bij het onderzoeken en oplossen van bedreigingen.

### <a name="logging-and-auditing"></a>Logboekregistratie en bewaking

Azure-Services registreren systeem-en gebruikers activiteiten uitvoerig, evenals systeem status:
- **Activiteiten logboeken**: [Activiteiten logboeken](../../azure-monitor/platform/activity-logs-overview.md) bieden inzicht in bewerkingen die worden uitgevoerd op resources in een abonnement. Activiteiten logboeken kunnen helpen bij het bepalen van de initiator, het tijdstip van de gebeurtenis en de status van een bewerking.
- **Diagnostische logboeken**: [Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md) bevatten alle logboeken die elke resource heeft verzonden. Deze logboeken bevatten Windows-gebeurtenis systeem logboeken, opslag logboeken, Key Vault controle logboeken en Azure-toepassing gateway toegang en firewall Logboeken. Alle Diagnostische logboeken schrijven naar een gecentraliseerd en versleuteld Azure Storage-account voor archivering. Gebruikers kunnen de Bewaar periode tot 730 dagen configureren om te voldoen aan de specifieke vereisten.

**Azure monitor logboeken**: Logboeken worden samengevoegd in [Azure monitor logboeken](https://azure.microsoft.com/services/log-analytics/) voor verwerking, opslag en dashboard rapportage. Nadat de gegevens zijn verzameld, worden deze in verschillende tabellen ingedeeld voor elk gegevens type binnen Log Analytics werk ruimten. Op deze manier kunnen alle gegevens samen worden geanalyseerd, ongeacht de oorspronkelijke bron. Security Center integreert met Azure Monitor-Logboeken. Klanten kunnen Kusto-query's gebruiken om toegang te krijgen tot de gegevens van de beveiligings gebeurtenis en deze te combi neren met gegevens uit andere services.

De volgende Azure- [bewakings oplossingen](../../monitoring/monitoring-solutions.md) zijn opgenomen als onderdeel van deze architectuur:
-   [Active Directory beoordeling](../../azure-monitor/insights/ad-assessment.md): De Active Directory Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval. Het bevat een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [SQL-evaluatie](../../azure-monitor/insights/sql-assessment.md): De SQL Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval. Het biedt klanten een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [Status van agent](../../monitoring/monitoring-solution-agenthealth.md): De Status van agent oplossing meldt hoeveel agents er zijn geïmplementeerd en wat hun geografische distributie is. Het rapport geeft ook aan hoeveel agents niet reageren en hoeveel agents operationele gegevens indienen.
-   [Analyse van activiteitenlogboek](../../azure-monitor/platform/collect-activity-logs.md): De Analyse van activiteitenlogboek oplossing helpt bij het analyseren van de activiteiten logboeken van Azure in alle Azure-abonnementen voor een klant.

**Azure Automation**: Met [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) worden runbooks opgeslagen, uitgevoerd en beheerd. In deze oplossing helpen runbooks bij het verzamelen van logboeken van SQL Database. Klanten kunnen de oplossing Automation [Wijzigingen bijhouden](../../automation/change-tracking.md) gebruiken om eenvoudig wijzigingen in de omgeving te identificeren.

**Azure monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) helpt gebruikers bij het volgen van prestaties, het onderhouden van de beveiliging en het identificeren van trends. Organisaties kunnen deze gebruiken om gegevens te controleren, te maken en te archiveren. Ze kunnen ook API-aanroepen volgen in hun Azure-resources.

**Application Insights**: [Application Insights](https://docs.microsoft.com/azure/application-insights/) is een uitbreid bare service voor het beheer van toepassings prestaties voor webontwikkelaars op meerdere platforms. Het detecteert prestatie afwijkingen en bevat krachtige analyse hulpprogramma's. Met de hulpprogram ma's kunt u problemen vaststellen en klanten helpen begrijpen wat gebruikers met de app doen. Het is ontworpen om gebruikers te helpen de prestaties en bruikbaarheid continu te verbeteren.

## <a name="threat-model"></a>Bedreigings model

Het gegevens stroom diagram voor deze referentie architectuur is beschikbaar voor [downloaden](https://aka.ms/nist171-analytics-tm) of kan hier worden gevonden. Dit model kan klanten helpen om inzicht te krijgen in de punten van potentieel risico in de systeem infrastructuur wanneer deze wijzigingen aanbrengen.

![Gegevens analyse voor het NIST SP 800-171 Threat model](images/nist171-analytics-threat-model.png "Gegevens analyse voor het NIST SP 800-171 Threat model")

## <a name="compliance-documentation"></a>Documentatie voor naleving
In de matrix voor het [Azure-blauwdruk voor beveiliging en naleving-NIST sp 800-171-gebruikers verantwoordelijkheid](https://aka.ms/nist171-crm) worden alle beveiligings controles vermeld die zijn vereist voor het NIST SP 800-171. Deze matrix geeft aan of de implementatie van elk besturings element de verantwoordelijkheid is van micro soft, de klant of het gedeeld tussen de twee.

De [implementatie matrix voor het beheer van de gegevens analyse van het Azure-blauwdruk voor beveiliging en naleving-800-171 NIST](https://aka.ms/nist171-analytics-cim) bevat informatie over welke NIST-sp-800-171-besturings elementen worden behandeld door de gegevens analyse-architectuur. Het bevat gedetailleerde beschrijvingen van de manier waarop de implementatie voldoet aan de vereisten van elk gedekte besturings element.


## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen
### <a name="vpn-and-expressroute"></a>VPN-en ExpressRoute
Een beveiligde VPN-tunnel of [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) moet worden geconfigureerd om een veilige verbinding tot stand te brengen met de resources die zijn geïmplementeerd als onderdeel van deze referentie architectuur voor gegevens analyse. Door een VPN-of ExpressRoute in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

Als u een beveiligde VPN-tunnel met Azure implementeert, kan een virtuele particuliere verbinding tussen een on-premises netwerk en een virtueel Azure-netwerk worden gemaakt. Deze verbinding vindt plaats via internet. Klanten kunnen de verbinding gebruiken voor het veilig ' tunnel ' binnen een versleutelde koppeling tussen hun netwerk en Azure. Site-naar-site-VPN is een veilige, rijpere technologie die is geïmplementeerd door ondernemingen van elke omvang voor tien tallen. De [IPSec-tunnel modus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wordt in deze optie als een versleutelings mechanisme gebruikt.

Omdat verkeer in de VPN-tunnel via internet met een site-naar-site-VPN passeert, biedt micro soft nog een nog veiliger verbindings optie. ExpressRoute is een specifieke WAN-verbinding tussen Azure en een on-premises locatie of een Exchange-hosting provider. ExpressRoute-verbindingen maken rechtstreeks verbinding met de telecommunicatie provider van een klant. Als gevolg hiervan worden de gegevens niet via internet verzonden en niet beschikbaar gemaakt. Deze verbindingen bieden meer betrouw baarheid, hogere snelheden, lagere wacht tijden en hogere beveiliging dan typische verbindingen. 

Aanbevolen procedures voor het implementeren van een veilig hybride netwerk dat een on-premises netwerk uitbreidt naar Azure, zijn [beschikbaar](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

### <a name="extract-transform-load-process"></a>Extractie-trans formatie-laad proces
Met [poly base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) kunnen gegevens worden geladen in SQL database zonder dat hiervoor een apart hulp programma voor het laden of importeren van de extractie wordt vereist. Poly Base biedt toegang tot gegevens via T-SQL-query's. De micro soft business intelligence-en analyse-stack en hulpprogram ma's van derden die compatibel zijn met SQL Server kunnen worden gebruikt in combi natie met poly base.

### <a name="azure-ad-setup"></a>Setup van Azure AD
[Azure AD](../../active-directory/fundamentals/active-directory-whatis.md) is essentieel voor het beheren van de implementatie en het inrichten van toegang tot mede werkers die communiceren met de omgeving. On-premises Active Directory kan in [vier klikken](../../active-directory/hybrid/how-to-connect-install-express.md)worden geïntegreerd met Azure AD. Klanten kunnen ook de geïmplementeerde Active Directory-infra structuur (domein controllers) koppelen aan Azure AD. Als u dit wilt doen, maakt u de geïmplementeerde Active Directory-infra structuur een subdomein van een Azure AD-forest.

## <a name="disclaimer"></a>Vrijwaring

 - Dit document is alleen ter informatie bedoeld. MICRO SOFT BIEDT GEEN ENKELE GARANTIE, UITDRUKKELIJK, IMPLICIET OF WETTELIJK, MET BETREKKING TOT DE INFORMATIE IN DIT DOCUMENT. Dit document wordt in de as-is opgenomen. Informatie en weer gaven in dit document, inclusief URL'S en andere website verwijzingen, kunnen zonder kennisgeving worden gewijzigd. Klanten die dit document lezen, hebben het risico van het gebruik ervan.
 - Dit document biedt klanten geen juridische rechten voor intellectueel eigendom in een micro soft-product of-oplossingen.
 - Klanten kunnen dit document kopiëren en gebruiken voor interne referentie doeleinden.
 - Bepaalde aanbevelingen in dit document kunnen leiden tot meer gegevens-, netwerk-of COMPUTE-resource gebruik in azure, en kunnen de kosten van de Azure-licentie of het abonnement van de klant verhogen.
 - Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.
 - Dit document is ontwikkeld als referentie en mag niet worden gebruikt voor het definiëren van de manier waarop een klant kan voldoen aan specifieke nalevings vereisten en-voor Schriften. Klanten moeten juridische ondersteuning van hun organisatie zoeken op goedgekeurde klant implementaties.
