---
title: Azure-blauwdruk voor beveiliging en naleving-PaaS-webtoepassing voor NIST SP 800-171
description: 'Azure-blauwdruk voor beveiliging en naleving-PaaS: Web Application NIST SP 800-171'
services: security
author: jomolesk
ms.assetid: eea21a0a-5930-43e8-937f-5419c20744c9
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 22b18988406beb5bed491d7101f376340d35f623
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259791"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-nist-special-publication-800-171"></a>Azure-blauwdruk voor beveiliging en naleving-PaaS-webtoepassing voor NIST speciale publicatie 800-171

## <a name="overview"></a>Overzicht
De [800-171 speciale](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) cui van het NIST bevat richt lijnen voor het beveiligen van de beheerde niet-geclassificeerde informatie, die zich in niet-federale informatie systemen en organisaties bevindt. NIST SP 800-171 brengt 14 beveiligings vereisten tot stand voor het beschermen van de vertrouwelijkheid van CUI.

Deze Azure-blauwdruk voor beveiliging en naleving biedt hulp bij het implementeren van een platform as a Service-webtoepassing (PaaS) in azure, waarmee een subset van de Controls van het NIST-besturings systeem 800-171 wordt geïmplementeerd. Deze oplossing laat zien hoe klanten kunnen voldoen aan specifieke vereisten voor beveiliging en naleving. Het fungeert ook als basis voor klanten om hun eigen webtoepassing te bouwen en te configureren in Azure.

Deze referentie architectuur, de bijbehorende implementatie handleiding en het bedreigings model zijn bedoeld om te fungeren als basis voor klanten om aan hun specifieke behoeften aan te passen. Ze mogen niet worden gebruikt als-in een productie omgeving. Het implementeren van deze architectuur zonder aanpassing is niet voldoende om volledig te voldoen aan de vereisten van NIST SP 800-171. Klanten zijn verantwoordelijk voor het uitvoeren van passende beveiligings-en nalevings beoordelingen van alle oplossingen die zijn gebouwd met behulp van deze architectuur. De vereisten kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

## <a name="architecture-diagram-and-components"></a>Architectuur diagram en onderdelen

Deze Azure-blauwdruk voor beveiliging en naleving biedt een referentie architectuur voor een PaaS-webtoepassing met een Azure SQL Database back-end. De webtoepassing wordt gehost in een geïsoleerde App Service omgeving, een persoonlijke, toegewezen omgeving in een Azure-Data Center. De omgevings taak verdeelt het verkeer voor de webtoepassing op virtuele machines (Vm's) die worden beheerd door Azure. Deze architectuur omvat ook netwerk beveiligings groepen (Nsg's), een Azure Application Gateway, Azure DNS en Azure Load Balancer.

Azure SQL-data bases kunnen worden geconfigureerd met column Store-indexen voor uitgebreide analyse en rapportage. Azure SQL-data bases kunnen omhoog of omlaag worden geschaald of volledig worden afgesloten in reactie op het gebruik van de klant. Alle SQL-verkeer wordt versleuteld met SSL via het opnemen van zelfondertekende certificaten. Als best practice, raadt Azure het gebruik aan van een vertrouwde certificerings instantie voor verbeterde beveiliging.

De oplossing maakt gebruik van Azure Storage accounts, die klanten kunnen configureren om Storage Service Encryption te gebruiken om de vertrouwelijkheid van gegevens in rust te houden. In Azure worden drie kopieën van gegevens opgeslagen in het geselecteerde Data Center van de klant voor tolerantie. Geografisch redundante opslag zorgt ervoor dat gegevens worden gerepliceerd naar een secundair Data Center van honderden kilo meters en opnieuw worden opgeslagen als drie kopieën binnen dat Data Center. Deze regeling voor komt dat een nadelige gebeurtenis op het primaire Data Center van de klant leidt tot verlies van gegevens.

