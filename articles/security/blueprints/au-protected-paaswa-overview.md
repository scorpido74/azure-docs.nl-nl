---
title: Azure-blauwdruk voor beveiliging en naleving-PaaS-webtoepassing voor Australië beveiligd
description: Azure-blauwdruk voor beveiliging en naleving-PaaS-webtoepassing voor Australië beveiligd
services: security
author: meladie
ms.assetid: 708aa129-b226-4e02-85c6-1f86e54564e4
ms.service: security
ms.topic: article
ms.date: 08/23/2018
ms.author: meladie
ms.openlocfilehash: 6d1b84894439010e5297ce010b2ece6dea8f3e56
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257609"
---
# <a name="azure-security-and-compliance-blueprint---paas-web-application-for-australia-protected"></a>Azure-blauwdruk voor beveiliging en naleving-PaaS-webtoepassing voor Australië beveiligd

## <a name="overview"></a>Overzicht

Deze Azure-blauwdruk voor beveiliging en naleving biedt richt lijnen voor de implementatie van een PaaS-omgeving (platform as a Service) die geschikt is voor het verzamelen, opslaan en ophalen van door AU beschermde overheids gegevens die voldoen aan de doel stellingen van de Australische Government Information Security-hand leiding (ISM), geproduceerd door het Australische signalen Directoraat (ASD). In deze blauw druk wordt een algemene referentie architectuur toegelicht en wordt de juiste verwerking van gevoelige overheids gegevens in een beveiligde, compatibele omgeving met meerdere lagen gedemonstreerd.

Deze referentie architectuur, implementatie handleiding en bedreigings model bieden een basis voor klanten om hun eigen plannings-en systeem accreditatie processen te kunnen uitvoeren, waardoor klanten werk belastingen op een op ASD gebaseerde manier kunnen implementeren naar Azure. Klanten kunnen ervoor kiezen om een Azure VPN Gateway of ExpressRoute te implementeren voor het gebruik van federatieve Services en om on-premises resources te integreren met Azure-resources. Klanten moeten rekening houden met de beveiligings implicaties van het gebruik van on-premises resources. Aanvullende configuratie is vereist om aan alle vereisten te voldoen, aangezien deze kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

Voor het bereiken van een ASD-compatibiliteit moet een informatie beveiliging geregistreerde beoordelaar het systeem controleren. Klanten zijn verantwoordelijk voor het uitvoeren van passende beveiligings-en nalevings beoordelingen van alle oplossingen die zijn gebouwd met behulp van deze architectuur, aangezien de vereisten kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

## <a name="architecture-diagram-and-components"></a>Architectuur diagram en onderdelen
Deze oplossing biedt een referentie architectuur voor een PaaS-webtoepassing met een Azure SQL Database back-end. De webtoepassing wordt gehost in een geïsoleerde Azure App Service Environment, een persoonlijke, toegewezen omgeving in een Azure-Data Center. De omgevings taak verdeelt het verkeer voor de webtoepassing op virtuele machines die worden beheerd door Azure. Voor alle verbindingen van webtoepassingen is TLS vereist met een minimum versie van 1,2. Deze architectuur omvat ook netwerk beveiligings groepen, een Application Gateway, Azure DNS en Load Balancer.

De architectuur kan een beveiligde hybride omgeving bieden die een on-premises netwerk uitbreidt naar Azure, zodat op internet gebaseerde workloads veilig kunnen worden benaderd door zakelijke gebruikers van het particuliere lokale netwerk van een organisatie of via internet. Voor on-premises oplossingen is de klant beide verantwoordelijk voor alle aspecten van beveiliging, bewerkingen en naleving.

