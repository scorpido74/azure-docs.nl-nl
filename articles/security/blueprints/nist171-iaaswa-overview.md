---
title: Azure-blauwdruk voor beveiliging en naleving-IaaS-webtoepassing voor NIST SP 800-171
description: 'Azure-blauwdruk voor beveiliging en naleving-IaaS: Web Application NIST SP 800-171'
services: security
author: jomolesk
ms.assetid: 1f1ad27f-32c3-4e76-abb9-ea768d01747f
ms.service: security
ms.topic: article
ms.date: 07/31/2018
ms.author: jomolesk
ms.openlocfilehash: 83d368e419550f38c173a7a1dca42c84db7d542f
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259838"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-nist-sp-800-171"></a>Azure-blauwdruk voor beveiliging en naleving-IaaS-webtoepassing voor NIST SP 800-171

## <a name="overview"></a>Overzicht
De [800-171 speciale](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-171.pdf) cui van het NIST bevat richt lijnen voor het beveiligen van de beheerde niet-geclassificeerde informatie, die zich in niet-federale informatie systemen en organisaties bevindt. NIST SP 800-171 brengt 14 beveiligings vereisten tot stand voor het beschermen van de vertrouwelijkheid van CUI.

Deze Azure-blauwdruk voor beveiliging en naleving biedt hulp bij het implementeren van een architectuur voor webtoepassingen in azure waarmee een subset van de besturings elementen van het NIST-SP 800-171 wordt geïmplementeerd. Deze oplossing laat zien hoe klanten kunnen voldoen aan specifieke vereisten voor beveiliging en naleving. Het fungeert ook als basis voor klanten om hun eigen webtoepassingen te bouwen en te configureren in Azure.

Deze referentie architectuur, de bijbehorende implementatie handleiding en het bedreigings model zijn bedoeld om te fungeren als basis voor klanten om aan hun specifieke behoeften aan te passen. Ze mogen niet worden gebruikt als-in een productie omgeving. Het implementeren van deze architectuur zonder aanpassing is niet voldoende om volledig te voldoen aan de vereisten van NIST SP 800-171. Klanten zijn verantwoordelijk voor het uitvoeren van passende beveiligings-en nalevings beoordelingen van alle oplossingen die zijn gebouwd met behulp van deze architectuur. De vereisten kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