Voor een betere beveiliging worden alle resources in deze oplossing als resource groep beheerd via Azure Resource Manager. Op rollen gebaseerd toegangs beheer (RBAC) van Azure Active Directory (Azure AD) wordt gebruikt om de toegang tot geïmplementeerde resources te beheren. Deze resources omvatten klant sleutels in Azure Key Vault. De systeem status wordt bewaakt via Azure Monitor. Klanten configureren deze bewakings service voor het vastleggen van Logboeken. De systeem status wordt weer gegeven in één dash board dat eenvoudig te gebruiken is.

SQL Database wordt vaak beheerd via SQL Server Management Studio. Het wordt uitgevoerd vanaf een lokale computer die is geconfigureerd voor toegang tot de SQL database via een beveiligde VPN-of Azure ExpressRoute-verbinding.

Application Insights voorziet in realtime beheer en analyse van toepassings prestaties via Azure Monitor logboeken *micro soft raadt u aan een VPN-of ExpressRoute-verbinding te configureren voor beheer en gegevens import in de referentie architectuur subnet.*

![PaaS-webtoepassing voor NIST SP 800-171-referentie architectuur diagram](images/nist171-paaswa-architecture.png "PaaS-webtoepassing voor NIST SP 800-171-referentie architectuur diagram")

Deze oplossing maakt gebruik van de volgende Azure-Services. Zie de sectie [implementatie architectuur](#deployment-architecture) voor meer informatie.

- Azure Virtual Machines
    - (1) beheer/Bastion (Windows Server 2016 Data Center)
- Azure Virtual Network
    - (1)/16 netwerk
    - (4)/24 netwerken
    - (4) netwerk beveiligings groepen
- Azure Application Gateway
    - Web Application Firewall
        - Firewall modus: voor komen
        - Regelset: OWASP
        - Listener-poort: 443
- Application Insights
- Azure Active Directory
- App Service Environment v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (Logboeken)
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Automation
- Azure Web Apps

## <a name="deployment-architecture"></a>Implementatie architectuur
De volgende sectie bevat informatie over de implementatie-en implementatie-elementen.

**Azure Resource Manager**: [Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) kan door klanten worden gebruikt om te werken met de resources in de oplossing als groep. Klanten kunnen alle resources voor de oplossing implementeren, bijwerken of verwijderen in een enkele, gecoördineerde bewerking. Klanten gebruiken een sjabloon voor de implementatie. De sjabloon kan worden gebruikt voor verschillende omgevingen, zoals testen, faseren en productie. Resource Manager biedt functies voor beveiliging, controle en labeling om klanten te helpen bij het beheren van hun resources na de implementatie.

**Bastion-host**: De bastion-host is het enige toegangs punt dat gebruikers kunnen gebruiken om toegang te krijgen tot de geïmplementeerde resources in deze omgeving. De bastion-host biedt een beveiligde verbinding met geïmplementeerde bronnen door alleen extern verkeer van open bare IP-adressen op een veilige lijst toe te staan. Om extern bureau blad-verkeer toe te staan, moet de bron van het verkeer worden gedefinieerd in de NSG.

Met deze oplossing wordt een VM gemaakt als een aan een domein gekoppelde bastion-host met de volgende configuraties:
-   [Uitbrei ding van antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Azure Diagnostics extensie](../../virtual-machines/windows/extensions-diagnostics-template.md).
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) met behulp van Key Vault.
-   Een [beleid voor automatisch afsluiten](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) om het verbruik van VM-resources te verminderen wanneer het niet wordt gebruikt.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) is ingeschakeld, zodat referenties en andere geheimen worden uitgevoerd in een beveiligde omgeving die is geïsoleerd van het besturings systeem dat wordt uitgevoerd.