De Azure-resources die in deze oplossing zijn opgenomen, kunnen verbinding maken met een on-premises netwerk-of Data Center-faciliteit (bijvoorbeeld CDC in Canberra) via een IPSec-VPN met behulp van een VPN Gateway en via ExpressRoute. Het besluit om gebruik te maken van een VPN moet worden uitgevoerd met de classificatie van de verzonden gegevens en het netwerkpad. Klanten die grootschalige, bedrijfskritische workloads met big data vereisten uitvoeren, moeten een hybride netwerk architectuur overwegen met ExpressRoute voor de connectiviteit van particuliere netwerken met Azure-Services. Raadpleeg de sectie [richt lijnen en aanbevelingen](#guidance-and-recommendations) voor meer informatie over verbindings mechanismen voor Azure.

Federatie met Azure Active Directory moet worden gebruikt om gebruikers in staat te stellen te verifiëren met on-premises referenties en toegang te krijgen tot alle resources in de Cloud met behulp van een on-premises Active Directory Federation Services-infra structuur. Active Directory Federation Services kunnen eenvoudige, beveiligde identiteits Federatie en mogelijkheden voor eenmalige aanmelding voor het web bieden voor deze hybride omgeving. Raadpleeg het gedeelte [richt lijnen en aanbevelingen](#guidance-and-recommendations) voor meer informatie Azure Active Directory Setup.

De oplossing maakt gebruik van Azure Storage accounts, die klanten kunnen configureren om Storage Service Encryption te gebruiken om de vertrouwelijkheid van gegevens in rust te houden. In Azure worden drie kopieën van gegevens opgeslagen in de geselecteerde regio van een klant voor tolerantie. Azure-regio's worden geïmplementeerd in robuuste regio paren en geografisch redundante opslag zorgt ervoor dat gegevens worden gerepliceerd naar de tweede regio met drie kopieën. Dit voor komt dat een nadelige gebeurtenis op de primaire gegevens locatie van de klant leidt tot verlies van gegevens.

Voor een betere beveiliging worden alle Azure-resources in deze oplossing als resource groep beheerd via Azure Resource Manager. Azure Active Directory op rollen gebaseerd toegangs beheer wordt gebruikt voor het beheren van de toegang tot geïmplementeerde resources en sleutels in Azure Key Vault. Systeem status wordt bewaakt via Azure Security Center en Azure Monitor. Klanten configureren beide bewakings Services voor het vastleggen van Logboeken en het weer geven van de systeem status in één eenvoudig navigeerbaar-dash board. Azure-toepassing gateway is geconfigureerd als een firewall in de modus voor preventie en zonder dat er geen TLS v 1.2 of hoger verkeer is. De oplossing maakt gebruik van Azure-toepassing service Environment v2 om de weblaag te isoleren in een niet-multi tenant omgeving.

![PaaS-webtoepassing voor met au beveiligde referentie architectuur](images/au-protected-paaswa-architecture.png?raw=true "PaaS-webtoepassing voor met au beveiligd referentie architectuur diagram")

Deze oplossing maakt gebruik van de volgende Azure-Services. Meer informatie vindt u in de sectie [implementatie architectuur](#deployment-architecture) .

- Application Gateway
    - Web Application Firewall
        - Firewall modus: voor komen
        - Regelset: OWASP
        - Listener-poort: 443
- Application Insights
- Azure Active Directory
- Azure-toepassing-service omgeving v2
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Monitor-logboeken
- Azure Virtual Network
    - (1)/16 netwerk
    - (4)/24 netwerken
    - Netwerkbeveiligingsgroepen
- Netwerkbeveiligingsgroepen
- Recovery Services-kluis
- Azure Web App

Deze blauw druk bevat Azure-Services die niet zijn gecertificeerd voor gebruik in de beveiligde classificatie van het Australische Cyber Security Center (ACSC). Micro soft raadt klanten aan de gepubliceerde beveiligings-en controle rapporten te bekijken die betrekking hebben op deze Azure-Services en hun risico beheer Framework te gebruiken om te bepalen of de Azure-service geschikt is voor hun interne erkenning en te gebruiken op het Beveiligde classificatie.

## <a name="deployment-architecture"></a>Implementatie architectuur
De volgende sectie bevat informatie over de implementatie-en implementatie-elementen.

**Azure Resource Manager**: Met [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) kunnen klanten samen werken met de resources in de oplossing als groep. Klanten kunnen alle resources voor de oplossing implementeren, bijwerken of verwijderen in een enkele, gecoördineerde bewerking. Klanten gebruiken een sjabloon voor implementatie en die sjabloon kan worden gebruikt voor verschillende omgevingen, zoals testen, faseren en productie. Resource Manager biedt functies voor beveiliging, controle en labeling om klanten te helpen bij het beheren van hun resources na de implementatie.

**Bastion-host**: De bastion-host is het enige toegangs punt dat gebruikers in staat stelt om toegang te krijgen tot de geïmplementeerde resources in deze omgeving. De bastion-host biedt een beveiligde verbinding met geïmplementeerde bronnen door alleen extern verkeer van open bare IP-adressen in een veilige lijst toe te staan. Als u RDP-verkeer (extern bureau blad) wilt toestaan, moet de bron van het verkeer worden gedefinieerd in de netwerk beveiligings groep.

Met deze oplossing maakt u een virtuele machine als een aan een domein gekoppelde bastion-host met de volgende configuraties:
-   [Uitbrei ding voor antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure Diagnostics extensie](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) Azure Key Vault gebruiken
-   Een [beleid voor automatisch afsluiten](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) om het verbruik van resources van de virtuele machine te verminderen wanneer het niet wordt gebruikt

**App service Environment v2**: De [Azure app service Environment](https://docs.microsoft.com/azure/app-service/environment/intro) is een app service-functie die een volledig geïsoleerde en toegewezen omgeving biedt voor het veilig uitvoeren van app service toepassingen op een hoge schaal.

App Service omgevingen worden alleen geïsoleerd voor het uitvoeren van de toepassingen van één klant en worden altijd geïmplementeerd in een virtueel netwerk. Klanten hebben nauw keurige controle over zowel binnenkomend als uitgaand netwerk verkeer en toepassingen kunnen snelle, veilige verbindingen tot stand brengen via virtuele netwerken naar on-premises bedrijfs bronnen.

Het gebruik van App Service omgevingen voor deze architectuur staat de volgende besturings elementen/configuraties toe:

- App Service omgevingen moeten worden geconfigureerd voor het gebruik van het geïsoleerde service-abonnement
    - Andere App Service omgevingen configureren voor toepassingen met verschillende classificaties
- Host binnen een beveiligd Azure Virtual Network en netwerk beveiligings regels
- App Service omgevingen die zijn geconfigureerd met een zelf-ondertekend intern load balancer certificaat voor HTTPS-communicatie. Als best practice, raadt micro soft u aan het gebruik van een vertrouwde certificerings instantie te gebruiken voor verbeterde beveiliging.
- [Modus voor interne taak verdeling](../../app-service/environment/app-service-environment-with-internal-load-balancer.md) (modus 3)
- [TLS v 1.0 en v 1.1](../../app-service/environment/app-service-app-service-environment-custom-settings.md) uitschakelen
- [TLS-code ring](../../app-service/environment/app-service-app-service-environment-custom-settings.md) wijzigen
- [Inkomend verkeer voor N/W-poorten](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md) beheren
- [Web Application firewall: gegevens beperken](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- [Azure SQL database verkeer](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md) toestaan

**Azure-web-app**: Met [Azure app service](https://docs.microsoft.com/azure/app-service/) kunnen klanten webtoepassingen bouwen en hosten in de programmeer taal van hun keuze zonder dat de infra structuur hoeft te worden beheerd. Het biedt automatisch schalen en een hoge beschikbaarheid, ondersteuning voor zowel Windows als Linux en maakt automatische implementaties mogelijk vanuit GitHub, Azure DevOps Services of een willekeurige Git-repo.

### <a name="virtual-network"></a>Virtueel netwerk
De architectuur definieert een particulier virtueel netwerk met een adres ruimte van 10.200.0.0/16.

**Netwerk beveiligings groepen**: [Netwerk beveiligings groepen](../../virtual-network/virtual-network-vnet-plan-design-arm.md) bevatten toegangs beheer lijsten waarmee verkeer binnen een virtueel netwerk wordt toegestaan of geweigerd. Netwerk beveiligings groepen kunnen worden gebruikt om verkeer op een subnet of op een afzonderlijke virtuele-machine niveau te beveiligen. De volgende netwerk beveiligings groepen bestaan:
- 1 netwerk beveiligings groep voor Application Gateway
- 1 netwerk beveiligings groep voor App Service Environment
- 1 netwerk beveiligings groep voor Azure SQL Database
- 1 netwerk beveiligings groep voor Bastion-host

Voor elk van de netwerk beveiligings groepen zijn specifieke poorten en protocollen geopend, zodat de oplossing veilig en goed werkt. Daarnaast zijn de volgende configuraties ingeschakeld voor elke netwerk beveiligings groep:

  - [Diagnostische logboeken en gebeurtenissen](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) worden ingeschakeld en opgeslagen in een opslag account
  - Azure Monitor-logboeken zijn verbonden met de [Diagnostische gegevens van de netwerk beveiligings groep](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Subnetten**: Elk subnet is gekoppeld aan de bijbehorende netwerk beveiligings groep.

**Azure DNS**: De Domain Name System, of DNS, is verantwoordelijk voor het vertalen van een website-of service naam naar het IP-adres. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) is een hosting service voor DNS-domeinen die naam omzetting biedt met behulp van Azure-infra structuur. Door domeinen in azure te hosten, kunnen gebruikers DNS-records beheren met dezelfde referenties, Api's, hulpprogram ma's en facturering als andere Azure-Services. Azure DNS biedt ook ondersteuning voor privé-DNS-domeinen.

**Azure Load Balancer**: Met [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) kunnen klanten hun toepassingen schalen en hoge Beschik baarheid maken voor services. Load Balancer ondersteunt zowel binnenkomende als uitgaande scenario's, en biedt lage latentie, hoge door Voer en schaalbaar tot miljoenen stromen voor alle TCP-en UDP-toepassingen.

### <a name="data-in-transit"></a>Actieve gegevens
Azure versleutelt standaard alle communicatie naar en van Azure-data centers. 

Voor beveiligde gegevens in door Voer van netwerken die eigendom zijn van de klant, gebruikt de architectuur Azure Internet of ExpressRoute met een VPN Gateway dat is geconfigureerd met IPSEC.

Daarnaast vinden alle trans acties naar Azure via de Azure-beheer portal plaats via HTTPS, waarbij TLS v 1.2 wordt gebruikt.

### <a name="data-at-rest"></a>Data-at-rest
De architectuur beveiligt gegevens op rest door versleuteling, database controle en andere metingen.

**Azure Storage**: Om te voldoen aan de versleutelde gegevens op rest-vereisten, gebruikt alle [Azure Storage](https://azure.microsoft.com/services/storage/) [Storage service Encryption](../../storage/common/storage-service-encryption.md). Dit helpt bij het beschermen en beschermen van gegevens ter ondersteuning van organisatie beveiligings verplichtingen en nalevings vereisten die zijn gedefinieerd door de Australische overheid ISM.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) maakt gebruik van de BitLocker-functie van Windows om volume versleuteling voor gegevens schijven te bieden. De oplossing kan worden geïntegreerd met Azure Key Vault om de versleutelings sleutels voor de schijf te controleren en te beheren.

**Azure SQL Database**: Het Azure SQL Database exemplaar maakt gebruik van de volgende data base Security-maat eenheden:
-   Met [Active Directory verificatie en autorisatie](https://docs.microsoft.com/azure/sql-database/sql-database-AAD-authentication) kan identiteits beheer van database gebruikers en andere micro soft-Services op één centrale locatie worden beheerd.
-   Met [SQL database controle](../../sql-database/sql-database-auditing.md) worden database gebeurtenissen bijgehouden en naar een audit logboek in een Azure-opslag account geschreven.
-   Azure SQL Database is geconfigureerd voor het gebruik van [transparante gegevens versleuteling](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), waarmee in realtime versleuteling en ontsleuteling van de data base, gekoppelde back-ups en transactie logboek bestanden worden uitgevoerd om informatie te beveiligen. Transparent Data Encryption biedt zekerheid dat opgeslagen gegevens niet zijn onderworpen aan onbevoegde toegang.
-   [Firewall regels](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor komen dat alle toegang tot database servers tot de juiste machtigingen worden verleend. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt.
-   Met de [detectie van SQL-bedreigingen](../../sql-database/sql-database-threat-detection.md) kunnen mogelijke dreigingen worden gedetecteerd en gereageerd als ze optreden door beveiligings waarschuwingen te bieden voor verdachte database activiteiten, potentiële kwetsbaar heden, SQL-injectie aanvallen en afwijkende database toegangs patronen. SQL Threat Detection integreert waarschuwingen met [Azure Security Center](https://azure.microsoft.com/services/security-center/), waaronder Details van verdachte activiteiten en aanbevolen actie voor het onderzoeken en oplossen van de dreiging.
-   [Always encrypted kolommen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zorgen ervoor dat gevoelige gegevens nooit als tekst zonder opmaak in het database systeem worden weer gegeven. Na het inschakelen van gegevens versleuteling, hebben alleen client toepassingen of toepassings servers met toegang tot de sleutels toegang tot tekst zonder opmaak.
- [SQL database dynamische gegevens maskering](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) beperkt de bloot stelling van gevoelige gegevens door de gegevens te maskeren voor niet-gemachtigde gebruikers of toepassingen. Met dynamische gegevens maskering kunnen mogelijk gevoelige gegevens automatisch worden gedetecteerd en worden de juiste maskers voorgesteld om te worden toegepast. Dit helpt bij het verminderen van de toegang, zodat gevoelige gegevens de data base niet afsluiten via onbevoegde toegang. Klanten moeten de instellingen voor dynamische gegevens maskering aanpassen om te voldoen aan hun database schema.

### <a name="identity-management"></a>Identiteitsbeheer
Klanten kunnen gebruikmaken van on-premises Active Directory federatieve Services om met [Azure Active Directory](https://azure.microsoft.com/services/active-directory/)te communiceren. Dit is de multi tenant-Cloud Directory en identiteits beheer service van micro soft. [Azure Active Directory Connect](../../active-directory/hybrid/whatis-hybrid-identity.md) integreert on-premises directory's met Azure Active Directory. Voor alle gebruikers in deze oplossing zijn Azure Active Directory accounts vereist, met inbegrip van gebruikers die toegang hebben tot de Azure SQL Database. Met aanmelden bij de Federatie kunnen gebruikers zich aanmelden bij Azure Active Directory en zich verifiëren bij Azure-resources met behulp van on-premises referenties.

Daarnaast kunt u met de volgende Azure Active Directory-functies de toegang tot gegevens in de Azure-omgeving beheren:
- Verificatie voor de toepassing wordt uitgevoerd met behulp van Azure Active Directory. Zie [toepassingen integreren met Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)voor meer informatie. Daarnaast maakt de database kolom versleuteling gebruik van Azure Active Directory om de toepassing te verifiëren voor Azure SQL Database. Zie voor meer informatie hoe u [gevoelige gegevens in Azure SQL database kunt beveiligen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Met op [rollen gebaseerd toegangs beheer van Azure](../../role-based-access-control/role-assignments-portal.md) kunnen beheerders verfijnde toegangs machtigingen definiëren om alleen de hoeveelheid toegang te verlenen die gebruikers nodig hebben om hun taken uit te voeren. In plaats van elke gebruiker onbeperkte machtiging voor Azure-resources geven, kunnen beheerders alleen bepaalde acties toestaan voor toegang tot gegevens. Abonnements toegang is beperkt tot de abonnements beheerder.
- Met [Azure Active Directory privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) kunnen klanten het aantal gebruikers dat toegang heeft tot bepaalde informatie minimaliseren. Beheerders kunnen Azure Active Directory Privileged Identity Management gebruiken om bevoegde identiteiten en hun toegang tot bronnen te detecteren, beperken en controleren. Deze functie kan ook worden gebruikt voor het afdwingen van alleen-in-time-beheer toegang op aanvraag als dat nodig is.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecteert mogelijke beveiligings problemen die van invloed zijn op een organisatie-identiteit, configureert automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op de identiteiten van organisaties en onderzoekt verdachte incidenten om deze problemen op te lossen.

**Azure multi-factor Authentication**: Als u identiteiten wilt beveiligen, moet multi-factor Authentication worden geïmplementeerd. [Azure multi-factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/) is een gebruiks vriendelijke, schaal bare en betrouw bare oplossing die een tweede verificatie methode biedt om gebruikers te beschermen. Azure Multi-Factor Authentication maakt gebruik van de kracht van de Cloud en integreert met on-premises Active Directory en aangepaste toepassingen. Deze beveiliging wordt uitgebreid naar essentiële scenario's met een hoge omvang.

### <a name="security"></a>Beveiliging
**Beheer van geheimen**: De oplossing maakt gebruik van [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) voor het beheer van sleutels en geheimen. Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Met de volgende Azure Key Vault mogelijkheden kunnen klanten gegevens beveiligen:
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

**Application Gateway**: De architectuur vermindert het risico op beveiligings problemen met behulp van een Application Gateway met een Web Application Firewall en de ruleset OWASP ingeschakeld. Aanvullende mogelijkheden zijn onder andere:

- [End-to-end-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL-offload](../../application-gateway/create-ssl-portal.md) inschakelen
- [TLS v 1.0 en v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) uitschakelen
- [Web Application firewall](../../application-gateway/waf-overview.md) (preventie modus)
- [Preventie modus](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) met OWASP 3,0 ruleSet
- [Logboek registratie van diagnostische gegevens](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics) inschakelen
- [Aangepaste status tests](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) en [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) bieden extra beveiliging en meldingen. Azure Security Center biedt ook een reputatie systeem.

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

Azure Network Watcher: [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) biedt hulpprogram ma's voor het bewaken, diagnosticeren, weer geven van metrische gegevens en het in-of uitschakelen van Logboeken voor bronnen in een virtueel Azure-netwerk.  Gemenebests entiteiten moeten Network Watcher-stroom logboeken implementeren voor Nsg's en Virtual Machines. Deze logboeken moeten worden opgeslagen in een speciaal opslag account dat alleen beveiligings logboeken worden opgeslagen in en de toegang tot het opslag account moet worden beveiligd met op rollen gebaseerd toegangs beheer.

## <a name="threat-model"></a>Bedreigings model

Het gegevensstroom diagram voor deze referentie architectuur is beschikbaar voor [downloaden](https://aka.ms/au-protected-paaswa-tm) of kan hieronder worden weer gegeven. Dit model kan klanten helpen om inzicht te krijgen in de punten van potentieel risico in de systeem infrastructuur bij het aanbrengen van wijzigingen.

![PaaS-webtoepassing voor met au beveiligd Threat model](images/au-protected-paaswa-threat-model.png?raw=true "PaaS-webtoepassing voor au-beveiligd Threat model diagram")

## <a name="compliance-documentation"></a>Documentatie voor naleving
Deze nalevings documentatie wordt gemaakt door micro soft op basis van platformen en services die beschikbaar zijn via micro soft. Vanwege de grote verscheidenheid aan klant implementaties biedt deze documentatie een gegeneraliseerde aanpak voor een oplossing die alleen wordt gehost in de Azure-omgeving. Klanten kunnen alternatieve producten en services identificeren en gebruiken op basis van hun eigen bedrijfs omgevingen en bedrijfs resultaten. Klanten die on-premises Resources kiezen, moeten de beveiliging en bewerkingen voor die on-premises resources aanpakken. De gedocumenteerde oplossing kan worden aangepast door klanten om hun specifieke on-premises en beveiligings vereisten op te lossen.

De [matrix met door au beveiligde gebruikers verantwoordelijkheden bevat Azure-blauwdruk voor beveiliging en naleving](https://aka.ms/au-protected-crm) een lijst met alle beveiligings controles die vereist zijn voor au-Security Protocol. Deze matrix geeft aan of de implementatie van elk besturings element de verantwoordelijkheid is van micro soft, de klant of het gedeeld tussen de twee.

De [Azure-blauwdruk voor beveiliging en naleving-au-Protected PaaS Web Application implementation-matrix](https://aka.ms/au-protected-paaswa-cim) bevat informatie over welke met au beveiligde besturings elementen worden verholpen door de architectuur van de Paas-webtoepassing, met inbegrip van gedetailleerde beschrijvingen van hoe de implementatie voldoet aan de vereisten van elk gedekte besturings element.

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
-   [Wacht woord terugschrijven uitschakelen](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr)
-   [Write-back van apparaat uitschakelen](../../active-directory/hybrid/how-to-connect-device-writeback.md)
-   De standaard instellingen behouden voor het [voor komen van onbedoeld verwijderen](../../active-directory/hybrid/how-to-connect-sync-feature-prevent-accidental-deletes.md) en [automatische upgrade](../../active-directory/hybrid/how-to-connect-install-automatic-upgrade.md)


## <a name="disclaimer"></a>Vrijwaring

 - Dit document is alleen ter informatie bedoeld. MICRO SOFT BIEDT GEEN ENKELE GARANTIE, UITDRUKKELIJK, IMPLICIET OF WETTELIJK, MET BETREKKING TOT DE INFORMATIE IN DIT DOCUMENT. Dit document wordt in de as-is opgenomen. Informatie en weer gaven in dit document, inclusief URL'S en andere website verwijzingen, kunnen zonder kennisgeving worden gewijzigd. Klanten die dit document lezen, hebben het risico van het gebruik ervan.
 - Dit document biedt klanten geen juridische rechten voor intellectueel eigendom in een micro soft-product of-oplossingen.
 - Klanten kunnen dit document kopiëren en gebruiken voor interne referentie doeleinden.
 - Bepaalde aanbevelingen in dit document kunnen leiden tot meer gegevens-, netwerk-of COMPUTE-resource gebruik in azure, en kunnen de kosten van de Azure-licentie of het abonnement van de klant verhogen.
 - Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.
 - Dit document is ontwikkeld als referentie en mag niet worden gebruikt voor het definiëren van de manier waarop een klant kan voldoen aan specifieke nalevings vereisten en-voor Schriften. Klanten moeten juridische ondersteuning van hun organisatie zoeken op goedgekeurde klant implementaties.