## <a name="architecture-diagram-and-components"></a>Architectuur diagram en onderdelen
Deze Azure-blauwdruk voor beveiliging en naleving implementeert een referentie architectuur voor een IaaS-webtoepassing met een SQL Server back-end. De architectuur bevat een weblaag, gegevenslaag, Active Directory-infra structuur, Azure-toepassing gateway en Azure Load Balancer. Virtuele machines (Vm's) die zijn geïmplementeerd op de web-en gegevens lagen, worden geconfigureerd in een beschikbaarheidsset. SQL Server exemplaren worden geconfigureerd in een always on-beschikbaarheids groep voor hoge Beschik baarheid. Vm's zijn lid van een domein. Active Directory groeps beleid dwingt u beveiligings-en nalevings configuraties af op het niveau van het besturings systeem.

De volledige oplossing is gebaseerd op Azure Storage, die klanten configureren van de Azure Portal. Opslag versleutelt alle gegevens met Storage Service Encryption om de vertrouwelijkheid van de gegevens in rust te behouden. Geografisch redundante opslag zorgt ervoor dat een nadelige gebeurtenis op het primaire Data Center van de klant geen verlies van gegevens oplevert. Een tweede kopie wordt opgeslagen op een afzonderlijke locatie, honderden kilo meters.

Voor een betere beveiliging worden alle resources in deze oplossing als resource groep beheerd via Azure Resource Manager. Op rollen gebaseerd toegangs beheer (RBAC) van Azure Active Directory (Azure AD) wordt gebruikt voor het beheren van de toegang tot geïmplementeerde resources en sleutels in Azure Key Vault. De systeem status wordt bewaakt via Azure Monitor. Klanten configureren beide bewakings Services voor het vastleggen van Logboeken. De systeem status wordt weer gegeven in één dash board dat eenvoudig te gebruiken is.

Een bastion-host van beheer biedt een beveiligde verbinding voor beheerders om toegang te krijgen tot geïmplementeerde bronnen. *Micro soft raadt u aan om een VPN-of Azure ExpressRoute-verbinding te configureren voor beheer en het importeren van gegevens in het subnet met referentie architectuur.*


![IaaS-webtoepassing voor NIST SP 800-171-referentie architectuur diagram](images/nist171-iaaswa-architecture.png "IaaS-webtoepassing voor NIST SP 800-171-referentie architectuur diagram")

Deze oplossing maakt gebruik van de volgende Azure-Services. Zie de sectie [implementatie architectuur](#deployment-architecture) voor meer informatie.

- Azure Virtual Machines
    - (1) beheer/Bastion (Windows Server 2016 Data Center)
    - (2) Active Directory domein controller (Windows Server 2016 Data Center)
    - (2) SQL Server cluster knooppunt (SQL Server 2017 op Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Data Center)
- Azure Virtual Network
    - (1)/16 netwerk
    - (5)/24 netwerken
    - (5) netwerk beveiligings groepen
- Beschikbaarheidssets
    - (1) Active Directory domein controllers
    - (1) SQL-cluster knooppunten
    - (1) Web/IIS
- Azure Application Gateway
    - (1) Web Application firewall
        - Firewall modus: voor komen
        - Regelset: OWASP 3,0
        - Listener-poort: 443
- Azure Active Directory
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor (Logboeken)
- Azure Resource Manager
- Azure Security Center
- Azure Storage
- Azure Automation
- Cloudwitness
- Recovery Services-kluis

## <a name="deployment-architecture"></a>Implementatie architectuur
De volgende sectie bevat informatie over de implementatie-en implementatie-elementen.

**Bastion-host**: De bastion-host is het enige toegangs punt dat gebruikers kunnen gebruiken om toegang te krijgen tot de geïmplementeerde resources in deze omgeving. De bastion-host biedt een beveiligde verbinding met geïmplementeerde bronnen door alleen extern verkeer van open bare IP-adressen op een veilige lijst toe te staan. Om extern bureau blad-verkeer toe te staan, moet de bron van het verkeer worden gedefinieerd in de netwerk beveiligings groep (NSG).

Met deze oplossing wordt een VM gemaakt als een aan een domein gekoppelde bastion-host met de volgende configuraties:
-   [Uitbrei ding van antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware).
-   [Azure Diagnostics extensie](../../virtual-machines/windows/extensions-diagnostics-template.md).
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) met behulp van Key Vault.
-   Een [beleid voor automatisch afsluiten](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) om het verbruik van VM-resources te verminderen wanneer het niet wordt gebruikt.
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) is ingeschakeld, zodat referenties en andere geheimen worden uitgevoerd in een beveiligde omgeving die is geïsoleerd van het actieve besturings systeem.

### <a name="virtual-network"></a>Virtueel netwerk
De architectuur definieert een particulier virtueel netwerk met een adres ruimte van 10.200.0.0/16.

**Netwerk beveiligings groepen**: Met deze oplossing worden bronnen geïmplementeerd in een architectuur met afzonderlijke subnetten voor web, Data Base, Active Directory en beheer binnen een virtueel netwerk. Subnetten worden logisch gescheiden door NSG-regels die worden toegepast op de afzonderlijke subnetten. De regels beperken het verkeer tussen subnetten alleen naar wat nodig is voor systeem-en beheer functionaliteit.