**Web apps**: [Web apps](https://docs.microsoft.com/azure/app-service/) is een Azure app service-functie. Klanten kunnen het gebruiken om webtoepassingen te bouwen en te hosten in de programmeer taal van hun keuze zonder dat u infra structuur hoeft te beheren. Het biedt automatisch schalen en hoge Beschik baarheid. Het ondersteunt Windows en Linux en maakt automatische implementaties mogelijk vanuit GitHub, Azure DevOps of een Git-opslag plaats.

**App service Environment**: [App service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) is een app service-functie. Het biedt een volledig geïsoleerde en toegewezen omgeving voor het veilig uitvoeren van App Service toepassingen op een grote schaal.

De App Service omgeving is geïsoleerd om slechts één toepassing uit te voeren. Het wordt altijd geïmplementeerd in een virtueel netwerk. Vanwege de isolatie functie heeft de referentie architectuur de isolatie van de Tenant voltooid en wordt deze verwijderd uit de multi tenant-omgeving van Azure. Klanten hebben nauw keurige controle over zowel binnenkomend als uitgaand netwerk verkeer van toepassingen. Toepassingen kunnen snelle, veilige verbindingen met virtuele netwerken tot stand brengen met on-premises bedrijfs bronnen. Klanten kunnen ' automatisch schalen ' met App Service Environment op basis van metrische gegevens over belasting, beschikbaar budget of een gedefinieerd schema.

Het gebruik van App Service Environment voor deze architectuur biedt de volgende besturings elementen en configuraties:

- Host binnen een beveiligde Azure Virtual Network-en netwerk beveiligings regels.
- Zelfondertekend intern load balancer certificaat voor HTTPS-communicatie. Als best practice, raadt micro soft u aan het gebruik van een vertrouwde certificerings instantie te gebruiken voor verbeterde beveiliging.
- [Modus voor interne taak verdeling](../../app-service/environment/app-service-environment-with-internal-load-balancer.md) (modus 3).
- Schakel [TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md)uit.
- [TLS-versleuteling](../../app-service/environment/app-service-app-service-environment-custom-settings.md)wijzigen.
- Beheer [poorten voor binnenkomend verkeer N/W](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md).
- [Web Application firewall: gegevens beperken](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md).
- [Azure SQL database verkeer](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md)toestaan.

### <a name="virtual-network"></a>Virtueel netwerk
De architectuur definieert een particulier virtueel netwerk met een adres ruimte van 10.200.0.0/16.

**Netwerk beveiligings groepen**: [Nsg's](../../virtual-network/virtual-network-vnet-plan-design-arm.md) bevatten toegangs beheer lijsten waarmee verkeer binnen een virtueel netwerk wordt toegestaan of geweigerd. Nsg's kan worden gebruikt om verkeer op een subnet of een afzonderlijk VM-niveau te beveiligen. De volgende Nsg's bestaan:
- Eén NSG voor Application Gateway
- Eén NSG voor App Service Environment
- Eén NSG voor SQL Database
- Eén NSG voor Bastion-host

Voor elk van de Nsg's zijn specifieke poorten en protocollen geopend, zodat de oplossing veilig en goed werkt. Daarnaast zijn de volgende configuraties ingeschakeld voor elke NSG:
  - [Diagnostische logboeken en gebeurtenissen](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) worden ingeschakeld en opgeslagen in een opslag account.
  - Azure Monitor-Logboeken is verbonden met de [Diagnostische gegevens van de NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Subnetten**: Elk subnet is gekoppeld aan de bijbehorende NSG.

**Azure DNS**: De Domain Name System (DNS) is verantwoordelijk voor het vertalen van een website-of service naam naar het IP-adres. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) is een hosting service voor DNS-domeinen die naam omzetting biedt met behulp van Azure-infra structuur. Gebruikers kunnen met behulp van dezelfde referenties, Api's, hulpprogram ma's en facturering als andere Azure-Services DNS-records beheren door in azure hosting domeinen te hosten. Azure DNS biedt ook ondersteuning voor privé-DNS-domeinen.

**Azure Load Balancer**: [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) kunnen door klanten worden gebruikt om hun toepassingen te schalen en hoge Beschik baarheid voor services te maken. Load Balancer ondersteunt de scenario's voor inkomend en uitgaand verkeer. Het biedt een lage latentie en hoge door Voer en schaalt Maxi maal miljoenen stromen voor alle TCP-en UDP-toepassingen.

### <a name="data-in-transit"></a>Actieve gegevens
Azure versleutelt standaard alle communicatie naar en van Azure-data centers. Alle trans acties die via de Azure Portal worden Azure Storage, vindt plaats via HTTPS.

### <a name="data-at-rest"></a>Data-at-rest

De architectuur beveiligt gegevens op rest door versleuteling, database controle en andere metingen.

**Azure Storage**: Om te voldoen aan de vereisten voor versleutelde gegevens in rust, gebruikt alle [opslag](https://azure.microsoft.com/services/storage/) [Storage service Encryption](../../storage/common/storage-service-encryption.md). Deze functie helpt bij het beschermen en beschermen van gegevens ter ondersteuning van organisatie beveiligings verplichtingen en nalevings vereisten die zijn gedefinieerd door NIST SP 800-171.

**Azure Disk Encryption**: [Schijf versleuteling](../azure-security-disk-encryption-overview.md) maakt gebruik van de BitLocker-functie van Windows om volume versleuteling voor gegevens schijven te bieden. De oplossing kan worden geïntegreerd met Key Vault om de versleutelings sleutels voor de schijf te controleren en te beheren.

**Azure SQL Database**: Het SQL Database exemplaar maakt gebruik van de volgende data base Security-maat eenheden:
-   Met [Active Directory verificatie en autorisatie](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) kan identiteits beheer van database gebruikers en andere micro soft-Services op één centrale locatie worden beheerd.
-   Met [SQL database controle](../../sql-database/sql-database-auditing.md) worden database gebeurtenissen bijgehouden en naar een audit logboek in een Azure-opslag account geschreven.
-   SQL Database is geconfigureerd voor het gebruik van [transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql). De data base, gekoppelde back-ups en transactie logboek bestanden worden in realtime versleuteld en ontsleuteld om informatie op rest te beveiligen. Transparent Data Encryption biedt zekerheid dat opgeslagen gegevens niet zijn onderworpen aan onbevoegde toegang.
-   [Firewall regels](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor komen dat alle toegang tot database servers tot de juiste machtigingen worden verleend. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt.
-   Met de [SQL-bedreigings detectie](../../sql-database/sql-database-threat-detection.md) kan de detectie en het antwoord op mogelijke dreigingen worden uitgevoerd. Het biedt beveiligings waarschuwingen voor verdachte database activiteiten, potentiële kwetsbaar heden, SQL-injectie aanvallen en afwijkende patronen voor database toegang.
-   [Versleutelde kolommen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zorgen ervoor dat gevoelige gegevens nooit als tekst zonder opmaak in het database systeem worden weer gegeven. Nadat de gegevens versleuteling is ingeschakeld, hebben alleen client toepassingen of toepassings servers met toegang tot de sleutels toegang tot onbewerkte-tekst gegevens.
- [Dynamische gegevens maskering](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) beperkt de bloot stelling van gevoelige gegevens door de gegevens te maskeren voor niet-gemachtigde gebruikers of toepassingen. Er kunnen mogelijk gevoelige gegevens automatisch worden gedetecteerd en de juiste maskers worden voorgesteld om toe te passen. Dynamische gegevens maskering helpt de toegang te beperken, zodat gevoelige gegevens de data base niet sluiten via onbevoegde toegang. *Klanten zijn verantwoordelijk voor het aanpassen van de instellingen om te voldoen aan hun database schema.*

### <a name="identity-management"></a>Identiteitsbeheer
De volgende technologieën bieden mogelijkheden voor het beheren van toegang tot gegevens in de Azure-omgeving:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) is de multi tenant-Cloud Directory-en identiteits beheer service van micro soft. Alle gebruikers voor deze oplossing worden gemaakt in azure AD en bevatten gebruikers die toegang hebben tot de SQL database.
-   Verificatie voor de toepassing wordt uitgevoerd met behulp van Azure AD. Zie [toepassingen integreren met Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)voor meer informatie. De database kolom versleuteling maakt ook gebruik van Azure AD om de toepassing te verifiëren voor SQL Database. Zie voor meer informatie hoe u [gevoelige gegevens in SQL database kunt beveiligen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) kan door beheerders worden gebruikt voor het definiëren van verfijnde toegangs machtigingen. Hiermee kunnen ze alleen de hoeveelheid toegang verlenen die gebruikers nodig hebben om hun taken uit te voeren. In plaats van elke gebruiker onbeperkte toegang voor Azure-resources te geven, kunnen beheerders alleen bepaalde acties toestaan voor het openen van bronnen en gegevens. Abonnements toegang is beperkt tot de abonnements beheerder.
- [Azure Active Directory privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) kunnen door klanten worden gebruikt om het aantal gebruikers dat toegang tot bepaalde gegevens heeft, te minimaliseren. Beheerders kunnen Azure AD Privileged Identity Management gebruiken om bevoegde identiteiten en hun toegang tot bronnen te detecteren, beperken en controleren. Deze functie kan ook worden gebruikt voor het afdwingen van alleen-in-time-beheer toegang op aanvraag wanneer dit nodig is.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecteert mogelijke beveiligings problemen die van invloed zijn op de identiteiten van een organisatie. Hiermee configureert u automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op de identiteiten van een organisatie. Er worden ook verdachte incidenten onderzocht om de juiste maat regelen te nemen om deze op te lossen.

### <a name="security"></a>Beveiliging
**Beheer van geheimen**: De oplossing maakt gebruik van [Key Vault](https://azure.microsoft.com/services/key-vault/) voor het beheer van sleutels en geheimen. Key Vault helpt bij het beveiligen van cryptografische sleutels en geheimen die worden gebruikt door Cloud toepassingen en-services. Met de volgende Key Vault mogelijkheden kunnen klanten gegevens beveiligen:
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

**Azure-toepassing gateway**: De architectuur vermindert het risico op beveiligings problemen door gebruik te maken van een toepassings gateway met een Web Application Firewall geconfigureerd en dat de regel set OWASP is ingeschakeld. Aanvullende mogelijkheden zijn onder andere:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [SSL-offload](../../application-gateway/create-ssl-portal.md)inschakelen.
- Schakel [TLS v 1.0 en v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)uit.
- [Web Application firewall](../../application-gateway/waf-overview.md) (preventie modus).
- [Preventie modus](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) met OWASP 3,0-regelset.
- Schakel [logboek registratie van diagnostische gegevens](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)in.
- [Aangepaste status controles](../../application-gateway/quick-create-portal.md).
- [Security Center](https://azure.microsoft.com/services/security-center) en [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) bieden extra beveiliging en meldingen. Security Center biedt ook een reputatie systeem.

### <a name="logging-and-auditing"></a>Logboekregistratie en bewaking

Azure-Services registreren systeem-en gebruikers activiteiten uitvoerig, evenals systeem status:
- **Activiteiten logboeken**: [Activiteiten logboeken](../../azure-monitor/platform/activity-logs-overview.md) bieden inzicht in bewerkingen die worden uitgevoerd op resources in een abonnement. Activiteiten logboeken kunnen helpen bij het bepalen van de initiator, het tijdstip van de gebeurtenis en de status van een bewerking.
- **Diagnostische logboeken**: [Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md) bevatten alle logboeken die elke resource heeft verzonden. Deze logboeken bevatten Windows-gebeurtenis systeem logboeken, opslag logboeken, Key Vault controle logboeken en Application Gateway toegang en firewall Logboeken. Alle Diagnostische logboeken schrijven naar een gecentraliseerd en versleuteld Azure Storage-account voor archivering. Gebruikers kunnen de Bewaar periode tot 730 dagen configureren om te voldoen aan de specifieke vereisten.

**Azure monitor logboeken**: Logboeken worden samengevoegd in [Azure monitor logboeken](https://azure.microsoft.com/services/log-analytics/) voor verwerking, opslag en dashboard rapportage. Nadat de gegevens zijn verzameld, worden deze in verschillende tabellen ingedeeld voor elk gegevens type binnen Log Analytics werk ruimten. Op deze manier kunnen alle gegevens samen worden geanalyseerd, ongeacht de oorspronkelijke bron. Security Center integreert met Azure Monitor-Logboeken. Klanten kunnen Kusto-query's gebruiken om toegang te krijgen tot de gegevens van de beveiligings gebeurtenis en deze te combi neren met gegevens uit andere services.

De volgende Azure- [bewakings oplossingen](../../monitoring/monitoring-solutions.md) zijn opgenomen als onderdeel van deze architectuur:
-   [Active Directory beoordeling](../../azure-monitor/insights/ad-assessment.md): De Active Directory Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval. Het bevat een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [SQL-evaluatie](../../azure-monitor/insights/sql-assessment.md): De SQL Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval. Het biedt klanten een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [Status van agent](../../monitoring/monitoring-solution-agenthealth.md): De Status van agent oplossing meldt hoeveel agents er zijn geïmplementeerd en wat hun geografische distributie is. Ook wordt gerapporteerd hoeveel agents niet reageren en hoeveel agents operationele gegevens indienen.
-   [Analyse van activiteitenlogboek](../../azure-monitor/platform/collect-activity-logs.md): De Analyse van activiteitenlogboek oplossing helpt bij het analyseren van de activiteiten logboeken van Azure in alle Azure-abonnementen voor een klant.

**Azure Automation**: Met [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) worden runbooks opgeslagen, uitgevoerd en beheerd. In deze oplossing helpen runbooks bij het verzamelen van logboeken van SQL Database. Klanten kunnen de oplossing Automation [Wijzigingen bijhouden](../../automation/change-tracking.md) gebruiken om eenvoudig wijzigingen in de omgeving te identificeren.

**Azure monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) helpt gebruikers bij het volgen van prestaties, het onderhouden van de beveiliging en het identificeren van trends. Organisaties kunnen deze gebruiken om gegevens te controleren, te maken en te archiveren. Ze kunnen ook API-aanroepen volgen in hun Azure-resources.

**Application Insights**: [Application Insights](../../azure-monitor/app/app-insights-overview.md) is een uitbreid bare service voor het beheer van toepassings prestaties voor webontwikkelaars op meerdere platforms. Application Insights detecteert prestatie afwijkingen. Klanten kunnen het gebruiken om de Live webtoepassing te bewaken. Application Insights bevat krachtige analyse hulpprogramma's om klanten te helpen bij het vaststellen van problemen en inzicht te krijgen in wat gebruikers met hun app doen. Het&#39;is ontworpen om klanten voortdurend te helpen de prestaties en bruikbaarheid te verbeteren.

## <a name="threat-model"></a>Bedreigings model

Het gegevens stroom diagram voor deze referentie architectuur is beschikbaar voor [downloaden](https://aka.ms/nist171-paaswa-tm) of kan hier worden gevonden. Dit model kan klanten helpen om inzicht te krijgen in de punten van potentieel risico in de systeem infrastructuur wanneer deze wijzigingen aanbrengen.

![PaaS-webtoepassing voor het NIST SP 800-171 Threat model](images/nist171-paaswa-threat-model.png "PaaS-webtoepassing voor het NIST SP 800-171 Threat model")

## <a name="compliance-documentation"></a>Documentatie voor naleving
In de matrix voor het [Azure-blauwdruk voor beveiliging en naleving-NIST sp 800-171-gebruikers verantwoordelijkheid](https://aka.ms/nist171-crm) worden alle beveiligings controles vermeld die zijn vereist voor het NIST SP 800-171. Deze matrix geeft aan of de implementatie van elk besturings element de verantwoordelijkheid is van micro soft, de klant of het gedeeld tussen de twee.

De [implementatie matrix voor PaaS Web Application Control van het Azure-blauwdruk voor beveiliging en naleving-NIST sp 800-171](https://aka.ms/nist171-paaswa-cim) bevat informatie over welke NIST SP 800-171-besturings elementen worden verholpen door de architectuur van de Paas-webtoepassing. Het bevat gedetailleerde beschrijvingen van de manier waarop de implementatie voldoet aan de vereisten van elk gedekte besturings element.

## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen
### <a name="vpn-and-expressroute"></a>VPN-en ExpressRoute
Een beveiligde VPN-tunnel of [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) moet worden geconfigureerd om een veilige verbinding tot stand te brengen met de resources die zijn geïmplementeerd als onderdeel van deze PaaS-referentie architectuur voor webtoepassingen. Door een VPN-of ExpressRoute in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

Als u een beveiligde VPN-tunnel met Azure implementeert, kan een virtuele particuliere verbinding tussen een on-premises netwerk en een virtueel Azure-netwerk worden gemaakt. Deze verbinding vindt plaats via internet en stelt klanten in staat om ' tunnel-informatie veilig ' te maken binnen een versleutelde koppeling tussen het netwerk van de klant en Azure. Site-naar-site-VPN is een veilige, rijpere technologie die is geïmplementeerd door ondernemingen van elke omvang voor tien tallen. De [IPSec-tunnel modus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wordt in deze optie als een versleutelings mechanisme gebruikt.

Omdat verkeer in de VPN-tunnel via internet met een site-naar-site-VPN passeert, biedt micro soft nog een nog veiliger verbindings optie. ExpressRoute is een specifieke WAN-verbinding tussen Azure en een on-premises locatie of een Exchange-hosting provider. ExpressRoute-verbindingen maken rechtstreeks verbinding met de telecommunicatie provider van een klant. Als gevolg hiervan worden de gegevens niet via internet verzonden en niet beschikbaar gemaakt. Deze verbindingen bieden meer betrouw baarheid, hogere snelheden, lagere wacht tijden en hogere beveiliging dan typische verbindingen.

Aanbevolen procedures voor het implementeren van een veilig hybride netwerk dat een on-premises netwerk uitbreidt naar Azure, zijn [beschikbaar](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Vrijwaring

 - Dit document is alleen ter informatie bedoeld. MICRO SOFT BIEDT GEEN ENKELE GARANTIE, UITDRUKKELIJK, IMPLICIET OF WETTELIJK, MET BETREKKING TOT DE INFORMATIE IN DIT DOCUMENT. Dit document wordt in de as-is opgenomen. Informatie en weer gaven in dit document, inclusief URL'S en andere website verwijzingen, kunnen zonder kennisgeving worden gewijzigd. Klanten die dit document lezen, hebben het risico van het gebruik ervan.
 - Dit document biedt klanten geen juridische rechten voor intellectueel eigendom in een micro soft-product of-oplossingen.
 - Klanten kunnen dit document kopiëren en gebruiken voor interne referentie doeleinden.
 - Bepaalde aanbevelingen in dit document kunnen leiden tot meer gegevens-, netwerk-of COMPUTE-resource gebruik in azure, en kunnen de kosten van de Azure-licentie of het abonnement van de klant verhogen.
 - Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.
 - Dit document is ontwikkeld als referentie en mag niet worden gebruikt voor het definiëren van de manier waarop een klant kan voldoen aan specifieke nalevings vereisten en-voor Schriften. Klanten moeten juridische ondersteuning van hun organisatie zoeken op goedgekeurde klant implementaties.
