---
title: Azure-blauwdruk voor beveiliging en naleving-PaaS-webtoepassing voor FedRAMP
description: Azure-blauwdruk voor beveiliging en naleving-PaaS-webtoepassing voor FedRAMP
services: security
author: jomolesk
ms.assetid: 41570fc1-4d74-48ed-afb0-ef1be857029e
ms.service: security
ms.topic: article
ms.date: 06/01/2018
ms.author: jomolesk
ms.openlocfilehash: e8458d505575a5bf39bbd0a9970c5044b9cbd0f0
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257329"
---
# <a name="azure-security-and-compliance-blueprint-paas-web-application-for-fedramp"></a>Azure-blauwdruk voor beveiliging en naleving: PaaS-webtoepassing voor FedRAMP

## <a name="overview"></a>Overzicht

De [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov/) is een Verenigde Staten overheids programma dat een gestandaardiseerde benadering biedt voor de beveiligings beoordeling, de autorisatie en de voortdurende bewaking van Cloud producten en-services. Deze Azure-blauwdruk voor beveiliging en naleving biedt richt lijnen voor het leveren van een Microsoft Azure Platform as a Service-architectuur (PaaS) die helpt bij het implementeren van een subset van FedRAMP High Controls. Deze oplossing biedt richt lijnen voor de implementatie en configuratie van Azure-resources voor een algemene referentie architectuur, waarbij u kunt zien hoe klanten aan specifieke vereisten voor beveiliging en naleving kunnen voldoen en als basis voor klanten worden gebruikt om bouw en configureer hun eigen oplossingen op Azure.

Deze referentie architectuur, de bijbehorende beheer implementatie handleidingen en bedreigings modellen zijn bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mogen niet worden gebruikt als in een productie omgeving. Het implementeren van een toepassing in deze omgeving zonder aanpassing is niet voldoende om volledig te voldoen aan de vereisten van de FedRAMP hoge basis lijn. Houd rekening met het volgende:
- De architectuur biedt een basis lijn om klanten te helpen bij het implementeren van werk belastingen naar Azure op een FedRAMP.
- Klanten zijn verantwoordelijk voor het uitvoeren van passende beveiligings-en nalevings beoordelingen van alle oplossingen die zijn gebouwd met behulp van deze architectuur, aangezien de vereisten kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

## <a name="architecture-diagram-and-components"></a>Architectuur diagram en onderdelen
Deze oplossing biedt een referentie architectuur voor een PaaS-webtoepassing met een Azure SQL Database back-end. De webtoepassing wordt gehost in een geïsoleerde Azure App Service Environment, een persoonlijke, toegewezen omgeving in een Azure-Data Center. De omgevings taak verdeelt het verkeer voor de webtoepassing op virtuele machines die worden beheerd door Azure. Deze architectuur omvat ook netwerk beveiligings groepen, een Application Gateway, Azure DNS en Load Balancer. Daarnaast biedt Azure Monitor een realtime analyse van de systeem status. **Azure raadt aan om een VPN-of ExpressRoute-verbinding te configureren voor beheer en gegevens import in het subnet met referentie architectuur.**

![PaaS-webtoepassing voor FedRAMP-referentie architectuur diagram](images/fedramp-paaswa-architecture.png?raw=true "PaaS-webtoepassing voor FedRAMP-referentie architectuur diagram")

