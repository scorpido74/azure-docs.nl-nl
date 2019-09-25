---
title: Azure-blauwdruk voor beveiliging en naleving-PaaS-webtoepassing voor PCI DSS
description: Azure-blauwdruk voor beveiliging en naleving-PaaS-webtoepassing voor PCI DSS
services: security
author: meladie
ms.assetid: 5ef64374-7b4e-4176-afe1-0724072f653c
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 45c3e5da6cff51e14d22ed3a6a3ab526b96b50ef
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259659"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-pci-dss"></a>Azure-blauwdruk voor beveiliging en naleving: PaaS-webtoepassing voor PCI DSS

## <a name="overview"></a>Overzicht

Deze Azure-blauwdruk voor beveiliging en naleving automatisering biedt richt lijnen voor de implementatie van een PaaS-omgeving (platform as a Service) die compatibel is met de betalings kaart (PCI DSS 3,2) die geschikt is voor het verzamelen, opslaan en ophalen van gegevens. gegevens van de kaart houder. Met deze oplossing automatiseert u de implementatie en configuratie van Azure-resources voor een algemene referentie architectuur, waarbij u kunt zien hoe klanten aan specifieke vereisten voor beveiliging en naleving voldoen en als basis voor de ontwikkeling van klanten kunnen worden gebruikt en Configureer hun eigen oplossingen op Azure. De oplossing implementeert een subset van vereisten van PCI DSS 3,2. Zie de sectie [nalevings documentatie](#compliance-documentation) voor meer informatie over de vereisten van PCI DSS 3,2 en deze oplossing.

Deze Azure-blauwdruk voor beveiliging en naleving automatisering implementeert automatisch een referentie architectuur van een PaaS-webtoepassing met vooraf geconfigureerde beveiligings controles zodat klanten kunnen voldoen aan de vereisten van PCI DSS 3,2. De oplossing bestaat uit Azure Resource Manager sjablonen en Power shell-scripts die de implementatie en configuratie van resources begeleiden.

Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving. Het implementeren van een toepassing in deze omgeving zonder wijziging is niet voldoende om volledig te voldoen aan de vereisten van PCI DSS 3,2. Houd rekening met het volgende:
- Deze architectuur biedt een basis lijn om klanten te helpen bij het gebruik van Azure in een PCI DSS 3,2-bestendige manier.
- Klanten zijn verantwoordelijk voor het uitvoeren van de juiste beveiligings-en compatibiliteits beoordeling van alle oplossingen die zijn gebouwd met behulp van deze architectuur, aangezien de vereisten kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

Voor het bereiken van PCI DSS-naleving is vereist dat een goedgekeurde gekwalificeerde beveiligings-beoordelaar (QSA) een productie klant oplossing certificeren. Klanten zijn verantwoordelijk voor het uitvoeren van passende beveiligings-en nalevings beoordelingen van alle oplossingen die zijn gebouwd met behulp van deze architectuur, aangezien de vereisten kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

Klik [hier](https://aka.ms/pcidss-paaswa-repo) voor implementatie-instructies.

## <a name="architecture-diagram-and-components"></a>Architectuur diagram en onderdelen

Deze Azure-blauwdruk voor beveiliging en naleving Automation implementeert een referentie architectuur voor een PaaS-webtoepassing met een Azure SQL Database back-end. De webtoepassing wordt gehost in een geïsoleerde Azure App Service Environment, een persoonlijke, toegewezen omgeving in een Azure-Data Center. De omgevings taak verdeelt het verkeer voor de webtoepassing op virtuele machines die worden beheerd door Azure. Deze architectuur omvat ook netwerk beveiligings groepen, een Application Gateway, Azure DNS en Load Balancer.

Azure SQL-data bases kunnen worden geconfigureerd met column Store-indexen voor uitgebreide analyse en rapportage. Azure SQL-data bases kunnen omhoog of omlaag worden geschaald of volledig worden afgesloten in reactie op het gebruik van de klant. Alle SQL-verkeer wordt versleuteld met SSL via het opnemen van zelfondertekende certificaten. Als best practice, raadt Azure het gebruik aan van een vertrouwde certificerings instantie voor verbeterde beveiliging.

De oplossing maakt gebruik van Azure Storage accounts, die klanten kunnen configureren om Storage Service Encryption te gebruiken om de vertrouwelijkheid van gegevens in rust te houden. In Azure worden drie kopieën van gegevens opgeslagen in het geselecteerde Data Center van de klant voor tolerantie. Geografisch redundante opslag zorgt ervoor dat gegevens worden gerepliceerd naar een secundair Data Center honderden kilo meters en worden opgeslagen als drie kopieën binnen dat Data Center, waardoor een nadelige gebeurtenis op het primaire Data Center van de klant wordt voor komen, wat leidt tot een verlies van gegevens.

Voor een betere beveiliging worden alle resources in deze oplossing als resource groep beheerd via Azure Resource Manager. Azure Active Directory op rollen gebaseerd toegangs beheer wordt gebruikt voor het beheren van de toegang tot geïmplementeerde resources, inclusief de bijbehorende sleutels in Azure Key Vault. De systeem status wordt bewaakt via Azure Monitor. Klanten configureren beide bewakings Services voor het vastleggen van Logboeken en het weer geven van de systeem status in één eenvoudig navigeerbaar-dash board.

Azure SQL Database wordt doorgaans beheerd via SQL Server Management Studio, dat wordt uitgevoerd vanaf een lokale computer die is geconfigureerd voor toegang tot de Azure SQL Database via een beveiligde VPN-of ExpressRoute-verbinding.

Daarnaast biedt Application Insights realtime beheer van toepassings prestaties en-analyse via Azure Monitor-Logboeken. **U wordt aangeraden een VPN-of ExpressRoute-verbinding te configureren voor beheer en het importeren van gegevens in het subnet met referentie architectuur.**

![PaaS-webtoepassing voor PCI DSS referentie architectuur diagram](images/pcidss-paaswa-architecture.png "PaaS-webtoepassing voor PCI DSS referentie architectuur diagram")

Deze oplossing maakt gebruik van de volgende Azure-Services. Details van de implementatie architectuur vindt u in de sectie [implementatie architectuur](#deployment-architecture) .

- App Service Environment v2
- Application Gateway
  - (1) Web Application Firewall
    - Firewall modus: voor komen
    - Regelset: OWASP 3,0
    - Listener-poort: 443
- Application Insights
- Azure Active Directory
- Azure Automation
- Azure DNS
- Azure Key Vault
- Azure Load Balancer
- Azure Monitor
- Azure Resource Manager
- Azure Security Center
- Azure SQL Database
- Azure Storage
- Azure Virtual Network
    - (1)/16 netwerk
    - (4)/24 netwerken
    - (4) netwerk beveiligings groepen
- Azure Web App

## <a name="deployment-architecture"></a>Implementatie architectuur

De volgende sectie bevat informatie over de implementatie-en implementatie-elementen.

**Azure Resource Manager**: Met [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) kunnen klanten samen werken met de resources in de oplossing als groep. Klanten kunnen alle resources voor de oplossing implementeren, bijwerken of verwijderen in een enkele, gecoördineerde bewerking. Klanten gebruiken een sjabloon voor implementatie en die sjabloon kan worden gebruikt voor verschillende omgevingen, zoals testen, faseren en productie. Resource Manager biedt functies voor beveiliging, controle en labeling om klanten te helpen bij het beheren van hun resources na de implementatie.

**Bastion-host**: De bastion-host is het enige toegangs punt dat gebruikers in staat stelt om toegang te krijgen tot de geïmplementeerde resources in deze omgeving. De bastion-host biedt een beveiligde verbinding met geïmplementeerde bronnen door alleen extern verkeer van open bare IP-adressen in een veilige lijst toe te staan. Als u RDP-verkeer (extern bureau blad) wilt toestaan, moet de bron van het verkeer worden gedefinieerd in de netwerk beveiligings groep.

Met deze oplossing maakt u een virtuele machine als een aan een domein gekoppelde bastion-host met de volgende configuraties:
-   [Uitbrei ding voor antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Azure Diagnostics extensie](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) Azure Key Vault gebruiken
-   Een [beleid voor automatisch afsluiten](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) om het verbruik van resources van de virtuele machine te verminderen wanneer het niet wordt gebruikt
-   [Windows Defender Credential Guard](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) is ingeschakeld zodat referenties en andere geheimen worden uitgevoerd in een beveiligde omgeving die is geïsoleerd van het actieve besturings systeem

**App service Environment v2**: De Azure App Service Environment is een App Service-functie die een volledig geïsoleerde en toegewezen omgeving biedt voor het veilig uitvoeren van App Service toepassingen op een hoge schaal. Deze isolatie functie is vereist om te voldoen aan de vereisten voor PCI-naleving.

App Service omgevingen worden alleen geïsoleerd voor het uitvoeren van de toepassingen van één klant en worden altijd geïmplementeerd in een virtueel netwerk. Met deze isolatie functie kan de referentie architectuur volledige Tenant isolatie hebben, waardoor het verwijderen van de multi tenant-omgeving van Azure is verboden om te voor komen dat deze multi tenants de geïmplementeerde App Service Environment resources inventariseren. Klanten hebben nauw keurige controle over zowel binnenkomend als uitgaand netwerk verkeer en toepassingen kunnen snelle, veilige verbindingen tot stand brengen via virtuele netwerken naar on-premises bedrijfs bronnen. Klanten kunnen ' automatisch schalen ' met App Service Environment op basis van metrische gegevens over belasting, beschikbaar budget of een gedefinieerd schema.

Gebruik App Service omgevingen voor de volgende besturings elementen/configuraties:

- Host binnen een beveiligde Azure-Virtual Network en netwerk beveiligings regels
- Zelfondertekend ILB-certificaat voor HTTPS-communicatie
- [Modus voor interne taak verdeling](../../app-service/environment/app-service-environment-with-internal-load-balancer.md)
- [TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md) uitschakelen
- [TLS-code ring](../../app-service/environment/app-service-app-service-environment-custom-settings.md) wijzigen
- [Inkomend verkeer voor N/W-poorten](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md) beheren
- [Web Application firewall: gegevens beperken](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- [Azure SQL database verkeer](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md) toestaan

**Azure-web-app**: Met [Azure app service](https://docs.microsoft.com/azure/app-service/) kunnen klanten webtoepassingen bouwen en hosten in de programmeer taal van hun keuze zonder dat de infra structuur hoeft te worden beheerd. Het biedt automatisch schalen en een hoge beschikbaarheid, ondersteuning voor zowel Windows als Linux en maakt automatische implementaties mogelijk vanuit GitHub, Azure DevOps of een willekeurige Git-repo.

### <a name="virtual-network"></a>Virtueel netwerk

De architectuur definieert een persoonlijke Virtual Network met een adres ruimte van 10.200.0.0/16.

**Netwerk beveiligings groepen**: [Netwerk beveiligings groepen](../../virtual-network/virtual-network-vnet-plan-design-arm.md) bevatten Access Control-lijsten (acl's) voor het toestaan of weigeren van verkeer binnen een Virtual Network. Netwerk beveiligings groepen kunnen worden gebruikt voor het beveiligen van verkeer op een subnet of een afzonderlijk VM-niveau. De volgende netwerk beveiligings groepen bestaan:

- 1 netwerk beveiligings groep voor Application Gateway
- 1 netwerk beveiligings groep voor App Service Environment
- 1 netwerk beveiligings groep voor Azure SQL Database
- 1 netwerk beveiligings groep voor Bastion-host

Voor elk van de netwerk beveiligings groepen zijn specifieke poorten en protocollen geopend, zodat de oplossing veilig en goed werkt. Daarnaast zijn de volgende configuraties ingeschakeld voor elke netwerk beveiligings groep:

- [Diagnostische logboeken en gebeurtenissen](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) worden ingeschakeld en opgeslagen in een opslag account
- Azure Monitor-logboeken zijn verbonden met de [netwerk&#39;beveiligings groep s diagnostische gegevens](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Subnetten**: Elk subnet is gekoppeld aan de bijbehorende netwerk beveiligings groep.

**Azure DNS**: De Domain Name System, of DNS, is verantwoordelijk voor het vertalen van een website-of service naam naar het IP-adres. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) is een hosting service voor DNS-domeinen die naam omzetting biedt met behulp van Azure-infra structuur. Door domeinen in azure te hosten, kunnen gebruikers DNS-records beheren met dezelfde referenties, Api's, hulpprogram ma's en facturering als andere Azure-Services. Azure DNS biedt ook ondersteuning voor privé-DNS-domeinen.

**Azure Load Balancer**: Met [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) kunnen klanten hun toepassingen schalen en hoge Beschik baarheid maken voor services. Load Balancer ondersteunt zowel binnenkomende als uitgaande scenario's, en biedt lage latentie, hoge door Voer en schaalbaar tot miljoenen stromen voor alle TCP-en UDP-toepassingen.

### <a name="data-in-transit"></a>Actieve gegevens

Azure versleutelt standaard alle communicatie naar en van Azure-data centers. Alle trans acties die via de Azure Portal worden Azure Storage, vindt plaats via HTTPS.

### <a name="data-at-rest"></a>Data-at-rest

De architectuur beveiligt gegevens op rest door versleuteling, database controle en andere metingen.

**Azure Storage**: Om te voldoen aan de versleutelde gegevens op rest-vereisten, gebruikt alle [Azure Storage](https://azure.microsoft.com/services/storage/) [Storage service Encryption](../../storage/common/storage-service-encryption.md). Dit helpt de gegevens van de kaart houder te beschermen en te beschermen ter ondersteuning van organisatie beveiligings verplichtingen en nalevings vereisten die zijn gedefinieerd door PCI DSS 3,2.

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

De volgende technologieën bieden mogelijkheden voor het beheren van toegang tot gegevens van de kaart houder in de Azure-omgeving:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) is een&#39;op de cloud gebaseerde Directory-en identiteits beheer service van micro soft s met meerdere tenants. Alle gebruikers voor deze oplossing worden gemaakt in Azure Active Directory, met inbegrip van gebruikers die toegang hebben tot de Azure SQL Database.
- Verificatie voor de toepassing wordt uitgevoerd met behulp van Azure Active Directory. Zie [toepassingen integreren met Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)voor meer informatie. Daarnaast maakt de database kolom versleuteling gebruik van Azure Active Directory om de toepassing te verifiëren voor Azure SQL Database. Zie voor meer informatie hoe u [gevoelige gegevens in Azure SQL database kunt beveiligen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- Met op [rollen gebaseerd toegangs beheer van Azure](../../role-based-access-control/role-assignments-portal.md) kunnen beheerders verfijnde toegangs machtigingen definiëren om alleen de hoeveelheid toegang te verlenen die gebruikers nodig hebben om hun taken uit te voeren. In plaats van elke gebruiker onbeperkte machtiging voor Azure-resources geven, kunnen beheerders alleen bepaalde acties toestaan om toegang te krijgen tot gegevens van de kaart houder. Abonnements toegang is beperkt tot de abonnements beheerder.
- Met [Azure Active Directory privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) kunnen klanten het aantal gebruikers dat toegang heeft tot bepaalde gegevens, zoals gegevens van de kaart houder, minimaliseren. Beheerders kunnen Azure Active Directory Privileged Identity Management gebruiken om bevoegde identiteiten en hun toegang tot bronnen te detecteren, beperken en controleren. Deze functie kan ook worden gebruikt voor het afdwingen van alleen-in-time-beheer toegang op aanvraag als dat nodig is.
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

**Azure Security Center**: Met [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)kunnen klanten centraal beveiligings beleid Toep assen en beheren voor werk belastingen, bloot stelling aan bedreigingen beperken en aanvallen detecteren en erop reageren. Daarnaast Azure Security Center de bestaande configuraties van Azure-Services openen om aanbevelingen voor de configuratie en service te bieden voor het verbeteren van de beveiliging postuur en het beveiligen van gegevens.

Azure Security Center maakt gebruik van diverse detectie mogelijkheden om klanten te waarschuwen over mogelijke aanvallen die zijn gericht op hun omgeving. Deze waarschuwingen bevatten waardevolle informatie over de trigger van de waarschuwing, de betrokken resources en de bron van de aanval. Azure Security Center heeft een reeks [vooraf gedefinieerde beveiligings waarschuwingen](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), die worden geactiveerd wanneer een bedreiging of verdachte activiteit plaatsvindt. Met [aangepaste waarschuwings regels](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) in azure Security Center kunnen klanten nieuwe beveiligings waarschuwingen definiëren op basis van gegevens die al zijn verzameld uit hun omgeving.

Azure Security Center biedt beveiligings waarschuwingen en-incidenten met prioriteit, waardoor klanten eenvoudiger mogelijke beveiligings problemen kunnen detecteren en oplossen. Er wordt een [Threat Intelligence-rapport](https://docs.microsoft.com/azure/security-center/security-center-threat-report) voor elke gedetecteerde bedreiging gegenereerd om de incidenten te helpen bij het onderzoeken en oplossen van bedreigingen.

**Azure-toepassing gateway**: De architectuur vermindert het risico op beveiligings problemen met behulp van een Azure-toepassing gateway waarvoor een Web Application Firewall is geconfigureerd en de ruleset OWASP ingeschakeld. Aanvullende mogelijkheden zijn onder andere:

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

**Azure monitor logboeken**: Deze logboeken worden samengevoegd in [Azure monitor logboeken](https://azure.microsoft.com/services/log-analytics/) voor verwerking, opslag en dashboard rapportage. Zodra de gegevens zijn verzameld, worden deze in verschillende tabellen ingedeeld voor elk gegevens type binnen Log Analytics werk ruimten, waardoor alle gegevens kunnen worden geanalyseerd, ongeacht de oorspronkelijke bron. Bovendien kan Azure Security Center worden geïntegreerd met Azure Monitor-logboeken, zodat klanten Kusto-query's kunnen gebruiken om toegang te krijgen tot de gegevens van de beveiligings gebeurtenis en deze te combi neren met gegevens uit andere services.

De volgende Azure- [bewakings oplossingen](../../monitoring/monitoring-solutions.md) zijn opgenomen als onderdeel van deze architectuur:
-   [Active Directory-evaluatie](../../azure-monitor/insights/ad-assessment.md): De Active Directory Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval en biedt een lijst met aanbevelingen die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [SQL-evaluatie](../../azure-monitor/insights/sql-assessment.md): De SQL Health Check-oplossing evalueert het risico en de status van de server omgevingen met een regel matig interval en biedt klanten een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
- [Status van agent](../../monitoring/monitoring-solution-agenthealth.md): De Status van agent oplossing meldt hoeveel agents zijn geïmplementeerd en wat hun geografische distributie zijn, en hoeveel agents niet reageren en het aantal agents dat operationele gegevens verzendt.
-   [Analyse van activiteitenlogboek](../../azure-monitor/platform/collect-activity-logs.md): De Analyse van activiteitenlogboek oplossing helpt bij het analyseren van de activiteiten logboeken van Azure in alle Azure-abonnementen voor een klant.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) winkels, uitvoeren en beheren van runbooks. In deze oplossing helpen runbooks bij het verzamelen van logboeken van Azure SQL Database. Met de oplossing Automation [Wijzigingen bijhouden](../../automation/change-tracking.md) kunnen klanten eenvoudig wijzigingen in de omgeving identificeren.

**Azure monitor**: [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) helpt gebruikers bij het volgen van prestaties, het onderhouden van de beveiliging en het identificeren van trends door organisaties in te scha kelen, waarschuwingen te maken en gegevens te archiveren, met inbegrip van tracking-API-aanroepen in hun Azure-resources.

**Application Insights**: [Application Insights](../../azure-monitor/app/app-insights-overview.md) is een uitbreid bare service voor het beheer van toepassings prestaties voor webontwikkelaars op meerdere platforms. Application Insights detecteert prestatie afwijkingen en klanten kunnen deze gebruiken om de Live webtoepassing te bewaken. Het bevat krachtige analyse tools om klanten te helpen bij het onderzoeken van problemen en om te begrijpen wat gebruikers daad werkelijk doen met hun app. Het&#39;is ontworpen om klanten voortdurend te helpen de prestaties en bruikbaarheid te verbeteren.

## <a name="threat-model"></a>Bedreigings model

Het gegevensstroom diagram voor deze referentie architectuur is beschikbaar voor [downloaden](https://aka.ms/pcidss-paaswa-tm) of kan hieronder worden weer gegeven. Dit model kan klanten helpen om inzicht te krijgen in de punten van potentieel risico in de systeem infrastructuur bij het aanbrengen van wijzigingen.

![PaaS-webtoepassing voor PCI DSS Threat model](images/pcidss-paaswa-threat-model.png "PaaS-webtoepassing voor PCI DSS Threat model")

## <a name="compliance-documentation"></a>Documentatie voor naleving

De [Azure-blauwdruk voor beveiliging en naleving-PCI DSS matrix van de klant verantwoordelijkheid](https://aka.ms/pcidss-crm) bevat een lijst met controller-en processor verantwoordelijkheden voor alle PCI DSS 3,2-vereisten.

De [Azure-blauwdruk voor beveiliging en naleving-PCI DSS PaaS Web Application implementation-matrix](https://aka.ms/pcidss-paaswa-cim) bevat informatie over welke PCI DSS 3,2-vereisten worden verholpen door de architectuur van de Paas-webtoepassing, met inbegrip van gedetailleerde beschrijvingen van de manier waarop de implementatie voldoet aan de vereisten van elk gedekt artikel.

## <a name="deploy-this-solution"></a>Deze oplossing implementeren
Deze Azure-blauwdruk voor beveiliging en naleving automatisering bestaat uit JSON-configuratie bestanden en Power shell-scripts die worden verwerkt door de API-service van Azure Resource Manager om resources te implementeren in Azure. Gedetailleerde implementatie-instructies zijn [hier](https://aka.ms/pcidss-paaswa-repo)beschikbaar.

#### <a name="quickstart"></a>Quick Start
1. Kloon of down load [deze](https://aka.ms/pcidss-paaswa-repo) github-opslag plaats naar uw lokale werk station.

2. Bekijk 0-Setup-AdministrativeAccountAndPermission.md en voer de meegeleverde opdrachten uit.

3. Implementeer een test oplossing met de voorbeeld gegevens van Contoso of test een eerste productie omgeving.
   - 1A-ContosoWebStoreDemoAzureResources.ps1
     - Met dit script worden Azure-resources geïmplementeerd voor een demonstratie van een webstore met behulp van contoso-voorbeeld gegevens.
   - 1-DeployAndConfigureAzureResources.ps1
     - Met dit script implementeert u de Azure-resources die nodig zijn voor de ondersteuning van een productie omgeving voor een webtoepassing die eigendom is van een klant. Deze omgeving moet verder worden aangepast door de klant op basis van de vereisten van de organisatie.

## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen

### <a name="vpn-and-expressroute"></a>VPN-en ExpressRoute

Een beveiligde VPN-tunnel of [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) moet worden geconfigureerd om een veilige verbinding tot stand te brengen met de resources die zijn geïmplementeerd als onderdeel van deze PaaS-referentie architectuur voor webtoepassingen. Door een VPN-of ExpressRoute in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

Door een beveiligde VPN-tunnel te implementeren met Azure, kan een virtuele particuliere verbinding tussen een on-premises netwerk en een Azure-Virtual Network worden gemaakt. Deze verbinding vindt plaats via internet en biedt klanten de mogelijkheid om &quot;gegevens&quot; in een versleutelde koppeling tussen het&#39;netwerk van de klant en Azure veilig te tunnelen. Site-naar-site-VPN is een veilige, rijpere technologie die is geïmplementeerd door ondernemingen van elke omvang voor tien tallen. De [IPSec-tunnel modus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wordt in deze optie als een versleutelings mechanisme gebruikt.

Omdat verkeer binnen de VPN-tunnel via internet met een site-naar-site-VPN gaat, biedt micro soft een andere, nog veiliger verbindings optie. Azure ExpressRoute is een specifieke WAN-verbinding tussen Azure en een on-premises locatie of een Exchange-hosting provider. Omdat ExpressRoute-verbindingen niet via internet werken, bieden deze verbindingen meer betrouw baarheid, hogere snelheden, lagere latenties en een betere beveiliging dan typische verbindingen via internet. Omdat dit een directe verbinding is tussen de telecommunicatie&#39;provider van de klant, worden de gegevens niet via internet verzonden en worden ze daarom niet blootgesteld aan de service.

Aanbevolen procedures voor het implementeren van een veilig hybride netwerk dat een on-premises netwerk uitbreidt naar Azure, zijn [beschikbaar](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Vrijwaring

- Dit document is alleen ter informatie bedoeld. MICRO SOFT BIEDT GEEN ENKELE GARANTIE, UITDRUKKELIJK, IMPLICIET OF WETTELIJK, MET BETREKKING TOT DE INFORMATIE IN DIT DOCUMENT. Dit document wordt aangegeven &quot;als-is.&quot; Informatie en weer gaven in dit document, inclusief URL'S en andere website verwijzingen, kunnen zonder kennisgeving worden gewijzigd. Klanten die dit document lezen, hebben het risico van het gebruik ervan.
- Dit document biedt klanten geen juridische rechten voor intellectueel eigendom in een micro soft-product of-oplossingen.
- Klanten kunnen dit document kopiëren en gebruiken voor interne referentie doeleinden.
- Bepaalde aanbevelingen in dit document kunnen leiden tot meer gegevens-, netwerk-of COMPUTE-resource gebruik in Azure en kunnen de&#39;kosten voor Azure-licenties of-abonnementen van klanten verhogen.
- Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.
- Dit document is ontwikkeld als referentie en mag niet worden gebruikt voor het definiëren van de manier waarop een klant kan voldoen aan specifieke nalevings vereisten en-voor Schriften. Klanten moeten juridische ondersteuning van hun organisatie zoeken op goedgekeurde klant implementaties.
