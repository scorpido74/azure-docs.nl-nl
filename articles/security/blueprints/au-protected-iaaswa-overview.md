---
title: Azure-blauwdruk voor beveiliging en naleving-IaaS-webtoepassing voor Australië beveiligd
description: Azure-blauwdruk voor beveiliging en naleving-IaaS-webtoepassing voor Australië beveiligd
services: security
author: meladie
ms.assetid: f53a25c4-1c75-42d6-a0e7-a91661673891
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 5fe9cfe642585c4f5220d79813816e554fdf48ef
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259188"
---
# <a name="azure-security-and-compliance-blueprint---iaas-web-application-for-australia-protected"></a>Azure-blauwdruk voor beveiliging en naleving-IaaS-webtoepassing voor Australië beveiligd

## <a name="overview"></a>Overzicht
Deze Azure-blauwdruk voor beveiliging en naleving biedt richt lijnen voor de implementatie van een IaaS-omgeving (Infrastructure as a Service) die geschikt is voor het verzamelen, opslaan en ophalen van door AU beschermde overheids gegevens die voldoen aan de doel stellingen van de Australische Government Information Security-hand leiding (ISM), geproduceerd door het Australische signalen Directoraat (ASD). In deze blauw druk wordt een algemene referentie architectuur toegelicht en wordt de juiste verwerking van gevoelige overheids gegevens in een beveiligde, compatibele omgeving met meerdere lagen gedemonstreerd.

Deze referentie architectuur, implementatie handleiding en bedreigings model bieden een basis voor klanten om hun eigen plannings-en systeem accreditatie processen te kunnen uitvoeren, waardoor klanten werk belastingen op een op ASD gebaseerde manier kunnen implementeren naar Azure. Klanten kunnen ervoor kiezen om een Azure VPN Gateway of ExpressRoute te implementeren voor het gebruik van federatieve Services en om on-premises resources te integreren met Azure-resources. Klanten moeten rekening houden met de beveiligings implicaties van het gebruik van on-premises resources. Aanvullende configuratie is vereist om aan alle vereisten te voldoen, aangezien deze kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

Voor het bereiken van een ASD-compatibiliteit moet een informatie beveiliging geregistreerde beoordelaar het systeem controleren. Klanten zijn verantwoordelijk voor het uitvoeren van passende beveiligings-en nalevings beoordelingen van alle oplossingen die zijn gebouwd met behulp van deze architectuur, aangezien de vereisten kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

## <a name="architecture-diagram-and-components"></a>Architectuur diagram en onderdelen
Met deze oplossing wordt een referentie architectuur voor een IaaS-webtoepassing met een SQL Server back-end geïmplementeerd. De architectuur bevat een weblaag, gegevenslaag, Active Directory-infra structuur, Application Gateway en Load Balancer. Virtuele machines die zijn geïmplementeerd op de web-en gegevenslaagsen, worden geconfigureerd in een beschikbaarheidsset en SQL Server exemplaren worden geconfigureerd in een AlwaysOn-beschikbaarheids groep voor hoge Beschik baarheid. Virtuele machines zijn lid van een domein en Active Directory groeps beleid wordt gebruikt voor het afdwingen van beveiligings-en nalevings configuraties op het niveau van het besturings systeem. Een bastion-host van beheer biedt een beveiligde verbinding voor beheerders om toegang te krijgen tot geïmplementeerde bronnen.

De architectuur kan een beveiligde hybride omgeving bieden die een on-premises netwerk uitbreidt naar Azure, zodat op internet gebaseerde workloads veilig kunnen worden benaderd door zakelijke gebruikers van het particuliere lokale netwerk van een organisatie of via internet. Voor on-premises oplossingen is de klant beide verantwoordelijk voor alle aspecten van beveiliging, bewerkingen en naleving.