Zie de configuratie voor [nsg's](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) die met deze oplossing is geïmplementeerd. Organisaties kunnen Nsg's configureren door het vorige bestand te bewerken door [deze documentatie](../../virtual-network/virtual-network-vnet-plan-design-arm.md) als richt lijn te gebruiken.

Elk subnet heeft een toegewezen NSG:
- Eén NSG voor Application Gateway (LBNSG)
- Eén NSG voor Bastion-host (MGTNSG)
- Eén NSG voor primaire en back-updomein controllers (ADNSG)
- Eén NSG voor SQL-servers en Cloud-Witness (SQLNSG)
- Eén NSG voor de weblaag (WEBNSG)

### <a name="data-in-transit"></a>Actieve gegevens
Azure versleutelt standaard alle communicatie naar en van Azure-data centers. Daarnaast worden alle trans acties voor opslag via de Azure Portal uitgevoerd via HTTPS.

### <a name="data-at-rest"></a>Data-at-rest
De architectuur beveiligt gegevens in rust door meerdere metingen. Deze metingen omvatten versleuteling en database controle.

**Azure Storage**: Om te voldoen aan de vereisten voor versleutelde gegevens in rust, gebruikt alle [opslag](https://azure.microsoft.com/services/storage/) [Storage service Encryption](../../storage/common/storage-service-encryption.md). Deze functie helpt bij het beschermen en beschermen van gegevens ter ondersteuning van organisatie beveiligings verplichtingen en nalevings vereisten die zijn gedefinieerd in het NIST SP 800-171.

**Azure Disk Encryption**: Schijf versleuteling wordt gebruikt voor versleutelde Windows IaaS VM-schijven. [Schijf versleuteling](../azure-security-disk-encryption-overview.md) maakt gebruik van de BitLocker-functie van Windows om volume versleuteling te bieden voor besturings systeem en gegevens schijven. De oplossing is geïntegreerd met Key Vault om de versleutelings sleutels van de schijf te controleren en te beheren.

**SQL Server**: Het SQL Server exemplaar maakt gebruik van de volgende data base Security-maat eenheden:
-   Met [SQL Server controle](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) worden database gebeurtenissen bijgehouden en naar audit logboeken geschreven.
-   [Transparante gegevens versleuteling](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) voert realtime versleuteling en ontsleuteling van de data base, gekoppelde back-ups en transactie logboek bestanden uit om informatie te beveiligen. Transparent Data Encryption biedt zekerheid dat opgeslagen gegevens niet zijn onderworpen aan onbevoegde toegang.
-   [Firewall regels](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor komen dat alle toegang tot database servers tot de juiste machtigingen worden verleend. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt.
-   [Versleutelde kolommen](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) zorgen ervoor dat gevoelige gegevens nooit als tekst zonder opmaak in het database systeem worden weer gegeven. Nadat de gegevens versleuteling is ingeschakeld, hebben alleen client toepassingen of toepassings servers met toegang tot de sleutels toegang tot onbewerkte-tekst gegevens.
- [Dynamische gegevens maskering](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) beperkt de bloot stelling van gevoelige gegevens door de gegevens te maskeren voor niet-gemachtigde gebruikers of toepassingen. Er kunnen mogelijk gevoelige gegevens automatisch worden gedetecteerd en de juiste maskers worden voorgesteld om toe te passen. Dynamische gegevens maskering helpt de toegang te beperken, zodat gevoelige gegevens de data base niet sluiten via onbevoegde toegang. *Klanten zijn verantwoordelijk voor het aanpassen van de instellingen om te voldoen aan hun database schema.*

### <a name="identity-management"></a>Identiteitsbeheer
De volgende technologieën bieden mogelijkheden voor het beheren van toegang tot gegevens in de Azure-omgeving:
-   [Azure AD](https://azure.microsoft.com/services/active-directory/) is de multi tenant-Cloud Directory-en identiteits beheer service van micro soft. Alle gebruikers voor deze oplossing worden gemaakt in azure AD en bevatten de gebruikers die toegang hebben tot de SQL Server-instantie.
-   Verificatie voor de toepassing wordt uitgevoerd met behulp van Azure AD. Zie [toepassingen integreren met Azure AD](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)voor meer informatie.
-   [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) kan door beheerders worden gebruikt om nauw keurige toegangs machtigingen te definiëren om alleen de hoeveelheid toegang te verlenen die gebruikers nodig hebben om hun taken uit te voeren. In plaats van elke gebruiker onbeperkte machtigingen voor Azure-resources geven, kunnen beheerders alleen bepaalde acties toestaan voor toegang tot gegevens. Abonnements toegang is beperkt tot de abonnements beheerder.
- [Azure Active Directory privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) kunnen door klanten worden gebruikt om het aantal gebruikers dat toegang tot bepaalde resources heeft, te minimaliseren. Beheerders kunnen Azure AD Privileged Identity Management gebruiken om bevoegde identiteiten en hun toegang tot bronnen te detecteren, beperken en controleren. Deze functie kan ook worden gebruikt voor het afdwingen van alleen-in-time-beheer toegang op aanvraag wanneer dit nodig is.
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
- De oplossing is geïntegreerd met Key Vault voor het beheren van IaaS en geheimen voor de schijf versleutelings sleutels van de virtuele machine.

**Patch beheer**: Windows-Vm's die zijn geïmplementeerd als onderdeel van deze referentie architectuur, worden standaard geconfigureerd voor het ontvangen van automatische updates van Windows Update-service. Deze oplossing omvat ook de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) -service waarmee bijgewerkte implementaties kunnen worden gemaakt om zo nodig vm's te patchen.

**Bescherming tegen schadelijke software**: [Micro soft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) voor vm's biedt real-time beschermings mogelijkheden waarmee u virussen, spyware en andere schadelijke software kunt identificeren en verwijderen. Klanten kunnen waarschuwingen configureren die genereren wanneer bekende of ongewenste software probeert te installeren of uit te voeren op beveiligde Vm's.

**Azure Security Center**: Met [Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)kunnen klanten centraal beveiligings beleid Toep assen en beheren voor werk belastingen, bloot stelling aan bedreigingen beperken en aanvallen detecteren en erop reageren. Security Center heeft ook toegang tot bestaande configuraties van Azure-Services om aanbevelingen voor de configuratie en service te bieden voor het verbeteren van de beveiliging postuur en het beschermen van gegevens.

Security Center maakt gebruik van diverse detectie mogelijkheden om klanten te waarschuwen over mogelijke aanvallen die gericht zijn op hun omgeving. Deze waarschuwingen bevatten waardevolle informatie over de trigger van de waarschuwing, de betrokken resources en de bron van de aanval. Security Center heeft een reeks [vooraf gedefinieerde beveiligings waarschuwingen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type) die worden geactiveerd wanneer een bedreiging of verdachte activiteit plaatsvindt. Klanten kunnen [aangepaste waarschuwings regels](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) gebruiken om nieuwe beveiligings waarschuwingen te definiëren op basis van gegevens die al zijn verzameld uit hun omgeving.

Security Center biedt prioriteiten voor beveiligings waarschuwingen en incidenten. Security Center maakt het eenvoudiger voor klanten om potentiële beveiligings problemen te detecteren en op te lossen. Er wordt een [Threat Intelligence-rapport](https://docs.microsoft.com/azure/security-center/security-center-threat-report) gegenereerd voor elke gedetecteerde bedreiging. Incident response teams kunnen de rapporten gebruiken bij het onderzoeken en oplossen van bedreigingen.

Deze referentie architectuur maakt gebruik van de functie voor [evaluatie van beveiligings problemen](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) in Security Center. Nadat deze is geconfigureerd, rapporteert een partner agent (bijvoorbeeld Qualys) gegevens over het beveiligings probleem in het beheer platform van de partner. Het beheerplatform van de partner rapporteert op zijn beurt weer controlegegevens over beveiligingsproblemen en status naar Security Center. Klanten kunnen deze informatie gebruiken om snel kwets bare Vm's te identificeren.

**Azure-toepassing gateway**: De architectuur vermindert het risico op beveiligings problemen door gebruik te maken van een toepassings gateway met een Web Application Firewall geconfigureerd en dat de regel set OWASP is ingeschakeld. Aanvullende mogelijkheden zijn onder andere:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell).
- [SSL-offload](../../application-gateway/create-ssl-portal.md)inschakelen.
- Schakel [TLS v 1.0 en v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)uit.
- [Web Application firewall](../../application-gateway/waf-overview.md) (preventie modus).
- [Preventie modus](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) met OWASP 3,0-regelset.
- Schakel [logboek registratie van diagnostische gegevens](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)in.
- [Aangepaste status controles](../../application-gateway/quick-create-portal.md).
- [Security Center](https://azure.microsoft.com/services/security-center) en [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) bieden extra beveiliging en meldingen. Security Center biedt ook een reputatie systeem.

### <a name="business-continuity"></a>Bedrijfscontinuïteit

**Hoge beschikbaarheid**: De oplossing implementeert alle virtuele machines in een [beschikbaarheidsset](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Beschikbaarheids sets zorgen ervoor dat de Vm's worden verdeeld over meerdere geïsoleerde hardwareconfiguraties om de beschik baarheid te verbeteren. Er is ten minste één virtuele machine beschikbaar tijdens een geplande of niet-geplande onderhouds gebeurtenis die voldoet aan de 99,95% Azure SLA.

**Recovery Services kluis**: De [Recovery Services kluis](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) maakt back-upgegevens en beveiligt alle configuraties van Azure virtual machines in deze architectuur. Met een Recovery Services kluis kunnen klanten bestanden en mappen herstellen vanaf een IaaS-VM zonder de hele virtuele machine te herstellen. Dit proces versnelt de herstel tijden.

**Cloudwitness**: [Cloud-Witness](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) is een type quorum-witness voor failoverclusters in Windows Server 2016 die Azure als arbitrage punt gebruikt. Cloud-Witness, zoals elke andere quorumwitness, haalt een stem op en kan deel nemen aan de quorum berekeningen. Hierbij wordt gebruikgemaakt van de standaard open bare Azure Blob-opslag. Deze regeling elimineert de extra onderhouds overhead van Vm's die worden gehost in een open bare Cloud.

### <a name="logging-and-auditing"></a>Logboekregistratie en bewaking

Azure-Services registreren systeem-en gebruikers activiteiten uitvoerig, evenals systeem status:
- **Activiteiten logboeken**: [Activiteiten logboeken](../../azure-monitor/platform/activity-logs-overview.md) bieden inzicht in bewerkingen die worden uitgevoerd op resources in een abonnement. Activiteiten logboeken kunnen helpen bij het bepalen van de initiator, het tijdstip van de gebeurtenis en de status van een bewerking.
- **Diagnostische logboeken**: [Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md) bevatten alle logboeken die elke resource heeft verzonden. Deze logboeken bevatten Windows-gebeurtenis systeem logboeken, opslag logboeken, Key Vault controle logboeken en Application Gateway toegang en firewall Logboeken. Alle Diagnostische logboeken schrijven naar een gecentraliseerd en versleuteld Azure Storage-account voor archivering. Gebruikers kunnen de Bewaar periode tot 730 dagen configureren om te voldoen aan de specifieke vereisten.

**Azure monitor logboeken**: Deze logboeken worden samengevoegd in [Azure monitor logboeken](https://azure.microsoft.com/services/log-analytics/) voor verwerking, opslag en dashboard rapportage. Nadat de gegevens zijn verzameld, worden deze in verschillende tabellen ingedeeld voor elk gegevens type binnen Log Analytics werk ruimten. Op deze manier kunnen alle gegevens samen worden geanalyseerd, ongeacht de oorspronkelijke bron. Security Center integreert met Azure Monitor-Logboeken. Klanten kunnen Kusto-query's gebruiken om toegang te krijgen tot de gegevens van de beveiligings gebeurtenis en deze te combi neren met gegevens uit andere services.

De volgende Azure- [bewakings oplossingen](../../monitoring/monitoring-solutions.md) zijn opgenomen als onderdeel van deze architectuur:
-   [Active Directory beoordeling](../../azure-monitor/insights/ad-assessment.md): De Active Directory Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval. Het bevat een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [SQL-evaluatie](../../azure-monitor/insights/sql-assessment.md): De SQL Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval. Het biedt klanten een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [Status van agent](../../monitoring/monitoring-solution-agenthealth.md): De Status van agent oplossing meldt hoeveel agents er zijn geïmplementeerd en wat hun geografische distributie is. Ook wordt gerapporteerd hoeveel agents niet reageren en hoeveel agents operationele gegevens indienen.
-   [Analyse van activiteitenlogboek](../../azure-monitor/platform/collect-activity-logs.md): De Analyse van activiteitenlogboek oplossing helpt bij het analyseren van de activiteiten logboeken van Azure in alle Azure-abonnementen voor een klant.

**Azure Automation**: Met [Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) worden runbooks opgeslagen, uitgevoerd en beheerd. In deze oplossing helpen runbooks bij het verzamelen van logboeken van SQL Server. Klanten kunnen de oplossing Automation [Wijzigingen bijhouden](../../automation/change-tracking.md) gebruiken om eenvoudig wijzigingen in de omgeving te identificeren.

**Azure monitor**: [Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) helpt gebruikers bij het volgen van prestaties, het onderhouden van de beveiliging en het identificeren van trends. Organisaties kunnen deze gebruiken om gegevens te controleren, te maken en te archiveren. Ze kunnen ook API-aanroepen volgen in hun Azure-resources.

## <a name="threat-model"></a>Bedreigings model

Het gegevens stroom diagram voor deze referentie architectuur is beschikbaar voor [downloaden](https://aka.ms/nist171-iaaswa-tm) of kan hier worden gevonden. Dit model kan klanten helpen om inzicht te krijgen in de punten van potentieel risico in de systeem infrastructuur wanneer deze wijzigingen aanbrengen.

![IaaS-webtoepassing voor het NIST SP 800-171 Threat model](images/nist171-iaaswa-threat-model.png "IaaS-webtoepassing voor het NIST SP 800-171 Threat model")

## <a name="compliance-documentation"></a>Documentatie voor naleving

In de matrix voor het [Azure-blauwdruk voor beveiliging en naleving-NIST sp 800-171-gebruikers verantwoordelijkheid](https://aka.ms/nist171-crm) worden alle beveiligings controles vermeld die zijn vereist voor het NIST SP 800-171. Deze matrix geeft aan of de implementatie van elk besturings element de verantwoordelijkheid is van micro soft, de klant of het gedeeld tussen de twee.

De [implementatie matrix voor IaaS Web Application Control van het Azure-blauwdruk voor beveiliging en naleving-NIST sp 800-171](https://aka.ms/nist171-iaaswa-cim) bevat informatie over welke NIST SP 800-171-besturings elementen worden verholpen door de architectuur van de IaaS-webtoepassing. Het bevat gedetailleerde beschrijvingen van de manier waarop de implementatie voldoet aan de vereisten van elk gedekte besturings element.

## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen
### <a name="vpn-and-expressroute"></a>VPN-en ExpressRoute
Een beveiligde VPN-tunnel of [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) moet worden geconfigureerd om een veilige verbinding tot stand te brengen met de resources die zijn geïmplementeerd als onderdeel van deze IaaS-referentie architectuur voor webtoepassingen. Door een VPN-of ExpressRoute in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

Door een beveiligde VPN-tunnel te implementeren met Azure, kan een virtuele particuliere verbinding tussen een on-premises netwerk en een Azure-Virtual Network worden gemaakt. Deze verbinding vindt plaats via internet. Klanten kunnen deze verbinding gebruiken voor het veilig ' tunnel ' binnen een versleutelde koppeling tussen het netwerk van de klant en Azure. Site-naar-site-VPN is een veilige, rijpere technologie die is geïmplementeerd door ondernemingen van elke omvang voor tien tallen. De [IPSec-tunnel modus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wordt in deze optie als een versleutelings mechanisme gebruikt.

Omdat verkeer in de VPN-tunnel via internet met een site-naar-site-VPN passeert, biedt micro soft nog een nog veiliger verbindings optie. ExpressRoute is een specifieke WAN-verbinding tussen Azure en een on-premises locatie of een Exchange-hosting provider. ExpressRoute-verbindingen worden rechtstreeks verbonden met de telecommunicatie provider van de klant. Als gevolg hiervan worden de gegevens niet via internet verzonden en niet beschikbaar gemaakt. Deze verbindingen bieden meer betrouw baarheid, hogere snelheden, lagere wacht tijden en hogere beveiliging dan typische verbindingen. 

Aanbevolen procedures voor het implementeren van een veilig hybride netwerk dat een on-premises netwerk uitbreidt naar Azure, zijn [beschikbaar](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Vrijwaring

- Dit document is alleen ter informatie bedoeld. MICRO SOFT BIEDT GEEN ENKELE GARANTIE, UITDRUKKELIJK, IMPLICIET OF WETTELIJK, MET BETREKKING TOT DE INFORMATIE IN DIT DOCUMENT. Dit document wordt in de as-is opgenomen. Informatie en weer gaven in dit document, inclusief URL'S en andere website verwijzingen, kunnen zonder kennisgeving worden gewijzigd. Klanten die dit document lezen, hebben het risico van het gebruik ervan. 
- Dit document biedt klanten geen juridische rechten voor intellectueel eigendom in een micro soft-product of-oplossingen. 
- Klanten kunnen dit document kopiëren en gebruiken voor interne referentie doeleinden. 
- Bepaalde aanbevelingen in dit document kunnen leiden tot meer gegevens-, netwerk-of COMPUTE-resource gebruik in azure, en kunnen de kosten van de Azure-licentie of het abonnement van de klant verhogen. 
- Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.
- Dit document is ontwikkeld als referentie en mag niet worden gebruikt voor het definiëren van de manier waarop een klant kan voldoen aan specifieke nalevings vereisten en-voor Schriften. Klanten moeten juridische ondersteuning van hun organisatie zoeken op goedgekeurde klant implementaties.
