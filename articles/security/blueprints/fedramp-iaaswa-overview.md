---
title: Azure-blauwdruk voor beveiliging en naleving-IaaS-webtoepassing voor FedRAMP
description: Azure-blauwdruk voor beveiliging en naleving-IaaS-webtoepassing voor FedRAMP
services: security
documentationcenter: na
author: jomolesk
manager: barbkess
editor: tomsh
ms.assetid: 8fe47cff-9c24-49e0-aa11-06ff9892a468
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2018
ms.author: jomolesk
ms.openlocfilehash: bcce4dcac35b783efefe81abc2090506502e9931
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257280"
---
# <a name="azure-security-and-compliance-blueprint-iaas-web-application-for-fedramp"></a>Azure-blauwdruk voor beveiliging en naleving: IaaS-webtoepassing voor FedRAMP

## <a name="overview"></a>Overzicht

De [Federal Risk and Authorization Management Program (FedRAMP)](https://www.fedramp.gov) is een Amerikaanse overheid-programma dat een gestandaardiseerde benadering biedt voor de beveiligings beoordeling, de autorisatie en de voortdurende bewaking van Cloud producten en-services. Deze Azure-blauwdruk voor beveiliging en naleving automatisering biedt richt lijnen voor de implementatie van een FedRAMP-compatibele IaaS-omgeving (Infrastructure as a Service) die geschikt is voor een eenvoudige webtoepassing op internet. Met deze oplossing automatiseert u de implementatie en configuratie van Azure-resources voor een algemene referentie architectuur, waarbij u kunt zien hoe klanten aan specifieke vereisten voor beveiliging en naleving voldoen en als basis voor de ontwikkeling van klanten kunnen worden gebruikt en Configureer hun eigen oplossingen op Azure. De oplossing implementeert een subset van besturings elementen uit de FedRAMP High baseline, op basis van NIST SP 800-53. Zie de [documentatie voor naleving](#compliance-documentation)voor meer informatie over FedRAMP-vereisten en deze oplossing.
> [!NOTE]
> Deze oplossing wordt geïmplementeerd in Azure Government.

Deze Azure-blauwdruk voor beveiliging en naleving automatisering implementeert automatisch een referentie architectuur van een IaaS-webtoepassing met vooraf geconfigureerde beveiligings controles zodat klanten kunnen voldoen aan de vereisten van FedRAMP. De oplossing bestaat uit Azure Resource Manager sjablonen en Power shell-scripts die de implementatie en configuratie van resources begeleiden.

Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving. Het implementeren van een toepassing in deze omgeving zonder wijziging is niet voldoende om volledig te voldoen aan de vereisten van de FedRAMP hoge basis lijn. Houd rekening met het volgende:
- Deze architectuur biedt een basis lijn waarmee klanten Azure op een FedRAMP manier kunnen gebruiken.
- Klanten zijn verantwoordelijk voor het uitvoeren van de juiste beveiligings-en compatibiliteits beoordeling van alle oplossingen die zijn gebouwd met behulp van deze architectuur, aangezien de vereisten kunnen variëren afhankelijk van de specifieke implementaties van elke klant.

Bekijk deze [video](https://aka.ms/fedrampblueprintvideo) waarin de implementatie van deze oplossing wordt uitgelegd en hoe deze werkt, voor een beknopt overzicht van de werking ervan.

Klik [hier](https://aka.ms/fedrampblueprintrepo) voor implementatie-instructies.

## <a name="architecture-diagram-and-components"></a>Architectuur diagram en onderdelen
Met deze oplossing wordt een referentie architectuur voor een IaaS-webtoepassing met een SQL Server back-end geïmplementeerd. De architectuur bevat een weblaag, gegevenslaag, Active Directory-infra structuur, Application Gateway en Load Balancer. Virtuele machines die zijn geïmplementeerd op de web-en gegevenslaagsen, worden geconfigureerd in een Beschikbaarheidsset en SQL Server exemplaren worden geconfigureerd in een AlwaysOn-beschikbaarheids groep voor hoge Beschik baarheid. Virtuele machines zijn lid van een domein en Active Directory groeps beleid wordt gebruikt voor het afdwingen van beveiligings-en nalevings configuraties op het niveau van het besturings systeem. Een bastion-host biedt een beveiligde verbinding voor beheerders om toegang te krijgen tot geïmplementeerde bronnen. **U wordt aangeraden een VPN-of Azure ExpressRoute-verbinding te configureren voor beheer en gegevens import in het subnet met de referentie architectuur.**

![IaaS-webtoepassing voor FedRAMP-referentie architectuur diagram](images/fedramp-iaaswa-architecture.png?raw=true "IaaS-webtoepassing voor FedRAMP-referentie architectuur diagram")

Deze oplossing maakt gebruik van de volgende Azure-Services. Details van de implementatie architectuur vindt u in de sectie [implementatie architectuur](#deployment-architecture) .

- Azure Virtual Machines
    - (1) bastion-host (Windows Server 2016 Data Center)
    - (2) Active Directory domein controller (Windows Server 2016 Data Center)
    - (2) SQL Server cluster knooppunt (SQL Server 2017 op Windows Server 2016)
    - (2) Web/IIS (Windows Server 2016 Data Center)
- Beschikbaarheidssets
    - (1) Active Directory domein controllers
    - (1) SQL-cluster knooppunten
    - (1) Web/IIS
- Azure Virtual Network
    - (1)/16 virtuele netwerken
    - (5)/24 subnetten
    - DNS-instellingen worden ingesteld op beide domein controllers
- Azure Load Balancer
- Azure Application Gateway
    - (1) WAF-Application Gateway ingeschakeld
        - Firewall modus: voor komen
        - Regelset: OWASP 3,0
        - Listener: poort 443
- Azure Storage
    - (7) geografisch redundante opslag accounts
- Azure-Cloudwitness
- Recovery Services-kluis
- Azure Key Vault
- Azure Active Directory (Azure AD)
- Azure Resource Manager
- Azure Monitor (Logboeken)

## <a name="deployment-architecture"></a>Implementatie architectuur

De volgende sectie bevat informatie over de ontwikkelings-en implementatie-elementen.

**Bastion-host**: De bastion-host is het enige toegangs punt dat beheerders een beveiligde verbinding biedt om toegang te krijgen tot geïmplementeerde bronnen. De Bastion-NSG van de host staat alleen verbindingen toe op TCP-poort 3389 voor RDP. Klanten kunnen de bastion-host verder configureren om te voldoen aan de vereisten voor de systeem beveiliging van uw organisatie.

### <a name="virtual-network"></a>Virtueel netwerk
De architectuur definieert een particulier virtueel netwerk met een adres ruimte van 10.200.0.0/16.

**Netwerk beveiligings groepen**: Met deze oplossing worden bronnen geïmplementeerd in een architectuur met een afzonderlijk websubnet, een Data Base-subnet, Active Directory subnet en het beheer subnet in een virtueel netwerk. Subnetten worden logisch gescheiden door de regels voor de netwerk beveiligings groep die worden toegepast op de afzonderlijke subnetten om het verkeer tussen subnetten alleen te beperken tot de gegevens die nodig zijn voor systeem-en beheer functionaliteit.

Zie de configuratie voor [netwerk beveiligings groepen](https://github.com/Azure/fedramp-iaas-webapp/blob/master/nestedtemplates/virtualNetworkNSG.json) die met deze oplossing zijn geïmplementeerd. Klanten kunnen netwerk beveiligings groepen configureren door het bovenstaande bestand te bewerken met behulp van [deze documentatie](../../virtual-network/virtual-network-vnet-plan-design-arm.md) als richt lijn.

Elk van de subnetten heeft een toegewezen netwerk beveiligings groep (NSG):
- 1 NSG voor Application Gateway (LBNSG)
- 1 NSG voor Bastion-host (MGTNSG)
- 1 NSG voor primaire en back-updomein controllers (ADNSG)
- 1 NSG voor SQL-servers (SQLNSG)
- 1 NSG voor de weblaag (WEBNSG)

**Subnetten**: Elk subnet is gekoppeld aan de bijbehorende NSG.

### <a name="data-at-rest"></a>Data-at-rest

De architectuur beveiligt de gegevens op rest door gebruik te maken van verschillende versleutelings maatregelen.

**Azure Storage**: Om te voldoen aan de vereisten voor het versleutelen van gegevens op rest, gebruiken alle opslag accounts [Storage service Encryption](https://docs.microsoft.com/azure/storage/common/storage-service-encryption).

**SQL Server**: SQL Server is geconfigureerd voor het gebruik van [transparent Data Encryption (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption), waarmee in realtime versleuteling en ontsleuteling van gegevens en logboek bestanden worden uitgevoerd om informatie te beveiligen. TDE biedt zekerheid dat opgeslagen gegevens niet zijn onderworpen aan onbevoegde toegang.

Klanten kunnen ook de volgende SQL Server beveiligings maatregelen configureren:
-   [Ad-verificatie en-autorisatie](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) maken het identiteits beheer mogelijk van database gebruikers en andere micro soft-Services op één centrale locatie.
-   Met [SQL database controle](../../sql-database/sql-database-auditing.md) worden database gebeurtenissen bijgehouden en naar een audit logboek in een Azure-opslag account geschreven.
-   [Firewall regels](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) voor komen dat alle toegang tot database servers tot de juiste machtigingen worden verleend. De firewall verleent toegang tot databases op basis van het IP-adres waar de aanvraag vandaan komt.
-   Met de [detectie van SQL-bedreigingen](../../sql-database/sql-database-threat-detection.md) kunnen mogelijke dreigingen worden gedetecteerd en gereageerd als ze optreden door beveiligings waarschuwingen te bieden voor verdachte database activiteiten, potentiële kwetsbaar heden, SQL-injectie aanvallen en afwijkende database toegangs patronen.
-   [Always encrypted kolommen](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) zorgen ervoor dat gevoelige gegevens nooit als tekst zonder opmaak in het database systeem worden weer gegeven. Na het inschakelen van gegevens versleuteling, hebben alleen client toepassingen of app-servers met toegang tot de sleutels toegang tot tekst zonder opmaak.
-   [SQL database dynamische gegevens maskering](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) kunnen worden uitgevoerd nadat de referentie architectuur is geïmplementeerd. Klanten moeten de instellingen voor dynamische gegevens maskering aanpassen om te voldoen aan hun database schema.

**Azure Disk Encryption**: Azure Disk Encryption wordt gebruikt voor versleutelde Windows IaaS-schijven van virtuele machines. [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) maakt gebruik van de BitLocker-functie van Windows om volume versleuteling te bieden voor besturings systeem-en gegevens schijven. De oplossing is geïntegreerd met Azure Key Vault om de versleutelings sleutels van de schijf te controleren en te beheren.

### <a name="identity-management"></a>Identiteitsbeheer

De volgende technologieën bieden mogelijkheden voor identiteits beheer in de Azure-omgeving:
- [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) is de multi tenant-Cloud Directory en identiteits beheer service van micro soft.
- Verificatie voor een door de klant geïmplementeerde webtoepassing kan worden uitgevoerd met behulp van Azure AD. Zie [toepassingen integreren met Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md)voor meer informatie.  
- [Met Access Control op basis van rollen (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) kunt u nauw keurig toegang krijgen tot het beheer van Azure. Abonnements toegang is beperkt tot de abonnements beheerder en de toegang tot bronnen kan worden beperkt op basis van de gebruikersrol.
- Een geïmplementeerde IaaS Active Directory instantie biedt identiteits beheer op het niveau van het besturings systeem voor geïmplementeerde virtuele machines van IaaS.

### <a name="security"></a>Beveiliging
**Beheer van geheimen**: De oplossing maakt gebruik van [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) voor het beheer van sleutels en geheimen. Met Azure Sleutelkluis kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Azure Key Vault helpt bij het beheren van IaaS en geheimen voor de schijf versleutelings sleutels van de virtuele machine voor deze referentie architectuur.

**Patch beheer**: Virtuele Windows-machines die door deze Azure-blauwdruk voor beveiliging en naleving automatisering worden geïmplementeerd, worden standaard geconfigureerd voor het ontvangen van automatische updates van Windows Update-service. Deze oplossing implementeert ook de Azure Automation oplossing waarmee update-implementaties kunnen worden gemaakt voor het implementeren van patches op Windows-servers wanneer dat nodig is.

**Bescherming tegen schadelijke software**: [Micro soft antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) voor virtual machines biedt realtime beschermings mogelijkheden waarmee u virussen, spyware en andere schadelijke software kunt identificeren en verwijderen, met Configureer bare waarschuwingen wanneer bekende of ongewenste software probeert om op beveiligde virtuele machines installeren of uitvoeren.

**Application Gateway**: De architectuur vermindert het risico op beveiligings problemen met behulp van een Application Gateway met Web Application Firewall (WAF) en de OWASP ruleset ingeschakeld. Aanvullende mogelijkheden zijn onder andere:

- [End-to-End-SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell)
- [SSL-offload](../../application-gateway/create-ssl-portal.md) inschakelen
- [TLS v 1.0 en v 1.1](https://docs.microsoft.com/azure/application-gateway/application-gateway-end-to-end-ssl-powershell) uitschakelen
- [Web Application firewall](../../application-gateway/waf-overview.md) (WAF-modus)
- [Preventie modus](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-portal) met OWASP 3,0 ruleSet

### <a name="business-continuity"></a>Bedrijfscontinuïteit

**Hoge beschikbaarheid**: Er is ten minste één virtuele machine beschikbaar tijdens een geplande of niet-geplande onderhouds gebeurtenis die voldoet aan de 99,95% Azure SLA. De oplossing implementeert alle virtuele machines met weblaag en gegevenslaag in een [beschikbaarheidsset](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets). Beschikbaarheids sets zorgen ervoor dat de virtuele machines over meerdere geïsoleerde hardwareconfiguraties worden gedistribueerd om de beschik baarheid te verbeteren. Bovendien implementeert deze oplossing de SQL Server virtuele machines in een Beschikbaarheidsset als [AlwaysOn-beschikbaarheids groep](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview). De functie AlwaysOn-beschikbaarheids groep biedt mogelijkheden voor hoge Beschik baarheid en herstel na nood gevallen.

**Recovery Services kluis**: De [Recovery Services kluis](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) maakt back-upgegevens en beveiligt alle configuraties van Azure virtual machines in deze architectuur. Met een Recovery Services kluis kunnen klanten bestanden en mappen herstellen vanaf een IaaS-VM zonder de hele virtuele machine te herstellen, waardoor de herstel tijd sneller verloopt.

**Cloudwitness**: [Cloudwitness](https://docs.microsoft.com/windows-server/failover-clustering/whats-new-in-failover-clustering#BKMK_CloudWitness) is een type quorum-witness voor failoverclusters in Windows Server 2016 die Azure als arbitrage punt gebruikt. De Cloudwitness, zoals elke andere quorumwitness, haalt een stem op en kan deel nemen aan de quorum berekeningen, maar maakt gebruik van de standaard openbaar beschik bare Azure-Blob Storage. Dit elimineert de extra onderhouds overhead van Vm's die worden gehost in een open bare Cloud.

### <a name="logging-and-auditing"></a>Logboekregistratie en bewaking

Azure Monitor Logboeken biedt uitgebreide logboek registratie van systeem-en gebruikers activiteiten, evenals de systeem status. De oplossing voor de [Azure monitor-logboeken](../azure-security-disk-encryption-overview.md) verzamelt en analyseert gegevens die zijn gegenereerd door resources in Azure-en on-premises omgevingen.

- **Activiteiten logboeken:**  [Activiteiten logboeken](../../azure-monitor/platform/activity-logs-overview.md) bieden inzicht in bewerkingen die worden uitgevoerd op resources in een abonnement. Activiteiten logboeken kunnen helpen bij het bepalen van de initiator, het tijdstip van de gebeurtenis en de status van een bewerking.
- **Diagnostische logboeken:**  [Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md) zijn alle logboeken die door elke resource worden gegenereerd. Deze logboeken bevatten Windows-gebeurtenis systeem logboeken, Azure-opslag logboeken, Key Vault controle logboeken en Application Gateway toegang en firewall Logboeken.
- **Archivering van Logboeken:**  Alle Diagnostische logboeken schrijven naar een gecentraliseerd en versleuteld Azure Storage-account voor archivering. De retentie kan door de gebruiker worden geconfigureerd, tot 730 dagen, om te voldoen aan de specifieke vereisten voor het bewaren van een organisatie. Deze logboeken maken verbinding met Azure Monitor logboeken voor verwerking, opslag en dashboard rapportage.

Daarnaast worden de volgende bewakings oplossingen geïnstalleerd als onderdeel van deze architectuur. Houd er rekening mee dat het de verantwoordelijkheid van de klant is om deze oplossingen te configureren voor uitlijning met FedRAMP-beveiligings controles:
-   [Ad-evaluatie](../../azure-monitor/insights/ad-assessment.md): De Active Directory Health Check-oplossing evalueert het risico en de status van de server omgevingen volgens een regel matig interval en biedt een lijst met aanbevelingen die specifiek zijn voor de geïmplementeerde server infrastructuur.
-   [Anitmalware-evaluatie](../../security-center/security-center-install-endpoint-protection.md): De antimalware-oplossing rapporteert over malware, bedreigingen en de beveiligings status.
-   [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker): Met de Azure Automation oplossing worden runbooks opgeslagen, uitgevoerd en beheerd.
-   [Beveiliging en audit](../../security-center/security-center-intro.md): Het Beveiliging en audit-dash board biedt een hoog niveau inzicht in de beveiligings status van bronnen door metrische gegevens op te geven over beveiligings domeinen, belang rijke problemen, detecties, bedreigings informatie en algemene beveiligings query's.
-   [SQL-evaluatie](../../azure-monitor/insights/sql-assessment.md): De SQL Health Check-oplossing evalueert het risico en de status van de server omgevingen met een regel matig interval en biedt klanten een lijst met prioriteiten die specifiek zijn voor de geïmplementeerde server infrastructuur.
-   [Updatebeheer](../../automation/automation-update-management.md): Met de Updatebeheer oplossing kan het klant beheer van beveiligings updates van besturings systemen worden uitgevoerd, inclusief de status van beschik bare updates en het proces voor het installeren van vereiste updates.
-   [Status van agent](../../monitoring/monitoring-solution-agenthealth.md): De Status van agent oplossing meldt hoeveel agents zijn geïmplementeerd en wat hun geografische distributie zijn, en hoeveel agents niet reageren en het aantal agents dat operationele gegevens verzendt.
-   [Azure-activiteiten logboeken](../../azure-monitor/platform/collect-activity-logs.md): De Analyse van activiteitenlogboek oplossing helpt bij het analyseren van de activiteiten logboeken van Azure in alle Azure-abonnementen voor een klant.
-   [Wijzigingen bijhouden](../../azure-monitor/platform/collect-activity-logs.md): Met de Wijzigingen bijhouden oplossing kunnen klanten eenvoudig wijzigingen in de omgeving identificeren.

Azuremonitor
[Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) helpt gebruikers bij het volgen van prestaties, het onderhouden van de beveiliging en het identificeren van trends door organisaties in staat te stellen om gegevens te controleren, te maken en te archiveren, inclusief het volgen van API-aanroepen in azure-bronnen van klanten.

## <a name="threat-model"></a>Bedreigings model
Het gegevensstroom diagram voor deze referentie architectuur is beschikbaar voor [downloaden](https://aka.ms/fedrampWAdfd) of kan hieronder worden weer gegeven. Dit model kan klanten helpen om inzicht te krijgen in de punten van potentieel risico in de systeem infrastructuur bij het aanbrengen van wijzigingen.

![IaaS-webtoepassing voor FedRAMP Threat model](images/fedramp-iaaswa-threat-model.png?raw=true "IaaS-webtoepassing voor FedRAMP Threat model")

## <a name="compliance-documentation"></a>Documentatie voor naleving

De [FedRAMP-matrix voor hoge klant verantwoordelijkheden bevat Azure-blauwdruk voor beveiliging en naleving](https://aka.ms/blueprinthighcrm) een lijst met alle beveiligings controles die vereist zijn voor de hoge basis lijn van FedRAMP. De matrix geeft aan of de implementatie van elk besturings element de verantwoordelijkheid is van micro soft, de klant of gedeeld door de twee.

De [FedRAMP IaaS Web Application High Control-implementatie matrix bevat Azure-blauwdruk voor beveiliging en naleving](https://aka.ms/blueprintwacim) een lijst met alle beveiligings controles die vereist zijn voor de FedRAMP hoge basis lijn. De matrix bevat informatie over welke besturings elementen worden gedekt door de architectuur van de IaaS-webtoepassing, met inbegrip van gedetailleerde beschrijvingen van de manier waarop de implementatie voldoet aan de vereisten van elk gedekte besturings element.

## <a name="deploy-the-solution"></a>De oplossing implementeren

Deze Azure-blauwdruk voor beveiliging en naleving automatisering bestaat uit JSON-configuratie bestanden en Power shell-scripts die worden verwerkt door de API-service van Azure Resource Manager om resources te implementeren in Azure. Gedetailleerde implementatie-instructies zijn [hier](https://aka.ms/fedrampblueprintrepo)beschikbaar.
> [!NOTE]
> Deze oplossing wordt geïmplementeerd in Azure Government.

#### <a name="quickstart"></a>Quick Start
1. Kloon of down load [deze](https://aka.ms/fedrampblueprintrepo) github-opslag plaats naar uw lokale werk station.

2. Voer het Power shell-script voorafgaand aan de implementatie uit: Azure-Blueprint/predeploy/Orchestration_InitialSetup. ps1.

3. Klik op de onderstaande knop, Meld u aan bij de Azure Portal, voer de vereiste ARM-sjabloon parameters in en klik op **kopen**.

    [![Implementeren in Azure](https://azuredeploy.net/AzureGov.png)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Ffedramp-iaas-webapp%2Fmaster%2Fazuredeploy.json)

## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen
### <a name="vpn-and-expressroute"></a>VPN-en ExpressRoute
Een beveiligde VPN-tunnel of [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) moet worden geconfigureerd om een veilige verbinding tot stand te brengen met de resources die zijn geïmplementeerd als onderdeel van deze IaaS-referentie architectuur voor webtoepassingen. Door een VPN-of ExpressRoute in te stellen, kunnen klanten een beveiligingslaag toevoegen voor gegevens die onderweg zijn.

Door een beveiligde VPN-tunnel te implementeren met Azure, kan een virtuele particuliere verbinding tussen een on-premises netwerk en een Azure-Virtual Network worden gemaakt. Deze verbinding vindt plaats via internet en stelt klanten in staat om ' tunnel-informatie veilig ' te maken binnen een versleutelde koppeling tussen het netwerk van de klant en Azure. Site-naar-site-VPN is een veilige, rijpere technologie die is geïmplementeerd door ondernemingen van elke omvang voor tien tallen. De [IPSec-tunnel modus](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) wordt in deze optie als een versleutelings mechanisme gebruikt.

Omdat verkeer binnen de VPN-tunnel via internet met een site-naar-site-VPN gaat, biedt micro soft een andere, nog veiliger verbindings optie. Azure ExpressRoute is een specifieke WAN-verbinding tussen Azure en een on-premises locatie of een Exchange-hosting provider. Omdat ExpressRoute-verbindingen niet via internet werken, bieden deze verbindingen meer betrouw baarheid, hogere snelheden, lagere latenties en een betere beveiliging dan typische verbindingen via internet. Omdat dit een directe verbinding is tussen de telecom municatie-provider van de klant, worden de gegevens niet via internet verzonden en worden ze daarom niet blootgesteld aan IT.

Aanbevolen procedures voor het implementeren van een veilig hybride netwerk dat een on-premises netwerk uitbreidt naar Azure, zijn [beschikbaar](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid).

## <a name="disclaimer"></a>Vrijwaring

- Dit document is alleen ter informatie bedoeld. MICRO SOFT BIEDT GEEN ENKELE GARANTIE, UITDRUKKELIJK, IMPLICIET OF WETTELIJK, MET BETREKKING TOT DE INFORMATIE IN DIT DOCUMENT. Dit document wordt in de as-is opgenomen. Informatie en weer gaven in dit document, inclusief URL'S en andere website verwijzingen, kunnen zonder kennisgeving worden gewijzigd. Klanten die dit document lezen, hebben het risico van het gebruik ervan.  
- Dit document biedt klanten geen juridische rechten voor intellectueel eigendom in een micro soft-product of-oplossingen.  
- Klanten kunnen dit document kopiëren en gebruiken voor interne referentie doeleinden.  
- Bepaalde aanbevelingen in dit document kunnen leiden tot meer gegevens-, netwerk-of COMPUTE-resource gebruik in azure, en kunnen de kosten van de Azure-licentie of het abonnement van de klant verhogen.  
- Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.
- Dit document is ontwikkeld als referentie en mag niet worden gebruikt voor het definiëren van de manier waarop een klant kan voldoen aan specifieke nalevings vereisten en-voor Schriften. Klanten moeten juridische ondersteuning van hun organisatie zoeken op goedgekeurde klant implementaties.