De Azure-resources die in deze oplossing zijn opgenomen, kunnen verbinding maken met een on-premises netwerk-of Data Center-faciliteit (bijvoorbeeld CDC in Canberra) via IPSec VPN met behulp van Azure VPN Gateway of via ExpressRoute. Het besluit om gebruik te maken van een VPN moet worden uitgevoerd met de classificatie van de verzonden gegevens en het netwerkpad. Klanten die grootschalige, bedrijfskritische workloads met big data vereisten uitvoeren, moeten een hybride netwerk architectuur overwegen met ExpressRoute voor de connectiviteit van particuliere netwerken met Azure-Services. Raadpleeg de sectie richt lijnen en aanbevelingen voor meer informatie over verbindings mechanismen voor Azure.

Federatie met Azure Active Directory moet worden gebruikt om gebruikers in staat te stellen te verifiëren met on-premises referenties en toegang te krijgen tot alle resources in de Cloud met behulp van een on-premises Active Directory Federation Services-infra structuur. Active Directory Federation Services kunnen eenvoudige, beveiligde identiteits Federatie en mogelijkheden voor eenmalige aanmelding voor het web bieden voor deze hybride omgeving. Raadpleeg het gedeelte richt lijnen en aanbevelingen voor meer informatie Azure Active Directory Setup.

De oplossing maakt gebruik van Azure Storage accounts, die klanten kunnen configureren om Storage Service Encryption te gebruiken om de vertrouwelijkheid van gegevens in rust te houden. In Azure worden drie kopieën van gegevens opgeslagen in de geselecteerde regio van een klant voor tolerantie. Azure-regio's worden geïmplementeerd in robuuste regio paren en geografisch redundante opslag zorgt ervoor dat gegevens worden gerepliceerd naar de tweede regio met drie kopieën. Dit voor komt dat een nadelige gebeurtenis op de primaire gegevens locatie van de klant leidt tot verlies van gegevens.

Voor een betere beveiliging worden alle resources in deze oplossing als resource groep beheerd via Azure Resource Manager. Azure Active Directory op rollen gebaseerd toegangs beheer wordt gebruikt voor het beheren van de toegang tot geïmplementeerde resources en sleutels in Azure Key Vault. Systeem status wordt bewaakt via Azure Security Center en Azure Monitor. Klanten configureren beide bewakings Services voor het vastleggen van Logboeken en het weer geven van de systeem status in één eenvoudig navigeerbaar-dash board. Azure-toepassing gateway is geconfigureerd als een firewall in preventie modus en vereist TLS-verkeer met een minimum versie van 1,2.

![IaaS-webtoepassing voor met au beveiligde referentie architectuur](images/au-protected-iaaswa-architecture.png?raw=true "IaaS-webtoepassing voor met au beveiligd referentie architectuur diagram") 

