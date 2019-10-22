---
title: Azure-blauwdruk voor beveiliging en naleving-IaaS-webtoepassing voor UK NHS
description: Azure-blauwdruk voor beveiliging en naleving-IaaS-webtoepassing voor UK NHS
services: security
author: jomolesk
ms.assetid: 6081bab2-315a-4af4-92a1-7c773f449d66
ms.service: security
ms.topic: article
ms.date: 06/15/2018
ms.author: jomolesk
ms.openlocfilehash: 9b0478b3e72a759186d7d18ce6f7a885a1098d4b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "71259511"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-uk-nhs"></a>Azure-blauwdruk voor beveiliging en naleving: IaaS-webtoepassing voor UK NHS

## <a name="overview"></a>Overzicht

Deze Azure-blauwdruk voor beveiliging en naleving biedt een referentie architectuur en richt lijnen voor een IaaS-webtoepassing (Infrastructure as a Service) die geschikt is voor het verzamelen, opslaan en ophalen van gezondheids gegevens. Deze oplossing laat zien hoe klanten kunnen voldoen aan de richt lijnen die zijn opgenomen in de [hand leiding voor de Cloud beveiliging](https://digital.nhs.uk/data-and-information/looking-after-information/data-security-and-information-governance/nhs-and-social-care-data-off-shoring-and-the-use-of-public-cloud-services/health-and-social-care-cloud-security-good-practice-guide) die wordt gepubliceerd door [NHS Digital](https://digital.nhs.uk/), een partner van het Verenigd Konink rijk (UK) Department of Health and Social Care (DHSC). De hand leiding Cloud Security good practices is gebaseerd op de 14 [beveiligings principes](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) voor de cloud die worden gepubliceerd door het UK National Cyber Security Center (NCSC).

Deze referentie architectuur, implementatie handleiding en bedreigings model zijn bedoeld om te fungeren als basis voor klanten om aan hun specifieke behoeften aan te passen en mogen niet worden gebruikt als-in een productie omgeving zonder extra configuratie. Klanten zijn verantwoordelijk voor het uitvoeren van passende beveiligings-en nalevings beoordelingen van alle oplossingen die zijn gebouwd met behulp van deze architectuur, aangezien de vereisten kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

## <a name="architecture-diagram-and-components"></a>Architectuur diagram en onderdelen

Met deze oplossing wordt een referentie architectuur voor een IaaS-webtoepassing met een SQL Server back-end geïmplementeerd. De architectuur bevat een weblaag, gegevenslaag, Active Directory-infra structuur, Application Gateway en Load Balancer. Virtuele machines die zijn geïmplementeerd op de web-en gegevenslaagsen, worden geconfigureerd in een beschikbaarheidsset en SQL Server exemplaren worden geconfigureerd in een AlwaysOn-beschikbaarheids groep voor hoge Beschik baarheid. Virtuele machines zijn lid van een domein en Active Directory groeps beleid wordt gebruikt voor het afdwingen van beveiligings-en nalevings configuraties op het niveau van het besturings systeem.

De oplossing maakt gebruik van Azure Storage accounts, die klanten kunnen configureren om Storage Service Encryption te gebruiken om de vertrouwelijkheid van gegevens in rust te houden. In Azure worden drie kopieën van gegevens in het Data Center van de klant voor tolerantie opgeslagen. Geografisch redundante opslag zorgt ervoor dat gegevens worden gerepliceerd naar een secundair Data Center honderden kilo meters en worden opgeslagen als drie kopieën binnen dat Data Center, waardoor een nadelige gebeurtenis op het primaire Data Center van de klant wordt voor komen, wat leidt tot een verlies van gegevens.

Voor een betere beveiliging worden alle resources in deze oplossing als resource groep beheerd via Azure Resource Manager. Azure Active Directory op rollen gebaseerd toegangs beheer wordt gebruikt voor het beheren van de toegang tot geïmplementeerde resources, inclusief de bijbehorende sleutels in Azure Key Vault. Systeem status wordt bewaakt via Azure Security Center en Azure Monitor. Klanten configureren beide bewakings Services voor het vastleggen van Logboeken en het weer geven van de systeem status in één eenvoudig navigeerbaar-dash board. Azure-toepassing gateway is geconfigureerd als een firewall in preventie modus en geen verkeer dat niet TLSv 1.2 is toegestaan.

Een bastion-host van beheer biedt een beveiligde verbinding voor beheerders om toegang te krijgen tot geïmplementeerde bronnen. **U wordt aangeraden een VPN-of ExpressRoute-verbinding te configureren voor beheer en het importeren van gegevens in het subnet met referentie architectuur.**

![IaaS-webtoepassing voor UK NHS-referentie architectuur diagram](images/uknhs-iaaswa-architecture.png?raw=true "IaaS-webtoepassing voor UK NHS-referentie architectuur diagram")

Deze oplossing maakt gebruik van de volgende Azure-Services. Details van de implementatie architectuur vindt u in de sectie [implementatie architectuur](#deployment-architecture) .

- Azure Virtual Machines
    - (1) beheer/Bastion (Windows Server 2016 Data Center)
    - (2) Active Directory domein controller (Windows Server 2016 Data Center)
    - (2) SQL Server cluster knooppunt (SQL Server 2017 op Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Data Center)
- Beschikbaarheidssets
    - (1) Active Directory domein controllers
    - (1) SQL-cluster knooppunten
    - (1) Web/IIS
- Virtual Network van Azure
    - (1)/16 netwerk
    - (5)/24 netwerken
    - (5) netwerk beveiligings groep
    - Recovery Services kluis
- Azure Application Gateway
    - (1) Web Application firewall
        - Firewall modus: voor komen
        - Regel instellingen: OWASP 3,0
        - Listener-poort: 443
- Azure Active Directory
- Azure-Cloudwitness
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Beveiligingscentrum
- Azure Automation
- Azure Storage
    - (7) geografisch redundante opslag accounts

## <a name="deployment-architecture"></a>Implementatie architectuur

De volgende sectie bevat informatie over de implementatie-en implementatie-elementen.

**Bastion-host**: de bastion-host is het enige toegangs punt dat gebruikers in staat stelt om toegang te krijgen tot de geïmplementeerde resources in deze omgeving. De bastion-host biedt een beveiligde verbinding met geïmplementeerde bronnen door alleen extern verkeer van open bare IP-adressen in een veilige lijst toe te staan. Als u RDP-verkeer (extern bureau blad) wilt toestaan, moet de bron van het verkeer worden gedefinieerd in de netwerk beveiligings groep.

Met deze oplossing maakt u een virtuele machine als een aan een domein gekoppelde bastion-host met de volgende configuraties:
-   [Uitbrei ding voor antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure Diagnostics extensie](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) Azure Key Vault gebruiken
-   Een [beleid voor automatisch afsluiten](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) om het verbruik van resources van de virtuele machine te verminderen wanneer het niet wordt gebruikt
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) is ingeschakeld zodat referenties en andere geheimen worden uitgevoerd in een beveiligde omgeving die is geïsoleerd van het actieve besturings systeem

### <a name="virtual-network"></a>Virtueel netwerk

De architectuur definieert een particulier virtueel netwerk met een adres ruimte van 10.200.0.0/16.

**Netwerk beveiligings groepen**: met deze oplossing worden bronnen geïmplementeerd in een architectuur met een afzonderlijk websubnet, een Data Base-subnet, Active Directory subnet en het beheer subnet in een virtueel netwerk. Subnetten worden logisch gescheiden door de regels voor de netwerk beveiligings groep die worden toegepast op de afzonderlijke subnetten om het verkeer tussen subnetten alleen te beperken tot de gegevens die nodig zijn voor systeem-en beheer functionaliteit.

Zie de configuratie voor [netwerk beveiligings groepen](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) die met deze oplossing zijn geïmplementeerd. Organisaties kunnen netwerk beveiligings groepen configureren door het bovenstaande bestand te bewerken met behulp van [deze documentatie](../../virtual-network/virtual-network-vnet-plan-design-arm.md) als richt lijn.

Elk van de subnetten heeft een toegewezen netwerk beveiligings groep:
- 1 netwerk beveiligings groep voor Application Gateway (LBNSG)
- 1 netwerk beveiligings groep voor Bastion-host (MGTNSG)
- 1 netwerk beveiligings groep voor primaire en back-updomein controllers (ADNSG)
- 1 netwerk beveiligings groep voor SQL-servers en Cloud-Witness (SQLNSG)
- 1 netwerk beveiligings groep voor de weblaag (WEBNSG)

### <a name="data-in-transit"></a>Actieve gegevens
Azure versleutelt standaard alle communicatie naar en van Azure-data centers. Daarnaast worden alle trans acties Azure Storage via de Azure Portal uitgevoerd via HTTPS.

### <a name="data-at-rest"></a>Inactieve gegevens

De architectuur beveiligt gegevens op rest door versleuteling, database controle en andere metingen.

**Azure Storage**: om aan de rest-vereisten te voldoen aan versleutelde gegevens, gebruikt alle [Azure Storage](https://azure.microsoft.com/services/storage/) [Storage service Encryption](../../storage/common/storage-service-encryption.md). Dit helpt bij het beschermen en beschermen van gegevens ter ondersteuning van organisatie beveiligings verplichtingen en nalevings vereisten die zijn gedefinieerd door NHS Digital.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) maakt gebruik van de BitLocker-functie van Windows om volume versleuteling voor gegevens schijven te bieden. De oplossing kan worden geïntegreerd met Azure Key Vault om de versleutelings sleutels voor de schijf te controleren en te beheren.

**Azure SQL database**: de Azure SQL database instantie gebruikt de volgende data base Security-maat eenheden:

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

**Geheimen beheer**: de oplossing maakt gebruik van [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) voor het beheer van sleutels en geheimen. Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Met de volgende Azure Key Vault mogelijkheden kunnen klanten dergelijke gegevens beveiligen en benaderen:

- Geavanceerd toegangs beleid wordt geconfigureerd op basis van behoefte.
- Key Vault toegangs beleid wordt gedefinieerd met mini maal vereiste machtigingen voor sleutels en geheimen.
- Alle sleutels en geheimen in Key Vault hebben verloop datums.
- Alle sleutels in Key Vault worden beveiligd door gespecialiseerde hardware security modules. Het sleutel type is een met Hardware Security module beveiligde 2048-bits RSA-sleutel.
- Aan alle gebruikers en identiteiten worden mini maal vereiste machtigingen verleend met toegangs beheer op basis van rollen.
- Diagnostische logboeken voor Key Vault zijn ingeschakeld met een Bewaar periode van ten minste 365 dagen.
- Toegestane cryptografische bewerkingen voor sleutels zijn beperkt tot de vereisten die nodig zijn.
- De oplossing is geïntegreerd met Azure Key Vault voor het beheren van IaaS en geheimen voor de schijf versleutelings sleutels van de virtuele machine.

**Patch Management**: virtuele Windows-machines die zijn geïmplementeerd als onderdeel van deze referentie architectuur, worden standaard geconfigureerd voor het ontvangen van automatische updates van Windows Update-service. Deze oplossing omvat ook de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) -service waarmee bijgewerkte implementaties kunnen worden gemaakt om virtuele machines te patchen wanneer dat nodig is.

**Bescherming tegen schadelijke software**: [micro soft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) voor virtual machines biedt real-time beschermings mogelijkheden waarmee u virussen, spyware en andere schadelijke software kunt identificeren en verwijderen, met Configureer bare waarschuwingen wanneer bekende kwaad aardige of ongewenste software probeert te installeren of uit te voeren op beveiligde virtuele machines.

**Azure Security Center**: met [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)kunnen klanten centraal beveiligings beleid Toep assen en beheren voor werk belastingen, bloot stelling aan bedreigingen beperken en aanvallen detecteren en erop reageren. Daarnaast Azure Security Center de bestaande configuraties van Azure-Services openen om aanbevelingen voor de configuratie en service te bieden voor het verbeteren van de beveiliging postuur en het beveiligen van gegevens.

Azure Security Center maakt gebruik van diverse detectie mogelijkheden om klanten te waarschuwen over mogelijke aanvallen die zijn gericht op hun omgeving. Deze waarschuwingen bevatten waardevolle informatie over de trigger van de waarschuwing, de betrokken resources en de bron van de aanval. Azure Security Center heeft een reeks [vooraf gedefinieerde beveiligings waarschuwingen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), die worden geactiveerd wanneer een bedreiging of verdachte activiteit plaatsvindt. Met [aangepaste waarschuwings regels](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) in azure Security Center kunnen klanten nieuwe beveiligings waarschuwingen definiëren op basis van gegevens die al zijn verzameld uit hun omgeving.

Azure Security Center biedt beveiligings waarschuwingen en-incidenten met prioriteit, waardoor klanten eenvoudiger mogelijke beveiligings problemen kunnen detecteren en oplossen. Er wordt een [Threat Intelligence-rapport](https://docs.microsoft.com/azure/security-center/security-center-threat-report) voor elke gedetecteerde bedreiging gegenereerd om de incidenten te helpen bij het onderzoeken en oplossen van bedreigingen.

Daarnaast gebruikt deze referentie architectuur de evaluatie van [beveiligings problemen](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) in azure Security Center. Eenmaal geconfigureerd, rapporteert een partner agent (bijvoorbeeld Qualys) gegevens over beveiligings problemen naar het beheer platform van de partner. Het beheer platform van de partner biedt op zijn beurt gegevens over beveiligings problemen en status controle terug naar Azure Security Center, zodat klanten snel kwets bare virtuele machines kunnen identificeren.

**Azure-toepassing gateway**: de architectuur vermindert het risico op beveiligings problemen met behulp van een Azure-toepassing gateway waarvoor een Web Application firewall is geconfigureerd en de OWASP ruleset ingeschakeld. Aanvullende mogelijkheden zijn onder andere:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL-offload](../../application-gateway/create-ssl-portal.md) inschakelen
- [TLS v 1.0 en v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) uitschakelen
- [Web Application firewall](../../application-gateway/waf-overview.md) (preventie modus)
- [Preventie modus](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) met OWASP 3,0 ruleSet
- [Logboek registratie van diagnostische gegevens](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics) inschakelen
- [Aangepaste status tests](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) en [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) bieden extra beveiliging en meldingen. Azure Security Center biedt ook een reputatie systeem.

### <a name="business-continuity"></a>Bedrijfscontinuïteit

**Hoge Beschik baarheid**: de oplossing implementeert alle virtuele machines in een [beschikbaarheidsset](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Beschikbaarheids sets zorgen ervoor dat de virtuele machines over meerdere geïsoleerde hardwareconfiguraties worden gedistribueerd om de beschik baarheid te verbeteren. Er is ten minste één virtuele machine beschikbaar tijdens een geplande of niet-geplande onderhouds gebeurtenis die voldoet aan de 99,95% Azure SLA.

**Recovery Services kluis**: de [Recovery Services kluis](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) maakt back-upgegevens en beveiligt alle configuraties van Azure virtual machines in deze architectuur. Met een Recovery Services kluis kunnen klanten bestanden en mappen herstellen vanaf een virtuele IaaS-machine zonder de volledige virtuele machine te herstellen, waardoor de herstel tijd sneller verloopt.

**Cloudwitness**: [cloudwitness](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) is een type quorum-witness voor failoverclusters in Windows Server 2016 die gebruikmaken van Azure als arbitrage punt. De Cloudwitness, zoals elke andere quorumwitness, haalt een stem op en kan deel nemen aan de quorum berekeningen, maar maakt gebruik van de standaard openbaar beschik bare Azure-Blob Storage. Dit elimineert de extra onderhouds overhead van virtuele machines die worden gehost in een open bare Cloud.

### <a name="logging-and-auditing"></a>Logboekregistratie en bewaking

Azure-Services registreren systeem-en gebruikers activiteiten uitvoerig, evenals systeem status:
- **Activiteiten logboeken**: [activiteiten logboeken](../../azure-monitor/platform/activity-logs-overview.md) bieden inzicht in bewerkingen die worden uitgevoerd op resources in een abonnement. Activiteiten logboeken kunnen helpen bij het bepalen van de initiator, het tijdstip van de gebeurtenis en de status van een bewerking.
- **Diagnostische logboeken**: [Diagnostische](../../azure-monitor/platform/resource-logs-overview.md) logboeken bevatten alle logboeken die worden verzonden door elke resource. Deze logboeken bevatten Windows-gebeurtenis systeem logboeken, Azure Storage logboeken, Key Vault controle logboeken en Application Gateway toegang en firewall Logboeken. Alle Diagnostische logboeken schrijven naar een gecentraliseerd en versleuteld Azure Storage-account voor archivering. De retentie kan door de gebruiker worden geconfigureerd, tot 730 dagen, om te voldoen aan de specifieke vereisten voor het bewaren van een organisatie.

**Azure monitor logboeken**: deze logboeken worden samengevoegd in [Azure monitor logboeken](https://azure.microsoft.com/services/log-analytics/) voor het verwerken, opslaan en dashboard rapportage. Zodra de gegevens zijn verzameld, worden ze georganiseerd in aparte tabellen voor elk gegevenstype, zodat alle gegevens samen kunnen worden geanalyseerd, ongeacht de oorspronkelijke bron. Bovendien kan Azure Security Center worden geïntegreerd met Azure Monitor-logboeken, zodat klanten Kusto-query's kunnen gebruiken om toegang te krijgen tot de gegevens van de beveiligings gebeurtenis en deze te combi neren met gegevens uit andere services.

De volgende Azure- [bewakings oplossingen](../../monitoring/monitoring-solutions.md) zijn opgenomen als onderdeel van deze architectuur:
-   [Active Directory-evaluatie](../../azure-monitor/insights/ad-assessment.md): de Active Directory Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval en biedt een lijst met aanbevelingen die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [SQL-evaluatie](../../azure-monitor/insights/sql-assessment.md): de SQL Health Check-oplossing evalueert het risico en de status van server omgevingen met een regel matig interval en biedt klanten een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [Status van agent](../../monitoring/monitoring-solution-agenthealth.md): de status van agent oplossing rapporteert hoeveel agents er zijn geïmplementeerd en wat hun geografische distributie zijn, en hoeveel agents niet reageren en het aantal agents dat operationele gegevens verzendt.
-   [Analyse van activiteitenlogboek](../../azure-monitor/platform/collect-activity-logs.md): de analyse van activiteitenlogboek oplossing helpt bij het analyseren van de activiteiten logboeken van Azure in alle Azure-abonnementen voor een klant.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) runbooks opslaat, uitvoert en beheert. In deze oplossing helpen runbooks bij het verzamelen van logboeken van Azure SQL Database. Met de oplossing Automation [Wijzigingen bijhouden](../../automation/change-tracking.md) kunnen klanten eenvoudig wijzigingen in de omgeving identificeren.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) helpt gebruikers bij het volgen van prestaties, het onderhouden van de beveiliging en het identificeren van trends door organisaties in te scha kelen, waarschuwingen te maken en gegevens te archiveren, inclusief het volgen van API-aanroepen in hun Azure-resources.

## <a name="threat-model"></a>Bedreigings model

Het gegevensstroom diagram voor deze referentie architectuur is beschikbaar voor [downloaden](https://aka.ms/uknhs-iaaswa-tm) of kan hieronder worden weer gegeven. Dit model kan klanten helpen om inzicht te krijgen in de punten van potentieel risico in de systeem infrastructuur bij het aanbrengen van wijzigingen.

![IaaS-webtoepassing voor UK NHS Threat model](images/uknhs-iaaswa-threat-model.png?raw=true "IaaS-webtoepassing voor UK NHS Threat model")

## <a name="compliance-documentation"></a>Documentatie voor naleving

De [Azure-blauwdruk voor beveiliging en naleving-matrix van de klant verantwoordelijkheid voor UK NHS](https://aka.ms/uknhs-crm) bevat alle vereisten voor UK NHS. Deze matrix geeft aan of de implementatie van elk principe de verantwoordelijkheid is van micro soft, de klant of het gedeeld tussen de twee.

De [Azure-blauwdruk voor beveiliging en naleving – UK NHS IaaS Web Application implementation matrix](https://aka.ms/uknhs-iaaswa-cim) bevat informatie over welke UK NHS-vereisten worden verholpen door de architectuur van de IaaS-webtoepassing, met inbegrip van gedetailleerde beschrijvingen van de manier waarop de de implementatie voldoet aan de vereisten van elk gedekt principe.

## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen

### <a name="vpn-and-expressroute"></a>VPN-en ExpressRoute

Een beveiligde VPN-tunnel of [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) moet worden geconfigureerd om een veilige verbinding tot stand te brengen met de resources die zijn geïmplementeerd als onderdeel van deze IaaS-referentie architectuur voor webtoepassingen. Door een VPN-of ExpressRoute in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

Als u een beveiligde VPN-tunnel met Azure implementeert, kan een virtuele particuliere verbinding tussen een on-premises netwerk en een virtueel Azure-netwerk worden gemaakt. Deze verbinding vindt plaats via internet en stelt klanten in staat om &quot; informatie veilig te &quot;tunnel in een versleutelde&#39;koppeling tussen het netwerk van de klant en Azure. Site-naar-site-VPN is een veilige, rijpere technologie die is geïmplementeerd door ondernemingen van elke omvang voor tien tallen. De [IPSec-tunnel modus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wordt in deze optie als een versleutelings mechanisme gebruikt.

Omdat verkeer binnen de VPN-tunnel via internet met een site-naar-site-VPN gaat, biedt micro soft een andere, nog veiliger verbindings optie. Azure ExpressRoute is een specifieke WAN-verbinding tussen Azure en een on-premises locatie of een Exchange-hosting provider. Omdat ExpressRoute-verbindingen niet via internet werken, bieden deze verbindingen meer betrouw baarheid, hogere snelheden, lagere latenties en een betere beveiliging dan typische verbindingen via internet. Omdat dit een directe verbinding is tussen de telecommunicatie&#39;provider van de klant, worden de gegevens niet via internet verzonden en worden ze daarom niet blootgesteld aan de service.

Aanbevolen procedures voor het implementeren van een veilig hybride netwerk dat een on-premises netwerk uitbreidt naar Azure, zijn [beschikbaar](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Vrijwaring

- Dit document is alleen ter informatie bedoeld. MICRO SOFT BIEDT GEEN ENKELE GARANTIE, UITDRUKKELIJK, IMPLICIET OF WETTELIJK, MET BETREKKING TOT DE INFORMATIE IN DIT DOCUMENT. Dit document wordt in de as-is opgenomen. Informatie en weer gaven in dit document, inclusief URL'S en andere website verwijzingen, kunnen zonder kennisgeving worden gewijzigd. Klanten die dit document lezen, hebben het risico van het gebruik ervan.
- Dit document biedt klanten geen juridische rechten voor intellectueel eigendom in een micro soft-product of-oplossingen.
- Klanten kunnen dit document kopiëren en gebruiken voor interne referentie doeleinden.
- Bepaalde aanbevelingen in dit document kunnen leiden tot meer gegevens-, netwerk-of COMPUTE-resource gebruik in azure, en kunnen de kosten van de Azure-licentie of het abonnement van de klant verhogen.
- Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.
- Dit document is ontwikkeld als referentie en mag niet worden gebruikt voor het definiëren van de manier waarop een klant kan voldoen aan specifieke nalevings vereisten en-voor Schriften. Klanten moeten juridische ondersteuning van hun organisatie zoeken op goedgekeurde klant implementaties.