Deze oplossing maakt gebruik van de volgende Azure-Services. Details van de implementatie architectuur vindt u in de sectie [implementatie architectuur](#deployment-architecture) .

- Azure Active Directory
- Azure Key Vault
- Azure SQL Database
- Application Gateway
    - (1) Web Application firewall
        - Firewall modus: voor komen
        - Regelset: OWASP 3,0
        - Listener: poort 443
- Virtueel Azure-netwerk
- Netwerkbeveiligingsgroepen
- Azure DNS
- Azure Storage
- Azure Monitor
- App Service Environment v2
- Azure Load Balancer
- Azure Web App
- Azure Resource Manager

## <a name="deployment-architecture"></a>Implementatie architectuur
De volgende sectie bevat informatie over de implementatie-en implementatie-elementen.

**Azure Resource Manager**: Met [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) kunnen klanten samen werken met de resources in de oplossing als groep. Klanten kunnen alle resources voor de oplossing implementeren, bijwerken of verwijderen in een enkele, gecoördineerde bewerking. Klanten gebruiken een sjabloon voor implementatie en die sjabloon kan worden gebruikt voor verschillende omgevingen, zoals testen, faseren en productie. Resource Manager biedt functies voor beveiliging, controle en labeling om klanten te helpen bij het beheren van hun resources na de implementatie.

**App service Environment v2**: De [Azure app service Environment (ASE)](https://docs.microsoft.com/azure/app-service/environment/intro) is een app service-functie die een volledig geïsoleerde en toegewezen omgeving biedt voor het veilig uitvoeren van app service toepassingen op een hoge schaal.

As zijn alleen geïsoleerd voor het uitvoeren van de toepassingen van één klant en worden altijd geïmplementeerd in een virtueel netwerk. Klanten hebben nauw keurige controle over zowel binnenkomend als uitgaand netwerk verkeer en toepassingen kunnen snelle, veilige verbindingen tot stand brengen via virtuele netwerken naar on-premises bedrijfs bronnen.

Het gebruik van as voor deze architectuur is toegestaan voor de volgende besturings elementen/configuraties:

- Host binnen een beveiligde Azure-Virtual Network en netwerk beveiligings regels
- ASE geconfigureerd met zelfondertekend ILB-certificaat voor HTTPS-communicatie
- [Modus voor interne taak verdeling](../../app-service/environment/app-service-environment-with-internal-load-balancer.md)
- [TLS 1,0](../../app-service/environment/app-service-app-service-environment-custom-settings.md) uitschakelen
- [TLS-code ring](../../app-service/environment/app-service-app-service-environment-custom-settings.md) wijzigen
- [Inkomend verkeer voor N/W-poorten](../../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md) beheren
- [Web Application firewall: gegevens beperken](../../app-service/environment/app-service-app-service-environment-web-application-firewall.md)
- [Azure SQL database verkeer](../../app-service/environment/app-service-app-service-environment-network-architecture-overview.md) toestaan

De sectie [instructies en aanbevelingen](#guidance-and-recommendations) bevat aanvullende informatie over as.

**Azure-web-app**: Met [Azure app service](https://docs.microsoft.com/azure/app-service/) kunnen klanten webtoepassingen bouwen en hosten in de programmeer taal van hun keuze zonder dat de infra structuur hoeft te worden beheerd. Het biedt automatisch schalen en een hoge beschikbaarheid, ondersteuning voor zowel Windows als Linux en maakt automatische implementaties mogelijk vanuit GitHub, Azure DevOps of een willekeurige Git-repo.

### <a name="virtual-network"></a>Virtueel netwerk
De architectuur definieert een particulier virtueel netwerk met een adres ruimte van 10.200.0.0/16.

**Netwerk beveiligings groepen**: [Netwerk beveiligings groepen (nsg's)](../../virtual-network/virtual-network-vnet-plan-design-arm.md) bevatten toegangs beheer lijsten waarmee verkeer binnen een virtueel netwerk wordt toegestaan of geweigerd. Nsg's kan worden gebruikt om verkeer op een subnet of een afzonderlijk VM-niveau te beveiligen. De volgende Nsg's bestaan:
- 1 NSG voor Application Gateway
- 1 NSG voor App Service Environment
- 1 NSG voor Azure SQL Database

Voor elk van de Nsg's zijn specifieke poorten en protocollen geopend, zodat de oplossing veilig en goed werkt. Daarnaast zijn de volgende configuraties ingeschakeld voor elke NSG:
  - [Diagnostische logboeken en gebeurtenissen](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) worden ingeschakeld en opgeslagen in een opslag account
  - Azure Monitor-logboeken zijn verbonden met de [Diagnostische gegevens van de NSG](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json)

**Subnetten**: Elk subnet is gekoppeld aan de bijbehorende NSG.

**Azure DNS**: De Domain Name System, of DNS, is verantwoordelijk voor het vertalen van een website-of service naam naar het IP-adres. [Azure DNS](https://docs.microsoft.com/azure/dns/dns-overview) is een hosting service voor DNS-domeinen die naam omzetting biedt met behulp van Azure-infra structuur. Door domeinen in azure te hosten, kunnen gebruikers DNS-records beheren met dezelfde referenties, Api's, hulpprogram ma's en facturering als andere Azure-Services. Azure DNS biedt ook ondersteuning voor privé-DNS-domeinen.

**Azure Load Balancer**: Met [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) kunnen klanten hun toepassingen schalen en hoge Beschik baarheid maken voor services. Load Balancer ondersteunt zowel binnenkomende als uitgaande scenario's, en biedt lage latentie, hoge door Voer en schaalbaar tot miljoenen stromen voor alle TCP-en UDP-toepassingen.

### <a name="data-in-transit"></a>Actieve gegevens
Azure versleutelt standaard alle communicatie naar en van Azure-data centers. Alle trans acties die via de Azure Portal worden Azure Storage, vindt plaats via HTTPS.

### <a name="data-at-rest"></a>Data-at-rest
De architectuur beveiligt gegevens op rest door versleuteling, database controle en andere metingen.

**Azure Storage**: Om te voldoen aan de versleutelde gegevens op rest-vereisten, gebruikt alle [Azure Storage](https://azure.microsoft.com/services/storage/) [Storage service Encryption](../../storage/common/storage-service-encryption.md).

AzureDiskEncryption
[Azure Disk Encryption](../azure-security-disk-encryption-overview.md) maakt gebruik van de BitLocker-functie van Windows om volume versleuteling voor gegevens schijven te bieden. De oplossing kan worden geïntegreerd met Azure Key Vault om de versleutelings sleutels voor de schijf te controleren en te beheren.

**Azure SQL Database**: Het Azure SQL Database exemplaar maakt gebruik van de volgende data base Security-maat eenheden:
-   [Ad-verificatie en-autorisatie](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) maken het identiteits beheer mogelijk van database gebruikers en andere micro soft-Services op één centrale locatie.
-   Met [SQL database controle](../../sql-database/sql-database-auditing.md) worden database gebeurtenissen bijgehouden en naar een audit logboek in een Azure-opslag account geschreven.
-   Azure SQL Database is geconfigureerd voor het gebruik van [transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), waarmee de data base, gekoppelde back-ups en transactie logboek bestanden in realtime kunnen worden geversleuteld en ontsleuteld om informatie te beveiligen.
-   [Firewall regels](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor komen dat alle toegang tot database servers tot de juiste machtigingen worden verleend. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt.
-   Met de [detectie van SQL-bedreigingen](../../sql-database/sql-database-threat-detection.md) kunnen mogelijke dreigingen worden gedetecteerd en gereageerd als ze optreden door beveiligings waarschuwingen te bieden voor verdachte database activiteiten, potentiële kwetsbaar heden, SQL-injectie aanvallen en afwijkende database toegangs patronen.
-   [Always encrypted kolommen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zorgen ervoor dat gevoelige gegevens nooit als tekst zonder opmaak in het database systeem worden weer gegeven. Na het inschakelen van gegevens versleuteling, hebben alleen client toepassingen of toepassings servers met toegang tot de sleutels toegang tot tekst zonder opmaak.
- Met [beveiliging op rijniveau](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) kunnen gebruikers beleid definiëren om de toegang tot gegevens te beperken om de verwerking te stoppen.
- [SQL database dynamische gegevens maskering](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) kunnen worden uitgevoerd nadat de referentie architectuur is geïmplementeerd. Klanten moeten de instellingen voor dynamische gegevens maskering aanpassen om te voldoen aan hun database schema.

### <a name="identity-management"></a>Identiteitsbeheer
De volgende technologieën bieden mogelijkheden voor identiteits beheer in de Azure-omgeving:
-   [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) is de multi tenant-Cloud Directory en identiteits beheer service van micro soft. Alle gebruikers voor deze oplossing worden gemaakt in AAD, met inbegrip van gebruikers die toegang hebben tot de Azure SQL Database.
-   Verificatie voor de toepassing wordt uitgevoerd met behulp van AAD. Zie [toepassingen integreren met Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)voor meer informatie. Daarnaast maakt de database kolom versleuteling gebruik van Azure Active Directory om de toepassing te verifiëren voor Azure SQL Database. Zie voor meer informatie hoe u [gevoelige gegevens in Azure SQL database kunt beveiligen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
-   [Met op rollen gebaseerd toegangs](../../role-based-access-control/role-assignments-portal.md) beheer van Azure kunt u nauw keurig toegang krijgen tot Azure. Abonnements toegang is beperkt tot de abonnements beheerder en de toegang tot bronnen kan worden beperkt op basis van de gebruikersrol.
- Met [Azure Active Directory privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) kunnen klanten het aantal gebruikers dat toegang heeft tot bepaalde informatie minimaliseren.  Beheerders kunnen AAD-Privileged Identity Management gebruiken om bevoegde identiteiten en hun toegang tot bronnen te detecteren, beperken en controleren. Deze functie kan ook worden gebruikt voor het afdwingen van alleen-in-time-beheer toegang op aanvraag als dat nodig is.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecteert mogelijke beveiligings problemen die invloed hebben op de identiteiten van een organisatie, configureert automatische antwoorden op gedetecteerde verdachte acties die betrekking hebben op de identiteit van een organisatie en onderzoeken verdachte incidenten om deze problemen op te lossen.

### <a name="security"></a>Beveiliging
**Geheimen beheren** De oplossing maakt gebruik van [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) voor het beheer van sleutels en geheimen. Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Met de volgende Azure Key Vault mogelijkheden kunnen klanten gegevens en toegang tot gegevens beveiligen:
- Geavanceerd toegangs beleid wordt geconfigureerd op basis van behoefte.
- Key Vault toegangs beleid wordt gedefinieerd met mini maal vereiste machtigingen voor sleutels en geheimen.
- Alle sleutels en geheimen in Key Vault hebben verloop datums.
- Alle sleutels in Key Vault worden beveiligd door gespecialiseerde hardware security modules (Hsm's). Het sleutel type is een met HSM beschermde 2048-bits RSA-sleutel.
- Aan alle gebruikers en identiteiten worden mini maal vereiste machtigingen verleend met toegangs beheer op basis van rollen.
- Diagnostische logboeken voor Key Vault zijn ingeschakeld met een Bewaar periode van ten minste 365 dagen.
- Toegestane cryptografische bewerkingen voor sleutels zijn beperkt tot de vereisten die nodig zijn.

**Application Gateway** De architectuur vermindert het risico op beveiligings problemen met behulp van een Application Gateway met Web Application firewall en de ruleset OWASP ingeschakeld. Aanvullende mogelijkheden zijn onder andere:
- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL-offload](../../application-gateway/create-ssl-portal.md) inschakelen
- [TLS v 1.0 en v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) uitschakelen
- [Web Application firewall](../../application-gateway/waf-overview.md)
- [Preventie modus](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) met OWASP 3,0 ruleSet
- [Logboek registratie van diagnostische gegevens](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics) inschakelen
- [Aangepaste status tests](../../application-gateway/quick-create-portal.md)
- [Azure Security Center](https://azure.microsoft.com/services/security-center) en [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-security-recommendations) bieden extra beveiliging en meldingen. Azure Security Center biedt ook een reputatie systeem.

### <a name="logging-and-auditing"></a>Logboekregistratie en bewaking
Azure Monitor biedt uitgebreide logboek registratie van systeem-en gebruikers activiteiten, evenals de systeem status. Het verzamelt en analyseert gegevens die zijn gegenereerd door resources in Azure-en on-premises omgevingen.
- **Activiteiten logboeken**: [Activiteiten logboeken](../../azure-monitor/platform/activity-logs-overview.md) bieden inzicht in bewerkingen die worden uitgevoerd op resources in een abonnement. Activiteiten logboeken kunnen helpen bij het bepalen van de initiator, het tijdstip van de gebeurtenis en de status van een bewerking.
- **Diagnostische logboeken**: [Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md) bevatten alle logboeken die elke resource heeft verzonden. Deze logboeken bevatten Windows-gebeurtenis systeem logboeken, Azure Storage logboeken, Key Vault controle logboeken en Application Gateway toegang en firewall Logboeken.
- **Archivering van Logboeken**: Alle Diagnostische logboeken schrijven naar een gecentraliseerd en versleuteld Azure Storage-account voor archivering. De retentie kan door de gebruiker worden geconfigureerd, tot 730 dagen, om te voldoen aan de specifieke vereisten voor het bewaren van een organisatie. Deze logboeken maken verbinding met Azure Monitor logboeken voor verwerking, opslag en dashboard rapportage.

Daarnaast zijn de volgende bewakings oplossingen opgenomen als onderdeel van deze architectuur:
-   [Active Directory-evaluatie](../../azure-monitor/insights/ad-assessment.md): De Active Directory Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval en biedt een lijst met aanbevelingen die specifiek zijn voor de geïmplementeerde server infrastructuur.
-   [Anitmalware-evaluatie](../../security-center/security-center-install-endpoint-protection.md): De antimalware-oplossing rapporteert over malware, bedreigingen en de beveiligings status.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Met de Azure Automation oplossing worden runbooks opgeslagen, uitgevoerd en beheerd. In deze oplossing helpen runbooks bij het verzamelen van logboeken van Application Insights en Azure SQL Database.
-   [Beveiliging en audit](../../security-center/security-center-intro.md): Het Beveiliging en audit-dash board biedt een hoog niveau inzicht in de beveiligings status van bronnen door metrische gegevens op te geven over beveiligings domeinen, belang rijke problemen, detecties, bedreigings informatie en algemene beveiligings query's.
-   [SQL-evaluatie](../../azure-monitor/insights/sql-assessment.md): De SQL Health Check-oplossing evalueert het risico en de status van de server omgevingen met een regel matig interval en biedt klanten een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
-   [Updatebeheer](../../automation/automation-update-management.md): Met de Updatebeheer oplossing kan het klant beheer van beveiligings updates van besturings systemen worden uitgevoerd, inclusief de status van beschik bare updates en het proces voor het installeren van vereiste updates.
-   [Status van agent](../../monitoring/monitoring-solution-agenthealth.md): De Status van agent oplossing meldt hoeveel agents zijn geïmplementeerd en wat hun geografische distributie zijn, en hoeveel agents niet reageren en het aantal agents dat operationele gegevens verzendt.
-   [Azure-activiteiten logboeken](../../azure-monitor/platform/collect-activity-logs.md): De Analyse van activiteitenlogboek oplossing helpt bij het analyseren van de activiteiten logboeken van Azure in alle Azure-abonnementen voor een klant.
-   [Wijzigingen bijhouden](../../automation/change-tracking.md): Met de Wijzigingen bijhouden oplossing kunnen klanten eenvoudig wijzigingen in de omgeving identificeren.

Azuremonitor
[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) helpt gebruikers bij het volgen van prestaties, het onderhouden van de beveiliging en het identificeren van trends door organisaties in staat te stellen om gegevens te controleren, te maken en te archiveren, inclusief het volgen van API-aanroepen in azure-bronnen van klanten.

## <a name="threat-model"></a>Bedreigings model

Het gegevensstroom diagram voor deze referentie architectuur is beschikbaar voor [downloaden](https://aka.ms/fedrampPaaSWebAppDFD) of kan hieronder worden weer gegeven. Dit model kan klanten helpen om inzicht te krijgen in de punten van potentieel risico in de systeem infrastructuur bij het aanbrengen van wijzigingen.

![PaaS-webtoepassing voor FedRAMP Threat model](images/fedramp-paaswa-threat-model.png?raw=true "PaaS-webtoepassing voor FedRAMP Threat model")

## <a name="compliance-documentation"></a>Documentatie voor naleving
De [FedRAMP-matrix voor hoge klant verantwoordelijkheden bevat Azure-blauwdruk voor beveiliging en naleving](https://aka.ms/blueprinthighcrm) een lijst met alle beveiligings controles die vereist zijn voor de hoge basis lijn van FedRAMP. De matrix geeft aan of de implementatie van elk besturings element de verantwoordelijkheid is van micro soft, de klant of gedeeld door de twee.

In [Azure-blauwdruk voor beveiliging en naleving de FedRAMP PaaS webapp-implementatie matrix voor hoge controle](https://aka.ms/fedrampPaaSWebAppCIM) worden alle beveiligings controles vermeld die zijn vereist voor de FedRAMP hoge basis lijn. De matrix bevat informatie over welke besturings elementen worden gedekt door de architectuur van de PaaS-webtoepassing, met inbegrip van gedetailleerde beschrijvingen van de manier waarop de implementatie voldoet aan de vereisten van elk gedekte besturings element.

## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen
### <a name="vpn-and-expressroute"></a>VPN-en ExpressRoute
Een beveiligde VPN-tunnel of [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) moet worden geconfigureerd om een veilige verbinding tot stand te brengen met de resources die zijn geïmplementeerd als onderdeel van deze PaaS-referentie architectuur voor webtoepassingen. Door een VPN-of ExpressRoute in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

Als u een beveiligde VPN-tunnel met Azure implementeert, kan een VPN-verbinding tussen een on-premises netwerk en een Azure-Virtual Network worden gemaakt. Deze verbinding vindt plaats via internet en stelt klanten in staat om ' tunnel-informatie veilig ' te maken binnen een versleutelde koppeling tussen het netwerk van de klant en Azure. Site-naar-site-VPN is een veilige, rijpere technologie die is geïmplementeerd door ondernemingen van elke omvang voor tien tallen. De [IPSec-tunnel modus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wordt in deze optie als een versleutelings mechanisme gebruikt.

Omdat verkeer binnen de VPN-tunnel via internet met een site-naar-site-VPN gaat, biedt micro soft een andere, nog veiliger verbindings optie. Azure ExpressRoute is een specifieke WAN-verbinding tussen Azure en een on-premises locatie of een Exchange-hosting provider. Omdat ExpressRoute-verbindingen niet via internet werken, bieden deze verbindingen meer betrouw baarheid, hogere snelheden, lagere latenties en een betere beveiliging dan typische verbindingen via internet. Omdat dit een directe verbinding is tussen de telecom municatie-provider van de klant, worden de gegevens niet via internet verzonden en worden ze daarom niet blootgesteld aan IT.

Aanbevolen procedures voor het implementeren van een veilig hybride netwerk dat een on-premises netwerk uitbreidt naar Azure, zijn [beschikbaar](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Vrijwaring

 - Dit document is alleen ter informatie bedoeld. MICRO SOFT BIEDT GEEN ENKELE GARANTIE, UITDRUKKELIJK, IMPLICIET OF WETTELIJK, MET BETREKKING TOT DE INFORMATIE IN DIT DOCUMENT. Dit document wordt in de as-is opgenomen. Informatie en weer gaven in dit document, inclusief URL'S en andere website verwijzingen, kunnen zonder kennisgeving worden gewijzigd. Klanten die dit document lezen, hebben het risico van het gebruik ervan.
 - Dit document biedt klanten geen juridische rechten voor intellectueel eigendom in een micro soft-product of-oplossingen.
 - Klanten kunnen dit document kopiëren en gebruiken voor interne referentie doeleinden.
 - Bepaalde aanbevelingen in dit document kunnen leiden tot meer gegevens-, netwerk-of COMPUTE-resource gebruik in azure, en kunnen de kosten van de Azure-licentie of het abonnement van de klant verhogen.
 - Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.
 - Dit document is ontwikkeld als referentie en mag niet worden gebruikt voor het definiëren van de manier waarop een klant kan voldoen aan specifieke nalevings vereisten en-voor Schriften. Klanten moeten juridische ondersteuning van hun organisatie zoeken op goedgekeurde klant implementaties.