Deze oplossing maakt gebruik van de volgende Azure-Services. Meer informatie vindt u in de sectie [implementatie architectuur](#deployment-architecture) .

- Beschikbaarheidssets
    - (1) SQL-cluster knooppunten
    - (1) Web/IIS
- Azure Active Directory
- Azure Application Gateway
    - (1) Web Application firewall
        - Firewall modus: voor komen
        - Regelset: OWASP 3,0
        - Listener-poort: 443
- Azure-Cloudwitness
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure Monitor-logboeken
- Azure Storage
- Azure Virtual Machines
    - (1) beheer/Bastion (Windows Server 2016 Data Center)
    - (2) SQL Server cluster knooppunt (SQL Server 2017 op Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Data Center)
- Azure Virtual Network
    - (4) netwerk beveiligings groepen
    - Azure Network Watcher
- Recovery Services-kluis

Deze blauw druk bevat Azure-Services die niet zijn gecertificeerd voor gebruik in de beveiligde classificatie van het Australische Cyber Security Center (ACSC). Alle services die deel uitmaken van deze referentie architectuur, zijn gecertificeerd door ACSC op het niveau van de DLM (Leveling Limit markers). Micro soft raadt klanten aan de gepubliceerde beveiligings-en controle rapporten te bekijken die betrekking hebben op deze Azure-Services en hun risico beheer Framework te gebruiken om te bepalen of de Azure-service geschikt is voor hun interne erkenning en te gebruiken op het Beveiligde classificatie.

## <a name="deployment-architecture"></a>Implementatie architectuur
De volgende sectie bevat informatie over de implementatie-en implementatie-elementen.

**Bastion-host**: De bastion-host is het enige toegangs punt dat gebruikers in staat stelt om toegang te krijgen tot de geïmplementeerde resources in deze omgeving. De bastion-host biedt een beveiligde verbinding met geïmplementeerde bronnen door alleen extern verkeer van open bare IP-adressen in een veilige lijst toe te staan. Als u RDP-verkeer (extern bureau blad) wilt toestaan, moet de bron van het verkeer worden gedefinieerd in de netwerk beveiligings groep.

Met deze oplossing maakt u een virtuele machine als een aan een domein gekoppelde bastion-host met de volgende configuraties:
-   [Uitbrei ding voor antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure Diagnostics extensie](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) Azure Key Vault gebruiken
-   Een [beleid voor automatisch afsluiten](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) om het verbruik van resources van de virtuele machine te verminderen wanneer het niet wordt gebruikt

### <a name="virtual-network"></a>Virtueel netwerk
De architectuur definieert een particulier virtueel netwerk met een adres ruimte van 10.200.0.0/16.

**Netwerk beveiligings groepen**: Met deze oplossing worden bronnen geïmplementeerd in een architectuur met een afzonderlijk websubnet, een Data Base-subnet, Active Directory subnet en het beheer subnet in een virtueel netwerk. Subnetten worden logisch gescheiden door de regels voor de netwerk beveiligings groep die worden toegepast op de afzonderlijke subnetten om het verkeer tussen subnetten alleen te beperken tot de gegevens die nodig zijn voor systeem-en beheer functionaliteit.

Zie de configuratie voor [netwerk beveiligings groepen](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) die met deze oplossing zijn geïmplementeerd. Organisaties kunnen netwerk beveiligings groepen configureren door het bovenstaande bestand te bewerken met behulp van [deze documentatie](../../virtual-network/virtual-network-vnet-plan-design-arm.md) als richt lijn.

Elk van de subnetten heeft een toegewezen netwerk beveiligings groep:
- 1 netwerk beveiligings groep voor Application Gateway (LBNSG)
- 1 netwerk beveiligings groep voor Bastion-host (MGTNSG)
- 1 netwerk beveiligings groep voor SQL-servers en Cloud-Witness (SQLNSG)
- 1 netwerk beveiligings groep voor de weblaag (WEBNSG)

### <a name="data-in-transit"></a>Actieve gegevens
Azure versleutelt standaard alle communicatie naar en van Azure-data centers. 

Voor beveiligde gegevens onderweg van netwerken die eigendom zijn van de klant, gebruikt de architectuur het internet-of ExpressRoute met een VPN Gateway dat is geconfigureerd met IPSEC.

Daarnaast vinden alle trans acties naar Azure via de Azure-beheer portal plaats via HTTPS met behulp van TLS 1,2.

### <a name="data-at-rest"></a>Data-at-rest
De architectuur beveiligt gegevens op rest door versleuteling, database controle en andere metingen.

**Azure Storage**: Om te voldoen aan de versleutelde gegevens op rest-vereisten, gebruikt alle [Azure Storage](https://azure.microsoft.com/services/storage/) [Storage service Encryption](../../storage/common/storage-service-encryption.md). Dit helpt bij het beschermen en beschermen van gegevens ter ondersteuning van organisatie beveiligings verplichtingen en nalevings vereisten die zijn gedefinieerd door de Australische overheid ISM.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) maakt gebruik van de BitLocker-functie van Windows om volume versleuteling voor gegevens schijven te bieden. De oplossing kan worden geïntegreerd met Azure Key Vault om de versleutelings sleutels voor de schijf te controleren en te beheren.

**SQL Server**: Het SQL Server exemplaar maakt gebruik van de volgende data base Security-maat eenheden:
-   Met [SQL Server controle](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-2017) worden database gebeurtenissen bijgehouden en naar audit logboeken geschreven.
-   [Transparante gegevens versleuteling](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) voert realtime versleuteling en ontsleuteling van de data base, gekoppelde back-ups en transactie logboek bestanden uit om informatie te beveiligen. Transparent Data Encryption biedt zekerheid dat opgeslagen gegevens niet zijn onderworpen aan onbevoegde toegang.
-   [Firewall regels](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor komen dat alle toegang tot database servers tot de juiste machtigingen worden verleend. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt.
-   [Versleutelde kolommen](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-wizard?view=sql-server-2017) zorgen ervoor dat gevoelige gegevens nooit als tekst zonder opmaak in het database systeem worden weer gegeven. Na het inschakelen van gegevens versleuteling, hebben alleen client toepassingen of toepassings servers met toegang tot de sleutels toegang tot tekst zonder opmaak.
- [Dynamische gegevens maskering](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?view=sql-server-2017) beperkt de bloot stelling van gevoelige gegevens door de gegevens te maskeren voor niet-gemachtigde gebruikers of toepassingen. Met dynamische gegevens maskering kunnen mogelijk gevoelige gegevens automatisch worden gedetecteerd en worden de juiste maskers voorgesteld om te worden toegepast. Dit helpt bij het verminderen van de toegang, zodat gevoelige gegevens de data base niet afsluiten via onbevoegde toegang. **Klanten zijn verantwoordelijk voor het aanpassen van instellingen voor dynamische gegevens maskering om te voldoen aan hun database schema.**

### <a name="identity-management"></a>Identiteitsbeheer
Klanten kunnen gebruikmaken van on-premises Active Directory federatieve Services om met [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)te communiceren. Dit is de multi tenant-Cloud Directory en identiteits beheer service van micro soft. [Azure Active Directory Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) integreert on-premises directory's met Azure Active Directory. Voor alle gebruikers in deze oplossing zijn Azure Active Directory accounts vereist. Met aanmelden bij de Federatie kunnen gebruikers zich aanmelden bij Azure Active Directory en zich verifiëren bij Azure-resources met behulp van on-premises referenties.

Daarnaast kunt u met de volgende Azure Active Directory-functies de toegang tot gegevens in de Azure-omgeving beheren:
- Verificatie voor de toepassing wordt uitgevoerd met behulp van Azure Active Directory. Zie [toepassingen integreren met Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)voor meer informatie.
- Met op [rollen gebaseerd toegangs beheer van Azure](../../role-based-access-control/role-assignments-portal.md) kunnen beheerders verfijnde toegangs machtigingen definiëren om alleen de hoeveelheid toegang te verlenen die gebruikers nodig hebben om hun taken uit te voeren. In plaats van elke gebruiker onbeperkte machtiging voor Azure-resources geven, kunnen beheerders alleen bepaalde acties toestaan voor toegang tot gegevens. Abonnements toegang is beperkt tot de abonnements beheerder.
- Met [Azure Active Directory privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) kunnen klanten het aantal gebruikers dat toegang heeft tot bepaalde informatie minimaliseren. Beheerders kunnen Azure Active Directory Privileged Identity Management gebruiken om bevoegde identiteiten en hun toegang tot bronnen te detecteren, beperken en controleren. Deze functie kan ook worden gebruikt voor het afdwingen van alleen-in-time-beheer toegang op aanvraag als dat nodig is.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecteert mogelijke beveiligings problemen die&#39;van invloed zijn op een organisatie s-identiteiten, configureert automatische antwoorden op&#39;gedetecteerde verdachte acties die betrekking hebben op de identiteit van een organisatie en onderzoekt verdachte incidenten om deze problemen op te lossen.

**Azure multi-factor Authentication**: Als u identiteiten wilt beveiligen, moet multi-factor Authentication worden geïmplementeerd. [Azure multi-factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) is een gebruiks vriendelijke, schaal bare en betrouw bare oplossing die een tweede verificatie methode biedt om gebruikers te beschermen. Azure Multi-Factor Authentication maakt gebruik van de kracht van de Cloud en integreert met on-premises Active Directory en aangepaste toepassingen. Deze beveiliging wordt uitgebreid naar essentiële scenario's met een hoge omvang.

### <a name="security"></a>Beveiliging
**Beheer van geheimen**: De oplossing maakt gebruik van [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) voor het beheer van sleutels en geheimen. Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Met de volgende Azure Key Vault mogelijkheden kunnen klanten dergelijke gegevens beveiligen en benaderen:

- Geavanceerd toegangs beleid wordt geconfigureerd op basis van behoefte.
- Key Vault toegangs beleid wordt gedefinieerd met mini maal vereiste machtigingen voor sleutels en geheimen.
- Alle sleutels en geheimen in Key Vault hebben verloop datums.
- Alle sleutels in Key Vault worden beveiligd door gespecialiseerde hardware security modules. Het sleutel type is een met Hardware Security module beveiligde 2048-bits RSA-sleutel.
- Aan alle gebruikers en identiteiten worden mini maal vereiste machtigingen verleend met toegangs beheer op basis van rollen.
- Diagnostische logboeken voor Key Vault zijn ingeschakeld met een Bewaar periode van ten minste 365 dagen.
- Toegestane cryptografische bewerkingen voor sleutels zijn beperkt tot de vereisten die nodig zijn.
- De oplossing is geïntegreerd met Azure Key Vault voor het beheren van IaaS en geheimen voor de schijf versleutelings sleutels van de virtuele machine.

**Patch beheer**: Virtuele Windows-machines die zijn geïmplementeerd als onderdeel van deze referentie architectuur, worden standaard geconfigureerd voor het ontvangen van automatische updates van Windows Update-service. Deze oplossing omvat ook de [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro) -service waarmee bijgewerkte implementaties kunnen worden gemaakt om virtuele machines te patchen wanneer dat nodig is.

**Bescherming tegen schadelijke software**: [Micro soft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) voor virtual machines biedt realtime beschermings mogelijkheden waarmee u virussen, spyware en andere schadelijke software kunt identificeren en verwijderen, met Configureer bare waarschuwingen wanneer bekende of ongewenste software probeert om op beveiligde virtuele machines installeren of uitvoeren.

**Azure Security Center**: Met [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)kunnen klanten centraal beveiligings beleid Toep assen en beheren voor werk belastingen, bloot stelling aan bedreigingen beperken en aanvallen detecteren en erop reageren. Daarnaast Azure Security Center de bestaande configuraties van Azure-Services openen om aanbevelingen voor de configuratie en service te bieden voor het verbeteren van de beveiliging postuur en het beveiligen van gegevens.

Azure Security Center maakt gebruik van diverse detectie mogelijkheden om klanten te waarschuwen over mogelijke aanvallen die zijn gericht op hun omgeving. Deze waarschuwingen bevatten waardevolle informatie over de trigger van de waarschuwing, de betrokken resources en de bron van de aanval. Azure Security Center heeft een reeks [vooraf gedefinieerde beveiligings waarschuwingen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), die worden geactiveerd wanneer een bedreiging of verdachte activiteit plaatsvindt. Met [aangepaste waarschuwings regels](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) in azure Security Center kunnen klanten nieuwe beveiligings waarschuwingen definiëren op basis van gegevens die al zijn verzameld uit hun omgeving.

Azure Security Center biedt beveiligings waarschuwingen en-incidenten met prioriteit, waardoor klanten eenvoudiger mogelijke beveiligings problemen kunnen detecteren en oplossen. Er wordt een [Threat Intelligence-rapport](https://docs.microsoft.com/azure/security-center/security-center-threat-report) voor elke gedetecteerde bedreiging gegenereerd om de incidenten te helpen bij het onderzoeken en oplossen van bedreigingen.

Daarnaast gebruikt deze referentie architectuur de evaluatie van [beveiligings problemen](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations) in azure Security Center. Eenmaal geconfigureerd, rapporteert een partner agent (bijvoorbeeld Qualys) gegevens over beveiligings problemen naar het beheer platform van de partner. Het beheer platform van de partner biedt op zijn beurt gegevens over beveiligings problemen en status controle terug naar Azure Security Center, zodat klanten snel kwets bare virtuele machines kunnen identificeren.

**Azure-toepassing gateway**: De architectuur vermindert het risico op beveiligings problemen met behulp van een Azure-toepassing gateway waarvoor een Web Application Firewall is geconfigureerd en de ruleset OWASP ingeschakeld. Aanvullende mogelijkheden zijn onder andere:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL-offload](../../application-gateway/create-ssl-portal.md) inschakelen
- [TLS v 1.0 en v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) uitschakelen
- [Web Application firewall](../../application-gateway/waf-overview.md) (preventie modus)
- [Preventie modus](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) met OWASP 3,0 ruleSet
- [Logboek registratie van diagnostische gegevens](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics) inschakelen
- [Aangepaste status tests](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) en [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) bieden extra beveiliging en meldingen. Azure Security Center biedt ook een reputatie systeem.

### <a name="business-continuity"></a>Bedrijfscontinuïteit
**Hoge beschikbaarheid**: De oplossing implementeert alle virtuele machines in een [beschikbaarheidsset](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Beschikbaarheids sets zorgen ervoor dat de virtuele machines over meerdere geïsoleerde hardwareconfiguraties worden gedistribueerd om de beschik baarheid te verbeteren. Er is ten minste één virtuele machine beschikbaar tijdens een geplande of niet-geplande onderhouds gebeurtenis die voldoet aan de 99,95% Azure SLA.

**Recovery Services kluis**: De [Recovery Services kluis](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) maakt back-upgegevens en beveiligt alle configuraties van Azure virtual machines in deze architectuur. Met een Recovery Services kluis kunnen klanten bestanden en mappen herstellen vanaf een virtuele IaaS-machine zonder de volledige virtuele machine te herstellen, waardoor de herstel tijd sneller verloopt.

**Cloudwitness**: [Cloudwitness](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) is een type quorum-witness voor failoverclusters in Windows Server 2016 die Azure als arbitrage punt gebruikt. De Cloudwitness, zoals elke andere quorumwitness, haalt een stem op en kan deel nemen aan de quorum berekeningen, maar maakt gebruik van de standaard openbaar beschik bare Azure-Blob Storage. Dit elimineert de extra onderhouds overhead van virtuele machines die worden gehost in een open bare Cloud.

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

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) winkels, uitvoeren en beheren van runbooks. In deze oplossing helpen runbooks bij het verzamelen van logboeken van Azure SQL Server. Met de oplossing Automation [Wijzigingen bijhouden](../../automation/change-tracking.md) kunnen klanten eenvoudig wijzigingen in de omgeving identificeren.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) helpt gebruikers bij het volgen van prestaties, het onderhouden van de beveiliging en het identificeren van trends door organisaties in te scha kelen, waarschuwingen te maken en gegevens te archiveren, met inbegrip van tracking-API-aanroepen in hun Azure-resources.

[Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview): Azure Network Watcher biedt hulpprogramma's voor het controleren, diagnosticeren en weergeven van metrische gegevens en het in- of uitschakelen van logboekregistratie voor resources in een virtueel Azure-netwerk.  Gemenebests entiteiten moeten Network Watcher-stroom logboeken implementeren voor Nsg's en Virtual Machines. Deze logboeken moeten worden opgeslagen in een speciaal opslag account dat alleen beveiligings logboeken worden opgeslagen in en de toegang tot het opslag account moet worden beveiligd met op rollen gebaseerd toegangs beheer.

## <a name="threat-model"></a>Bedreigings model
Het gegevensstroom diagram voor deze referentie architectuur is beschikbaar voor [downloaden](https://aka.ms/au-protected-iaaswa-tm) of kan hieronder worden weer gegeven. Dit model kan klanten helpen om inzicht te krijgen in de punten van potentieel risico in de systeem infrastructuur bij het aanbrengen van wijzigingen.

![IaaS-webtoepassing voor met au beveiligd Threat model](images/au-protected-iaaswa-threat-model.png?raw=true "IaaS-webtoepassing voor au-beveiligd Threat model diagram")

## <a name="compliance-documentation"></a>Documentatie voor naleving
Deze nalevings documentatie wordt gemaakt door micro soft op basis van platformen en services die beschikbaar zijn via micro soft. Vanwege de grote verscheidenheid aan klant implementaties biedt deze documentatie een gegeneraliseerde aanpak voor een oplossing die alleen wordt gehost in de Azure-omgeving. Klanten kunnen alternatieve producten en services identificeren en gebruiken op basis van hun eigen bedrijfs omgevingen en bedrijfs resultaten. Klanten die on-premises Resources kiezen, moeten de beveiliging en bewerkingen voor die on-premises resources aanpakken. De gedocumenteerde oplossing kan worden aangepast door klanten om hun specifieke on-premises en beveiligings vereisten op te lossen.

De [matrix met door au beveiligde gebruikers verantwoordelijkheden bevat Azure-blauwdruk voor beveiliging en naleving](https://aka.ms/au-protected-crm) een lijst met alle beveiligings controles die vereist zijn voor met au beveiligd. Deze matrix geeft aan of de implementatie van elk besturings element de verantwoordelijkheid is van micro soft, de klant of het gedeeld tussen de twee.

De [Azure-blauwdruk voor beveiliging en naleving: door au beveiligde IaaS Web Application implementation matrix](https://aka.ms/au-protected-iaaswa-cim) bevat informatie over welke met au beveiligde besturings elementen worden verholpen door de architectuur van de IaaS-webtoepassing, inclusief gedetailleerde beschrijvingen van hoe de implementatie voldoet aan de vereisten van elk gedekte besturings element.

## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen
### <a name="vpn-and-expressroute"></a>VPN-en ExpressRoute
Voor geclassificeerde informatie moet een beveiligde IPSec VPN-tunnel worden geconfigureerd om een veilige verbinding te maken met de resources die zijn geïmplementeerd als onderdeel van deze IaaS-referentie architectuur voor webtoepassingen. Door een IPSec-VPN-verbinding in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

Door een beveiligde IPSec-VPN-tunnel te implementeren met Azure, kan een virtuele particuliere verbinding tussen een on-premises netwerk en een virtueel Azure-netwerk worden gemaakt. Deze verbinding kan via internet worden uitgevoerd en biedt klanten de mogelijkheid om ' tunnel-' gegevens in een versleutelde koppeling tussen het netwerk van de klant en Azure veilig te maken. Site-naar-site-VPN is een veilige, rijpere technologie die is geïmplementeerd door ondernemingen van elke omvang voor tien tallen. 

Omdat verkeer binnen de VPN-tunnel via internet met een site-naar-site-VPN gaat, biedt micro soft een optie voor een particuliere verbinding. Azure ExpressRoute is een speciale koppeling tussen Azure en een on-premises locatie of een Exchange-hosting provider en wordt beschouwd als een privé netwerk. Omdat ExpressRoute-verbindingen niet via internet werken, bieden deze verbindingen meer betrouw baarheid, hogere snelheden en lagere latenties dan typische verbindingen via internet. Omdat dit een directe verbinding is tussen de telecom municatie-provider van de klant, worden de gegevens niet via internet verzonden en worden ze daarom niet blootgesteld aan IT.

Aanbevolen procedures voor het implementeren van een veilig hybride netwerk dat een on-premises netwerk uitbreidt naar Azure, zijn [beschikbaar](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid). 

Om geclassificeerde gegevens te helpen beveiligen, ongeacht of u het internet of Azure ExpressRoute gebruikt, moeten klanten hun IPSec VPN configureren door de volgende instellingen toe te passen:

• De klant-VPN-initiator moet worden geconfigureerd voor een SA-levens duur van niet meer dan 14400 seconden.
• De klant-VPN-initiator moet de IKEv1 agressieve modus uitschakelen.
• De klant-VPN-initiator moet perfect forward secrecy configureren.
• De klant-VPN-initiator moet het gebruik van HMAC-SHA256 of hoger configureren.

Configuratie opties voor VPN-apparaten en IPSec/IKE-para meters zijn [beschikbaar](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) voor controle.

### <a name="azure-active-directory-setup"></a>Azure Active Directory instellen
[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) is essentieel voor het beheren van de implementatie en het inrichten van de toegang tot mede werkers met de omgeving. Een bestaande Windows Server-Active Directory kan in [vier klikken](../../active-directory/hybrid/how-to-connect-install-express.md)worden geïntegreerd met Azure Active Directory.

Daarnaast kunnen klanten met [Azure Active Directory Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) Federatie configureren met on-premises [Active Directory Federation Services]( ../../active-directory/hybrid/how-to-connect-fed-azure-adfs.md) en Azure Active Directory. Met aanmelden bij de Federatie kunnen klanten gebruikers in staat stellen zich aan te melden bij op Azure Active Directory gebaseerde services met hun lokale wacht woorden en zonder dat ze hun wacht woord opnieuw hoeven in te voeren in het bedrijfs netwerk. U kunt met behulp van de optie Federatie met Active Directory Federation Services een nieuwe installatie van Active Directory Federation Services implementeren of u kunt een bestaande installatie opgeven in een Windows Server 2012 R2-farm.

Om te voor komen dat geclassificeerde gegevens worden gesynchroniseerd met Azure Active Directory, kunnen klanten de kenmerken die worden gerepliceerd naar Azure Active Directory beperken door de volgende instellingen toe te passen in Azure Active Directory Connect:
- [Filteren inschakelen](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md)
- [Wachtwoord hash-synchronisatie uitschakelen](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md)
- [Wacht woord terugschrijven uitschakelen](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
- [Write-back van apparaat uitschakelen](../../active-directory/hybrid/how-to-connect-device-writeback.md)
- De standaard instellingen behouden voor het [voor komen van onbedoeld verwijderen](../../active-directory/hybrid/how-to-connect-sync-feature-prevent-accidental-deletes.md) en [automatische upgrade](../../active-directory/hybrid/how-to-connect-install-automatic-upgrade.md)

## <a name="disclaimer"></a>Vrijwaring
- Dit document is alleen ter informatie bedoeld. MICRO SOFT BIEDT GEEN ENKELE GARANTIE, UITDRUKKELIJK, IMPLICIET OF WETTELIJK, MET BETREKKING TOT DE INFORMATIE IN DIT DOCUMENT. Dit document wordt in de as-is opgenomen. Informatie en weer gaven in dit document, inclusief URL'S en andere website verwijzingen, kunnen zonder kennisgeving worden gewijzigd. Klanten die dit document lezen, hebben het risico van het gebruik ervan.
- Dit document biedt klanten geen juridische rechten voor intellectueel eigendom in een micro soft-product of-oplossingen.
- Klanten kunnen dit document kopiëren en gebruiken voor interne referentie doeleinden.
- Bepaalde aanbevelingen in dit document kunnen leiden tot meer gegevens-, netwerk-of COMPUTE-resource gebruik in azure, en kunnen de kosten van de Azure-licentie of het abonnement van de klant verhogen.
- Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.
- Dit document is ontwikkeld als referentie en mag niet worden gebruikt voor het definiëren van de manier waarop een klant kan voldoen aan specifieke nalevings vereisten en-voor Schriften. Klanten moeten juridische ondersteuning van hun organisatie zoeken op goedgekeurde klant implementaties.
