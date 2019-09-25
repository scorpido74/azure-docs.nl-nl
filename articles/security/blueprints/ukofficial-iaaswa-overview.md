---
title: Azure-blauwdruk voor beveiliging en naleving-IaaS-webtoepassing met drie lagen voor UK-officiële
description: Azure-blauwdruk voor beveiliging en naleving-IaaS-webtoepassing met drie lagen voor UK-officiële
services: security
author: jomolesk
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.topic: article
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 4a30e496c96fcc90417e58b0f921717985b89693
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262801"
---
# <a name="azure-security-and-compliance-blueprint---three-tier-iaas-web-application-for-uk-official"></a>Azure-blauwdruk voor beveiliging en naleving-IaaS-webtoepassing met drie lagen voor UK-officiële

## <a name="overview"></a>Overzicht

 Dit artikel bevat richt lijnen en automatiserings scripts voor het leveren van een Microsoft Azure architectuur op basis van drie lagen die geschikt is voor het verwerken van veel werk belastingen die zijn geclassificeerd als officieel in het Verenigd Konink rijk.

 Met behulp van een infra structuur als code benadering kunt u met de set [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) -sjablonen een omgeving implementeren die wordt afgestemd op de NCSC-14- [Cloud BEVEILIGINGS principes](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) van [UK National Cyber Kritieke beveiligings controles](https://www.cisecurity.org/critical-controls.cfm).

 De NCSC raden de beveiligings principes van de Cloud door klanten te gebruiken om de beveiligings eigenschappen van de service te evalueren en om inzicht te krijgen in de verantwoordelijkheid van de functie van de klant en de leverancier. We hebben informatie over elk van deze principes gegeven om u te helpen inzicht te krijgen in de splitsing van verantwoordelijkheden.

 Deze architectuur en bijbehorende Azure Resource Manager sjablonen worden ondersteund door de micro soft-white paper- [beveiligings controles voor de 14-Cloud voor UK in de Cloud met behulp van Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). In dit document wordt uitgelegd hoe Azure-Services worden afgestemd op de UK NCSC 14-Cloud beveiligings principes, waardoor organisaties snel hun capaciteit kunnen volgen om te voldoen aan hun nalevings verplichtingen met behulp van wereld wijde Cloud Services en in het UK op het Microsoft Azure Cloud.

 Met deze sjabloon wordt de infra structuur voor de werk belasting geïmplementeerd. Toepassings code en ondersteunende software voor bedrijfslaag en gegevenslaag moeten worden geïnstalleerd en geconfigureerd. Gedetailleerde implementatie-instructies zijn [hier](https://aka.ms/ukwebappblueprintrepo)beschikbaar.

 Als u geen Azure-abonnement hebt, kunt u snel aan de [slag met Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Architectuur diagram en onderdelen

 De Azure-sjablonen bieden een architectuur voor een webtoepassing met drie lagen in een Azure-cloud omgeving die ondersteuning biedt voor UK officiële workloads. De architectuur levert een beveiligde hybride omgeving die een on-premises netwerk uitbreidt naar Azure zodat op internet gebaseerde workloads veilig kunnen worden benaderd door zakelijke gebruikers of via internet.

![IaaS-webtoepassing met drie lagen voor het officiële referentie architectuur diagram van UK](images/ukofficial-iaaswa-architecture.png?raw=true "IaaS-webtoepassing met drie lagen voor het officiële referentie architectuur diagram van UK")

 Deze oplossing maakt gebruik van de volgende Azure-Services. Details van de implementatie architectuur vindt u in de sectie [implementatie architectuur](#deployment-architecture) .

(1)/16 Virtual Network-operationeel VNet
- (3)/24 subnetten-3-tier (Web, hoge en hoge, gegevens)
- (1)/27 subnet-voegt
- (1)/27 subnet-gateway subnet
- (1)/29 subnet-Application Gateway subnet
- Maakt gebruik van standaard-DNS (door Azure)
- Peering is ingeschakeld voor beheer-VNet
- Netwerk beveiligings groep (NSG) voor het beheren van de verkeers stroom

(1)/24 Virtual Network-beheer-VNet
- (1)/27 subnet
- Gebruik (2) voegt DNS en (1) Azure DNS vermeldingen toe
- Peering ingeschakeld voor operationeel VNet
- Netwerk beveiligings groep (NSG) voor het beheren van de verkeers stroom

(1) Application Gateway
- WAF-ingeschakeld
- WAF-modus-voor komen
- Regelset: OWASP 3,0
- HTTP-listener op poort 80
- Connectiviteit/verkeer geregeld via NSG
- Eind punt voor openbaar IP-adres gedefinieerd (Azure)

(1) VPN-op route gebaseerde, site-2-site IPSec VPN-tunnel
- Eind punt voor openbaar IP-adres gedefinieerd (Azure)
- Connectiviteit/verkeer geregeld via NSG
- (1) lokale netwerk gateway (on-premises eind punt)
- (1) Azure-netwerk gateway (Azure-eind punt)

(9) Virtual Machines: alle virtuele machines worden geïmplementeerd met de DSC-instellingen van Azure IaaS antimalware

- (2) Active Directory Domain Services domein controllers (Windows Server 2012 R2)
  - (2) DNS-Server rollen-1 per VM
  - (2) Nic's die zijn verbonden met operationeel VNet-1 per VM
  - Beide zijn gekoppeld aan het domein dat in de sjabloon is gedefinieerd
    - Het domein is gemaakt als onderdeel van de implementatie


- (1) JumpBox (bastion host)-beheer-VM
  - 1 NIC op het beheer-VNet met openbaar IP-adres
    - NSG wordt gebruikt om het verkeer (in/uit) te beperken tot specifieke bronnen
  - Geen lid van een domein


- (2) Vm's op weblaag
  - (2) IIS-Server functies-1 per VM
  - (2) Nic's die zijn verbonden met operationeel VNet-1 per VM
  - Geen lid van een domein


- (2) Vm's met meerdere lagen
  - (2) Nic's die zijn verbonden met operationeel VNet-1 per VM
  - Geen lid van een domein


- (2) Vm's voor de gegevenslaag
  - (2) Nic's die zijn verbonden met operationeel VNet-1 per VM
  - Geen lid van een domein

Beschikbaarheidssets
- (1) Active Directory-domein controller VM set-2 Vm's
- (1) VM-set van de weblaag-2 Vm's
- (1) VM-set van het hoogste niveau-2 Vm's
- (1) VM-set van gegevenslaag-2 Vm's

Netwerktaakverdeler
- (1) Load Balancer voor de weblaag
- (1) Load Balancer van het hoogste niveau
- (1) gegevenslaag Load Balancer

Storage
- (14) totaal aantal opslag accounts
  - Beschikbaarheidsset Active Directory-domein controller
    - (2) primaire lokaal redundante opslag (LRS)-accounts-1 voor elke VM  
    - (1) diagnostische lokaal redundante opslag (LRS)-account voor de Beschikbaarheidsset toevoegen
  - Beheer JumpBox VM
    - (1) primair account voor lokaal redundante opslag (LRS) voor de JumpBox VM
    - (1) diagnostische lokaal redundante opslag (LRS)-account voor de JumpBox VM
  - Vm's voor weblaag
    - (2) primaire lokaal redundante opslag (LRS)-accounts-1 voor elke VM  
    - (1) diagnostische lokaal redundante opslag (LRS)-account voor de Beschikbaarheidsset voor de weblaag
  - Vm's met meerdere lagen
    - (2) primaire lokaal redundante opslag (LRS)-accounts-1 voor elke VM  
    - (1) diagnostische lokaal redundante opslag (LRS)-account voor de Beschikbaarheidsset met meerdere lagen
  - Vm's van gegevenslaag
    - (2) primaire lokaal redundante opslag (LRS)-accounts-1 voor elke VM  
    - (1) diagnostische lokaal redundante opslag (LRS)-account voor de Beschikbaarheidsset voor de gegevenslaag

### <a name="deployment-architecture"></a>Implementatie architectuur:

**On-premises netwerk**: Een particulier lokaal netwerk dat is geïmplementeerd in een organisatie.

**Productie-VNet**: Het Production [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (Virtual Network) fungeert als host voor de toepassing en andere operationele resources die worden uitgevoerd in Azure. Elk VNet kan verschillende subnetten bevatten die worden gebruikt voor het isoleren en beheren van netwerk verkeer.

**Weblaag**: Verwerkt binnenkomende HTTP-aanvragen. Antwoorden worden via deze laag geretourneerd.

**Zakelijke laag**: Implementeert bedrijfs processen en andere functionele logica voor het systeem.

**Database laag**: Biedt permanente gegevens opslag, met behulp van SQL Server AlwaysOn- [beschikbaarheids groepen](https://msdn.microsoft.com/library/hh510230.aspx) voor hoge Beschik baarheid. Klanten kunnen [Azure SQL database](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) als een Paas alternatief gebruiken.

**Gateway**: De [VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) biedt connectiviteit tussen de routers in het on-premises netwerk en het productie-VNet.

**Internet gateway en openbaar IP-adres**: De Internet gateway stelt toepassings services beschikbaar voor gebruikers via internet. Verkeer dat toegang tot deze services krijgt, is beveiligd met een [Application Gateway](../../application-gateway/overview.md) met Layer 7-route ring en taakverdelings mogelijkheden met Web Application firewall-beveiliging (WAF).

**Beheer-VNet**: Dit [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) bevat bronnen voor het implementeren van beheer-en controle mogelijkheden voor de werk belastingen die worden uitgevoerd in het productie-VNet.

**JumpBox**: Dit wordt ook wel een [bastion-host](https://en.wikipedia.org/wiki/Bastion_host)genoemd. Dit is een beveiligde vm in het netwerk die beheerders gebruiken om verbinding te maken met virtuele machines in de productie-VNet. De jumpbox heeft een netwerkbeveiligingsgroep die alleen extern verkeer vanaf openbare IP-adressen op een lijst met veilige adressen toelaat. Als u RDP-verkeer (extern bureau blad) wilt toestaan, moet de bron van het verkeer worden gedefinieerd in de NSG. Beheer van productie resources is via RDP met behulp van een beveiligde JumpBox-VM.

Door de **gebruiker gedefinieerde routes**: Door de [gebruiker gedefinieerde routes](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) worden gebruikt voor het definiëren van de stroom van IP-verkeer binnen Azure VNets.

**Netwerkpeered VNETs**: De productie-en beheer VNets zijn verbonden met behulp van [VNet-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).
Deze VNets worden nog steeds beheerd als afzonderlijke resources, maar weer gegeven als een voor alle connectiviteits doeleinden voor deze virtuele machines. Deze netwerken communiceren rechtstreeks met elkaar met behulp van privé-IP-adressen. VNet-peering is onderhevig aan de VNets die zich in dezelfde Azure-regio bevindt.

**Netwerk beveiligings groepen**: [Nsg's](../../virtual-network/virtual-network-vnet-plan-design-arm.md) bevatten Access Control-lijsten waarmee verkeer binnen een VNet wordt toegestaan of geweigerd. Nsg's kan worden gebruikt om verkeer op een subnet of een afzonderlijk VM-niveau te beveiligen.

**Active Directory Domain Services (AD DS)** : Deze architectuur biedt een speciale [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx) -implementatie.

**Logboek registratie en controle**: [Azure-activiteiten logboek](../../azure-monitor/platform/activity-logs-overview.md) legt bewerkingen vast die zijn uitgevoerd op de resources in uw abonnement, zoals wie de bewerking heeft gestart, op het moment dat de bewerking is uitgevoerd, de status van de bewerking en de waarden van andere eigenschappen die u kunnen helpen bij het onderzoeken van de bewerking. Azure-activiteiten logboek is een Azure-platform service waarmee alle acties voor een abonnement worden vastgelegd. U kunt logboeken archiveren of exporteren, indien nodig.

**Netwerk bewaking en waarschuwingen**: [Azure Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) is een platform service voor het vastleggen van netwerk pakketten, het registreren van stromen, hulpprogram ma's voor topologie en diagnoses voor netwerk verkeer binnen uw VNets.

## <a name="guidance-and-recommendations"></a>Richt lijnen en aanbevelingen

### <a name="business-continuity"></a>Bedrijfscontinuïteit

**Hoge Beschik baarheid**: Server werkbelastingen worden gegroepeerd in een [beschikbaarheidsset](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) om te zorgen voor een hoge Beschik baarheid van virtuele machines in Azure. Deze configuratie zorgt ervoor dat tijdens een geplande of niet-geplande onderhouds gebeurtenis ten minste één virtuele machine beschikbaar is en voldoet aan de 99,95% Azure SLA.

### <a name="logging-and-audit"></a>Logboek registratie en controle

**Bewaking**: [Azure monitor](../../azure-monitor/overview.md) is de platform service die één bron biedt voor het bewaken van het activiteiten logboek, metrische gegevens en Diagnostische logboeken van al uw Azure-resources. Azure Monitor kan worden geconfigureerd om de metrische gegevens en logboeken die afkomstig zijn van resources in azure, te visualiseren, op te slaan, te routeren, te archiveren en te verwerken. Het is raadzaam om de Access Control op basis van resources wordt gebruikt om de audittrail te beveiligen om ervoor te zorgen dat gebruikers niet de mogelijkheid hebben om de logboeken te wijzigen.

**Activiteiten logboeken**: Configureer [Azure-activiteiten logboeken](../../azure-monitor/platform/activity-logs-overview.md) om inzicht te krijgen in de bewerkingen die zijn uitgevoerd op de resources in uw abonnement.

**Diagnostische logboeken**: [Diagnostische logboeken](../../azure-monitor/platform/resource-logs-overview.md) zijn alle logboeken die door een bron worden gegenereerd. Deze logboeken kunnen Windows-gebeurtenis systeem logboeken, blob, tabel en wachtrij logboeken bevatten.

**Firewall logboeken**: Application Gateway biedt volledige diagnostische gegevens en toegangs logboeken. Er zijn firewalllogboeken beschikbaar voor Application Gateway-resources waarvoor WAF is ingeschakeld.

**Archivering van Logboeken**: Logboek gegevens opslag kan worden geconfigureerd om te schrijven naar een gecentraliseerd Azure Storage-account voor archivering en een gedefinieerde Bewaar periode. Logboeken kunnen worden verwerkt met behulp van Azure Monitor-Logboeken of SIEM-systemen van derden.

### <a name="identity"></a>Identiteit

**Active Directory Domain Services**: Deze architectuur levert een Active Directory Domain Services-implementatie in Azure. Zie de volgende artikelen voor specifieke aanbevelingen over het implementeren van Active Directory in Azure:

[Active Directory Domain Services (AD DS) uitbreiden naar Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain).

[Richt lijnen voor het implementeren van Windows Server-Active Directory op Azure virtual machines](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Integratie van Active Directory**: Als alternatief voor een speciale AD DS-architectuur kunnen klanten [Azure Active Directory](/azure/architecture/reference-architectures/identity) integratie of [Active Directory in azure gebruiken die zijn gekoppeld aan een on-premises forest](/azure/architecture/reference-architectures/identity).

### <a name="security"></a>Beveiliging

**Beheer beveiliging**: Met deze blauw druk kunnen beheerders verbinding maken met het beheer-VNet en JumpBox met behulp van RDP van een vertrouwde bron. Het netwerk verkeer voor het beheer-VNet wordt beheerd met behulp van Nsg's. Toegang tot poort 3389 is beperkt tot verkeer van een vertrouwd IP-bereik dat toegang kan krijgen tot het subnet met de JumpBox.

Klanten kunnen ook overwegen een [verbeterd beveiligings beheer model](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) te gebruiken om de omgeving te beveiligen wanneer er verbinding wordt gemaakt met het beheer-VNet en JumpBox. Het is raadzaam dat voor verbeterde beveiligings klanten een [privileged Access-werk station](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) en RDGateway-configuratie worden gebruikt. Het gebruik van virtuele netwerk apparaten en open bare/persoonlijke Dmz's biedt verdere verbeteringen op het niveau van beveiliging.

**Het netwerk beveiligen**: [Netwerk beveiligings groepen](../../virtual-network/virtual-network-vnet-plan-design-arm.md) (Nsg's) wordt aanbevolen voor elk subnet om een tweede beveiligings niveau te bieden voor inkomend verkeer dat een onjuist geconfigureerde of uitgeschakelde gateway omzeilt. Voor beeld: [Resource Manager-sjabloon voor het implementeren van een NSG](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Open bare eind punten beveiligen**: De Internet gateway stelt toepassings services beschikbaar voor gebruikers via internet. Verkeer dat toegang tot deze services krijgt, is beveiligd met een [Application Gateway](../../application-gateway/overview.md), dat een Web Application firewall en HTTPS-protocol beheer biedt.

**IP-bereiken**: De IP-bereiken in de architectuur zijn voorgestelde bereiken. Klanten wordt aangeraden hun eigen omgeving te overwegen en de juiste bereiken te gebruiken.

**Hybride connectiviteit**: De op de cloud gebaseerde workloads zijn verbonden met het on-premises Data Center via IPSEC VPN met behulp van de Azure-VPN Gateway. Klanten moeten ervoor zorgen dat ze een geschikte VPN Gateway gebruiken om verbinding te maken met Azure. Voor beeld- [VPN gateway Resource Manager-sjabloon](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Klanten die grootschalige, bedrijfskritische workloads met big data vereisten uitvoeren, willen wellicht een hybride netwerk architectuur overwegen met [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) voor de connectiviteit van particuliere netwerken met micro soft-Cloud Services.

**Schei ding van de bezorgdheid**: Deze referentie architectuur scheidt de VNets voor beheer bewerkingen en bedrijfs bewerkingen. Met afzonderlijke VNets en subnetten is verkeer beheer mogelijk, met inbegrip van binnenkomend verkeer en uitwijkings beperkingen, met behulp van Nsg's tussen netwerk segmenten die volgen op de [micro soft-Cloud Services en](/azure/architecture/vdc/networking-virtual-datacenter) aanbevolen procedures voor netwerk beveiliging.

**Resource beheer**: Azure-resources, zoals Vm's, VNets en load balancers, worden beheerd door ze samen te groeperen in [Azure-resource groepen](../../azure-resource-manager/resource-group-overview.md). Op resources gebaseerde Access Control rollen kunnen vervolgens worden toegewezen aan elke resource groep om de toegang tot alleen gemachtigde gebruikers te beperken.

**Access Control beperkingen**: Op [rollen gebaseerde Access Control](../../role-based-access-control/role-assignments-portal.md) (RBAC) gebruiken voor het beheren van de resources in uw toepassing met behulp van [aangepaste rollen](../../role-based-access-control/custom-roles.md) RBAC kan worden gebruikt om de bewerkingen te beperken die DevOps op elke laag kan uitvoeren. Gebruik het [principe van minimale bevoegdheden](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1)bij het verlenen van machtigingen. Registreer alle beheergerelateerde bewerkingen en controleer regelmatig of eventuele configuratiewijzigingen inderdaad zo waren gepland.

**Internet toegang**: Deze referentie architectuur maakt gebruik van [Azure-toepassing gateway](../../application-gateway/overview.md) als de Internet gateway en Load Balancer. Sommige klanten kunnen ook overwegen virtuele netwerk apparaten van derden te gebruiken voor extra netwerk beveiliging als alternatief voor de Azure-toepassing- [Gateway](../../application-gateway/overview.md).

**Azure Security Center**: De [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro) biedt een centraal overzicht van de beveiligings status van resources in het abonnement en biedt aanbevelingen voor het voor komen van gemanipuleerde resources. Het kan ook worden gebruikt om meer nauw keurigere beleids regels in te scha kelen. Zo kunnen beleids regels worden toegepast op specifieke resource groepen, zodat de onderneming de postuur kan aanpassen aan een risico. Het is raadzaam om Azure Security Center in te scha kelen in hun Azure-abonnement.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Nalevings documentatie voor NCSC Cloud Security Principles

De kroon commerciële dienst (een instantie die de commerciële en aanschaffings activiteiten door de overheid verbetert), vernieuwt de classificatie van micro soft in-Scope Enter prise Cloud Services naar G-Cloud V6, waarbij alle aanbiedingen op het officiële niveau worden gedekt. Details van Azure en G-Cloud vindt u in het [overzicht van Azure UK G-Cloud Security Assessment](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Deze blauw druk is afgestemd op de 14 beveiligings principes voor de cloud die zijn gedocumenteerd in de NCSC voor [Cloud beveiliging](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) om ervoor te zorgen dat een omgeving wordt ondersteund die werk belastingen ondersteunt die zijn geclassificeerd als UK-ambtenaar.

De [matrix van de klant verantwoordelijkheid](https://aka.ms/ukofficial-crm) (Excel-werkmap) bevat alle 14 beveiligings principes voor de Cloud en de matrix geeft voor elk principe (of principe subdeel) aan of de implementatie van het principe de verantwoordelijkheid is van micro soft, de klant of gedeeld tussen de twee.

De [principle-implementatie matrix](https://aka.ms/ukofficial-iaaswa-pim) (Excel-werkmap) bevat alle 14 beveiligings principes voor de Cloud en de matrix geeft voor elk principe (of principe subdeel) aan dat de klant verantwoordelijk is in de matrix voor klant verantwoordelijkheden, 1) als de blauw druk automatisering implementeert het principe en 2) een beschrijving van de manier waarop de implementatie wordt uitgelijnd met de vereiste voor Schriften.

Daarnaast publiceerde de Cloud Security Alliance (CSA) de Cloud beheer matrix ter ondersteuning van klanten in de evaluatie van cloud providers en om vragen te identificeren die moeten worden beantwoord voordat ze naar Cloud Services kunnen worden verplaatst. Als antwoord heeft Microsoft Azure de CSA consensus Assessment Initiative vragen lijst ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)) beantwoord, waarin wordt beschreven hoe micro soft de voorgestelde principes verhelpt.

## <a name="deploy-the-solution"></a>De oplossing implementeren

Er zijn twee methoden die gebruikers van de implementatie kunnen gebruiken voor het implementeren van deze Blue-Automation. De eerste methode maakt gebruik van een Power shell-script, terwijl de tweede methode gebruikmaakt van de Azure Portal voor het implementeren van de referentie architectuur. Gedetailleerde implementatie-instructies zijn [hier](https://aka.ms/ukofficial-iaaswa-repo)beschikbaar.

## <a name="disclaimer"></a>Vrijwaring

 - Dit document is alleen ter informatie bedoeld. MICRO SOFT BIEDT GEEN ENKELE GARANTIE, UITDRUKKELIJK, IMPLICIET OF WETTELIJK, MET BETREKKING TOT DE INFORMATIE IN DIT DOCUMENT. Dit document wordt in de as-is opgenomen. Informatie en weer gaven in dit document, inclusief URL'S en andere website verwijzingen, kunnen zonder kennisgeving worden gewijzigd. Klanten die dit document lezen, hebben het risico van het gebruik ervan.
 - Dit document biedt klanten geen juridische rechten voor intellectueel eigendom in een micro soft-product of-oplossingen.
 - Klanten kunnen dit document kopiëren en gebruiken voor interne referentie doeleinden.
 - Bepaalde aanbevelingen in dit document kunnen leiden tot meer gegevens-, netwerk-of COMPUTE-resource gebruik in azure, en kunnen de kosten van de Azure-licentie of het abonnement van de klant verhogen.
 - Deze architectuur is bedoeld om te fungeren als basis voor klanten om aan hun specifieke vereisten aan te passen en mag niet worden gebruikt in een productie omgeving.
 - Dit document is ontwikkeld als referentie en mag niet worden gebruikt voor het definiëren van de manier waarop een klant kan voldoen aan specifieke nalevings vereisten en-voor Schriften. Klanten moeten juridische ondersteuning van hun organisatie zoeken op goedgekeurde klant implementaties.
