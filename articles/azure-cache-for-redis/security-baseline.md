---
title: Azure Security Baseline voor Azure Cache voor Redis
description: Azure Security Baseline voor Azure Cache voor Redis
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 172ac4e13201457f62d722236dff130a312cfdeb
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81755509"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Azure Security Baseline voor Azure Cache voor Redis

De Azure Security Baseline voor Azure Cache for Redis bevat aanbevelingen waarmee u de beveiligingshouding van uw implementatie verbeteren.

De basislijn voor deze service is afkomstig van de [Azure Security Benchmark versie 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), die aanbevelingen geeft over hoe u uw cloudoplossingen op Azure beveiligen met onze richtlijnen voor best practices.

Zie [overzicht azure security baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Beveiligingsbeheer: netwerkbeveiliging](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Resources beveiligen met netwerkbeveiligingsgroepen of Azure Firewall in uw virtuele netwerk

**Richtlijnen**: Implementeer uw Azure-cache voor redis-instantie binnen een virtueel netwerk (VNet). Een VNet is een privénetwerk in de cloud. Wanneer een Azure-cache voor Redis-instantie is geconfigureerd met een VNet, is deze niet openbaar adresseerbaar en alleen toegankelijk vanaf virtuele machines en toepassingen binnen het VNet.

U ook firewallregels opgeven met een begin- en eind-IP-adresbereik. Wanneer firewallregels zijn geconfigureerd, kunnen alleen clientverbindingen uit de opgegeven IP-adresbereiken verbinding maken met de cache.

Ondersteuning voor virtueel netwerk configureren voor een Premium Azure-cache voor Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Azure-cache configureren voor firewallregels van Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: De configuratie en het verkeer van Vnets, Subnetten en NIC's controleren en registreren

**Richtlijnen:** Wanneer virtuele machines worden geïmplementeerd in hetzelfde virtuele netwerk als uw Azure Cache voor redis, u netwerkbeveiligingsgroepen (NSG) gebruiken om het risico op gegevensexfiltratie te verminderen. Schakel NSG-stroomlogboeken in en stuur logboeken naar een Azure Storage Account voor verkeerscontrole. U ook NSG-stroomlogboeken naar een log-analysewerkruimte verzenden en Traffic Analytics gebruiken om inzicht te geven in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteiten te visualiseren en hotspots te identificeren, beveiligingsbedreigingen te identificeren, verkeersstroompatronen te begrijpen en netwerkverkeerde configuraties te lokaliseren.

NSG-stroomlogboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics inschakelen en gebruiken:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webapplicaties beschermen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende kwaadaardige IP-adressen weigeren

**Richtlijnen:** VNet-implementatie (Azure Virtual Network) biedt verbeterde beveiliging en isolatie voor uw Azure-cache voor Redis, evenals subnetten, toegangscontrolebeleid en andere functies om de toegang verder te beperken. Wanneer azure cache voor Redis wordt geïmplementeerd in een VNet, is deze niet openbaar adresseerbaar en alleen toegankelijk vanaf virtuele machines en toepassingen binnen het VNet.

DDoS Protection Standard inschakelen op de VNets die zijn gekoppeld aan uw Azure-cache voor Redis-exemplaren om te beschermen tegen ddos-aanvallen (distributed denial-of-service). Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende kwaadaardige of ongebruikte internetIP-adressen te weigeren.

Ondersteuning voor virtueel netwerk configureren voor een Premium Azure-cache voor Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Azure DDoS Protection Standard beheren met de Azure-portal:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Netwerkpakketten en stroomlogboeken opnemen

**Richtlijnen:** Wanneer virtuele machines worden geïmplementeerd in hetzelfde virtuele netwerk als uw Azure Cache voor bijvoorbeeld Redis, u netwerkbeveiligingsgroepen (NSG) gebruiken om het risico op gegevensexfiltratie te verminderen. Schakel NSG-stroomlogboeken in en stuur logboeken naar een Azure Storage Account voor verkeerscontrole. U ook NSG-stroomlogboeken naar een log-analysewerkruimte verzenden en Traffic Analytics gebruiken om inzicht te geven in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteiten te visualiseren en hotspots te identificeren, beveiligingsbedreigingen te identificeren, verkeersstroompatronen te begrijpen en netwerkverkeerde configuraties te lokaliseren.

NSG-stroomlogboeken inschakelen:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics inschakelen en gebruiken:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Netwerkgebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** Wanneer u Azure Cache voor Redis gebruikt met uw webtoepassingen die worden uitgevoerd op Azure App Service of rekeninstanties, implementeert u alle resources binnen een Azure Virtual Network (VNet) en beveiligt u met een Azure Web Application Firewall (WAF) op Web Application Gateway. Configureer de WAF om te draaien in "Preventiemodus". Preventie-modus blokkeert inbraken en aanvallen die de regels detecteren. De aanvaller ontvangt een uitzondering voor '403 ongeautoriseerde toegang' en de verbinding is gesloten. Preventie modus registreert dergelijke aanvallen in de WAF logs.

U ook een aanbieding selecteren in de Azure Marketplace die IDS/IPS-functionaliteit ondersteunt met payload-inspectie- en/of anomaliedetectiemogelijkheden.

Inzicht in Azure WAF-mogelijkheden:

https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview

Azure WAF implementeren:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimaliseer de complexiteit en administratieve overhead van netwerkbeveiligingsregels

**Richtlijnen:** gebruik virtuele netwerkservicetags om netwerktoegangsbesturingselementen te definiëren voor netwerkbeveiligingsgroepen (NSG) of Azure Firewall. U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld ApiManagement) op te geven in het juiste bron- of bestemmingsveld van een regel, u het verkeer voor de desbetreffende service toestaan of weigeren. Microsoft beheert de adresvoorvoegsels van de servicetag en werkt de servicetag automatisch bij wanneer adressen worden gewijzigd.

U ook toepassingsbeveiligingsgroepen (ASG) gebruiken om complexe beveiligingsconfiguratie te vereenvoudigen. Met ASGs u netwerkbeveiliging configureren als een natuurlijke uitbreiding van de structuur van een toepassing, zodat u virtuele machines groeperen en netwerkbeveiligingsbeleid definiëren op basis van die groepen.

Tags voor virtuele netwerkservice:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Toepassingsbeveiligingsgroepen:

https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** Standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkresources die betrekking hebben op uw Azure-cache voor Redis-exemplaren met Azure Policy. Gebruik Azure Policy-aliassen in de naamruimten 'Microsoft.Cache' en 'Microsoft.Network' om aangepaste beleidsregels te maken om de netwerkconfiguratie van uw Azure-cache voor Redis-instanties te controleren of af te dwingen. U ook gebruik maken van ingebouwde beleidsdefinities zoals:

Alleen beveiligde verbindingen met uw Redis-cache moeten worden ingeschakeld

DDoS Protection Standard moet ingeschakeld zijn

U Azure Blueprints ook gebruiken om azure-implementaties op grote schaal te vereenvoudigen door belangrijke omgevingsartefacten te verpakken, zoals Arm-sjablonen (Azure Resource Manager), rbac (Role-based access control) en beleidsregels, in één blauwdrukdefinitie. Pas de blauwdruk eenvoudig toe op nieuwe abonnementen en omgevingen en stem de besturing en het beheer af via versiebeheer.

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een Azure Blueprint maken:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regels voor documentconfiguratie

**Richtlijnen:** Gebruik tags voor netwerkbronnen die zijn gekoppeld aan uw Azure Cache voor Redis-implementatie om ze logisch te ordenen in een taxonomie.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Gebruik geautomatiseerde hulpprogramma's om netwerkbronconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik het Azure Activity-logboek om netwerkbronconfiguraties te bewaken en wijzigingen op te sporen voor netwerkbronnen die betrekking hebben op uw Azure-cache voor Redis-exemplaren. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen plaatsvinden in kritieke netwerkbronnen.

Azure Activity Log-gebeurtenissen weergeven en ophalen:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Waarschuwingen maken in Azure Monitor:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Beveiligingscontrole: Logboekregistratie en -controle](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)voor meer informatie.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Gebruik goedgekeurde tijdsynchronisatiebronnen

**Richtlijnen**: Microsoft onderhoudt de tijdbron die wordt gebruikt voor Azure-resources, zoals Azure Cache voor Redis, voor tijdstempels in de logboeken.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboeken configureren

**Richtlijnen**: Schakel diagnostische instellingen voor Azure Activity Log in en stuur de logboeken naar een Log Analytics-werkruimte, Azure-gebeurtenishub of Azure-opslagaccount voor archief. Activiteitslogboeken geven inzicht in de bewerkingen die zijn uitgevoerd in uw Azure-cache voor Redis-exemplaren op het niveau van het besturingsvlak. Met azure activity log-gegevens u bepalen wat, wie en wanneer voor schrijfbewerkingen (PUT, POST, DELETE) die worden uitgevoerd op het niveau van het besturingsvlak voor uw Azure-cache voor Redis-exemplaren.

Diagnostische instellingen voor Azure Activity Log inschakelen:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Controlelogboekregistratie inschakelen voor Azure-resources

**Richtlijnen**: Schakel diagnostische instellingen voor Azure Activity Log in en stuur de logboeken naar een Log Analytics-werkruimte, Azure-gebeurtenishub of Azure-opslagaccount voor archief. Activiteitslogboeken geven inzicht in de bewerkingen die zijn uitgevoerd in uw Azure-cache voor Redis-exemplaren op het niveau van het besturingsvlak. Met azure activity log-gegevens u bepalen wat, wie en wanneer voor schrijfbewerkingen (PUT, POST, DELETE) die worden uitgevoerd op het niveau van het besturingsvlak voor uw Azure-cache voor Redis-exemplaren.

Hoewel statistieken beschikbaar zijn door diagnostische instellingen in te schakelen, is auditlogboekregistratie bij het gegevensvlak nog niet beschikbaar voor Azure Cache voor Redis.

Diagnostische instellingen voor Azure Activity Log inschakelen:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Beveiligingslogboeken verzamelen van besturingssystemen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagbehoud beveiligingslogboeken configureren

**Richtlijnen:** Stel in Azure Monitor de bewaarperiode voor Logboekanalyse-werkruimten die zijn gekoppeld aan uw Azure-cache voor Redis-exemplaren in volgens de nalevingsvoorschriften van uw organisatie.

Houd er rekening mee dat controlelogboekregistratie bij het gegevensvlak nog niet beschikbaar is voor Azure Cache voor Redis.

Parameters voor logboekbehoud instellen:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken controleren en controleren

**Richtlijnen**: Schakel diagnostische instellingen voor Azure Activity Log in en stuur de logboeken naar een werkruimte log Analytics. Voer query's uit in Log Analytics om termen te zoeken, trends te identificeren, patronen te analyseren en veel andere inzichten te bieden op basis van de activiteitslogboekgegevens die mogelijk zijn verzameld voor Azure Cache voor Redis.

Houd er rekening mee dat controlelogboekregistratie bij het gegevensvlak nog niet beschikbaar is voor Azure Cache voor Redis.

Diagnostische instellingen voor Azure Activity Log inschakelen:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Azure-activiteitslogboeken verzamelen en analyseren in de werkruimte Log Analytics in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteit

**Richtlijnen:** U configureren om waarschuwingen te ontvangen op basis van statistieken en activiteitslogboeken met betrekking tot uw Azure Cache voor Redis-exemplaren. Met Azure Monitor u een waarschuwing configureren om een e-mailmelding te verzenden, een webhook aan te roepen of een Azure Logic App aan te roepen.

Hoewel statistieken beschikbaar zijn door diagnostische instellingen in te schakelen, is auditlogboekregistratie bij het gegevensvlak nog niet beschikbaar voor Azure Cache voor Redis.

Waarschuwingen voor Azure Cache configureren voor Redis:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#alerts

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centraliseer anti-malware logging

**Richtsnoeren**: Niet van toepassing; Azure Cache voor Redis verwerkt of produceert geen anti-malware gerelateerde logboeken.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-querylogboekregistratie inschakelen

**Richtsnoeren**: Niet van toepassing; Azure Cache voor Redis verwerkt of produceert geen DNS-gerelateerde logboeken.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="210-enable-command-line-audit-logging"></a>2.10: Logboekregistratie van opdrachtregelcontrole inschakelen

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="identity-and-access-control"></a>Identiteits- en toegangsbeheer

*Zie [Beveiligingsbeheer: Identiteits- en toegangscontrole](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)voor meer informatie.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Inventaris bijhouden van de administratieve rekeningen

**Richtlijnen**: Azure Active Directory (AD) heeft ingebouwde rollen die expliciet moeten worden toegewezen en opvraagbaar zijn. Gebruik de Azure AD PowerShell-module om ad-hocquery's uit te voeren om accounts te ontdekken die lid zijn van beheergroepen.

Een maprol in Azure AD met PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Leden van een directoryrol in Azure AD krijgen met PowerShell:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Standaardwachtwoorden wijzigen indien van toepassing

**Richtlijnen:** Beheer de toegang tot azure-cache voor Redis via Azure Active Directory (AD). Azure AD heeft niet het concept van standaardwachtwoorden. 

Toegang tot Azure Cache voor Redis wordt beheerd via toegangssleutels. Deze sleutels worden gebruikt door de clients die verbinding maken met uw cache en kunnen op elk gewenst moment worden geregenereerd.

Het wordt niet aanbevolen om standaardwachtwoorden in uw toepassing in te bouwen. In plaats daarvan u uw wachtwoorden opslaan in Azure Key Vault en vervolgens Azure Active Directory gebruiken om ze op te halen.

Azure-cache voor Redis-toegangssleutels regenereren:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Gebruik maken van speciale administratieve rekeningen

**Richtlijnen**: Maak standaard werkprocedures rond het gebruik van specifieke administratieve accounts. Gebruik Azure Security Center Identity and Access Management om het aantal beheerdersaccounts te controleren.

Bovendien u aanbevelingen van Azure Security Center of ingebouwde Azure-beleidsregels gebruiken om u te helpen bij het bijhouden van speciale beheeraccounts, aanbevelingen van Azure Security Center of ingebouwde Azure-beleidsregels, zoals:

- Er moeten meer dan één eigenaar aan uw abonnement zijn toegewezen

- Afgeschafte accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd

- Externe accounts met eigenaarmachtigingen moeten uit uw abonnement worden verwijderd

Azure Security Center gebruiken om identiteit en toegang te controleren (Voorbeeld):https://docs.microsoft.com/azure/security-center/security-center-identity-access

Azure-beleid gebruiken:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Gebruik eenmalige aanmelding (SSO) met Azure Active Directory

**Richtlijnen**: Azure Cache voor Redis gebruikt toegangssleutels om gebruikers te verifiëren en ondersteunt geen enkele aanmelding (SSO) op het niveau van het gegevensvlak. Toegang tot het besturingsvlak voor Azure Cache voor Redis is beschikbaar via REST API en ondersteunt SSO. Als u wilt verifiëren, stelt u de kopautorisatie voor uw aanvragen in op een JSON-webtoken dat u in azure Active Directory hebt verkregen.

Informatie over Azure-cache voor Redis REST API:https://docs.microsoft.com/rest/api/redis/

SSO begrijpen met Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Multi-factor authenticatie gebruiken voor alle Azure Active Directory-gebaseerde toegang

**Richtlijnen:** Schakel Azure Active Directory (AD) Multi-Factor Authentication (MFA) in en volg aanbevelingen voor Azure Security Center Identity and Access Management.

Mfa inschakelen in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang bewaken binnen Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedicated machines (Privileged Access Workstations) gebruiken voor alle administratieve taken

**Richtlijnen:** Gebruik paws (Privileged Access Workstations) met Multi-Factor Authentication (MFA) geconfigureerd om in te loggen en Azure-bronnen te configureren.

Meer informatie over geprivilegieerde werkstations voor toegang:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Mfa inschakelen in Azure:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Inloggen en waarschuwen voor verdachte activiteiten vanuit administratieve accounts

**Richtlijnen:** Gebruik Azure Active Directory (AD) Privileged Identity Management (PIM) voor het genereren van logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving plaatsvinden.

Gebruik bovendien Azure AD-risicodetecties om waarschuwingen en rapporten over riskant gebruikersgedrag te bekijken.

Privileged Identity Management (PIM) implementeren:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Inzicht in Azure AD-risicodetecties:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources beheren vanaf alleen goedgekeurde locaties

**Richtlijnen**: Configureer benoemde locaties in Voorwaardelijke toegang van Azure Active Directory (AD) om alleen specifieke logische groeperingen van IP-adresbereiken of landen/regio's toe te staan.

Benoemde locaties configureren in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory gebruiken

**Richtlijnen:** Gebruik Azure Active Directory (AD) als het centrale verificatie- en autorisatiesysteem. Azure AD beschermt gegevens door sterke versleuteling te gebruiken voor gegevens in rust en onderweg. Azure AD zouten, hashes en slaat gebruikersreferenties ook veilig op.

Azure AD-verificatie kan niet worden gebruikt voor directe toegang tot Azure Cache voor het gegevensvlak van Redis, maar Azure AD-referenties kunnen worden gebruikt voor beheer op het niveau van het besturingsvlak (d.w.z. de Azure-portal) om Azure Cache voor Redis-toegangssleutels te beheren.


**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (AD) biedt logboeken waarmee u verouderde accounts ontdekken. Gebruik daarnaast Azure Identity Access Reviews om groepslidmaatschappen, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Gebruikerstoegang kan regelmatig worden beoordeeld om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben. 

Inzicht in Azure AD-rapportage:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure Identity Access-recensies gebruiken:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Pogingen om toegang te krijgen tot gedeactiveerde accounts controleren

**Richtlijnen:** u hebt toegang tot aanmeldingsactiviteit, controle- en risicogebeurtenislogboekbronnen van Azure Active Directory (AD), waarmee u integreren met Azure Sentinel of een SIEM van derden.

U dit proces stroomlijnen door diagnostische instellingen voor Azure AD-gebruikersaccounts te maken en de controlelogboeken en aanmeldingslogboeken naar een werkruimte log-Analyse te verzenden. U de gewenste logwaarschuwingen configureren binnen Log Analytics.

Azure-activiteitslogboeken integreren in Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Azure Sentinel aan boord:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Waarschuwing over afwijking van het inloggedrag van het account

**Richtlijnen:** Gebruik azure Active Directory (AD) Identiteitsbeveiliging en risicodetectiefuncties voor het afwijken van het inloggedrag van accounts in het controlevlak om geautomatiseerde antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot gebruikersidentiteiten. U ook gegevens opnemen in Azure Sentinel voor verder onderzoek.

Azure AD-riskante aanmeldingen weergeven:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Hoe u risicobeleid voor identiteitsbescherming configureert en inschakelt:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure Sentinel aan boord:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen**: Nog niet beschikbaar; Customer Lockbox wordt nog niet ondersteund voor Azure Cache voor Redis.

Lijst met door klanten ondersteunde services:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Niet van toepassing

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Beveiligingsbeheer: Gegevensbescherming](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie bijhouden

**Richtlijnen:** Gebruik tags om te helpen bij het bijhouden van Azure-bronnen die gevoelige informatie opslaan of verwerken.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen**: Implementeer afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, testen en productie. Azure Cache voor Redis-exemplaren moet worden gescheiden door virtueel netwerk/subnet en op de juiste manier worden getagd. Gebruik de Azure Cache voor Redis-firewall optioneel om regels te definiëren, zodat alleen clientverbindingen uit opgegeven IP-adresbereiken verbinding kunnen maken met de cache.

Extra Azure-abonnementen maken:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Managementgroepen maken:

https://docs.microsoft.com/azure/governance/management-groups/create

Azure-cache voor Redis implementeren in een Vnet:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Azure-cache configureren voor firewallregels van Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Toezicht houden op en blokkeren van ongeoorloofde overdracht van gevoelige informatie

**Richtlijnen**: Nog niet beschikbaar; gegevensidentificatie, classificatie en verliespreventiefuncties zijn nog niet beschikbaar voor Azure Cache voor Redis.

Microsoft beheert de onderliggende infrastructuur voor Azure Cache voor Redis en heeft strenge controles geïmplementeerd om het verlies of de blootstelling van klantgegevens te voorkomen.

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige informatie tijdens het transport versleutelen

**Richtlijnen:** Azure Cache voor Redis vereist standaard TLS-versleutelde communicatie. TLS-versies 1.0, 1.1 en 1.2 worden momenteel ondersteund. TLS 1.0 en 1.1 zijn echter op weg naar afschaffing industriebreed, dus gebruik TLS 1.2 indien mogelijk. Als uw clientbibliotheek of -tool TLS niet ondersteunt, kan het inschakelen van onversleutelde verbindingen worden gedaan via de Azure-portal of beheer-API's. In dergelijke gevallen waarin versleutelde verbindingen niet mogelijk zijn, wordt aanbevolen om uw cache en clienttoepassing in een virtueel netwerk te plaatsen.

Inzicht in versleuteling tijdens het transport voor Azure Cache voor Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-best-practices

Inzicht in vereiste poorten die worden gebruikt in Vnet-cachescenario's:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet#outbound-port-requirements

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Gebruik een actief detectiehulpmiddel om gevoelige gegevens te identificeren

**Richtlijnen**: Functies voor gegevensidentificatie, classificatie en verliespreventie zijn nog niet beschikbaar voor Azure Cache voor Redis. Tag exemplaren die gevoelige informatie als zodanig bevatten en implementeer oplossingen van derden indien vereist voor nalevingsdoeleinden.

Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot bronnen te beheren

**Richtlijnen**: Gebruik Azure Active Directory (AAD) role-based access control (RBAC) om de toegang tot het Azure Cache for Redis-besturingsvlak (d.w.z. Azure-portal) te beheren. 

RBAC configureren in Azure:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Gebruik hostgebaseerde preventie van gegevensverlies om toegangscontrole af te dwingen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

Microsoft beheert de onderliggende infrastructuur voor Azure Cache voor Redis en heeft strenge controles geïmplementeerd om het verlies of de blootstelling van klantgegevens te voorkomen.

Inzicht in de bescherming van klantgegevens in Azure:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Versleutel gevoelige informatie in rust

**Richtlijnen**: Azure Cache for Redis slaat klantgegevens op in het geheugen en hoewel het geheugen sterk wordt beschermd door veel besturingselementen die door Microsoft zijn geïmplementeerd, wordt het geheugen niet standaard versleuteld. Versleutel de inhoud, indien vereist door uw organisatie, voordat u deze opslaat in Azure Cache voor Redis.

Als u de Azure-cache voor Redis-functie 'Redis-gegevenspersistentie' gebruikt, worden gegevens verzonden naar een Azure Storage-account waarvan u eigenaar bent en beheert. U persistentie configureren vanuit het blade 'Nieuwe Azure Cache for Redis' tijdens het maken van de cache en in het resourcemenu voor bestaande premium caches.

Gegevens in Azure Storage worden versleuteld en transparant gedecodeerd met behulp van 256-bits AES-versleuteling, een van de sterkste blokcoderingen die beschikbaar zijn, en is FIPS 140-2-compatibel. Azure Storage-versleuteling kan niet worden uitgeschakeld. U vertrouwen op door Microsoft beheerde sleutels voor de versleuteling van uw opslagaccount, of u versleuteling beheren met uw eigen sleutels.

Persistentie configureren in Azure Cache voor Redis:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Versleuteling voor Azure Storage-accounts begrijpen:https://docs.microsoft.com/azure/storage/common/storage-service-encryption

Inzicht in azure-klantgegevensbescherming:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwing en waarschuwing over wijzigingen in kritieke Azure-resources

**Richtlijnen:** Gebruik Azure Monitor met het Azure Activity-logboek om waarschuwingen te maken voor wanneer er wijzigingen plaatsvinden in productie-exemplaren van Azure Cache voor Redis en andere kritieke of verwante resources.

Waarschuwingen maken voor gebeurtenissen in Azure Activity Log:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [Security Control: Vulnerability Management voor](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen van kwetsbaarheden uitvoeren

**Richtlijnen:** Volg aanbevelingen van Azure Security Center voor het beveiligen van uw Azure-cache voor Redis-exemplaren en gerelateerde bronnen.

Microsoft voert kwetsbaarheidsbeheer uit op de onderliggende systemen die Azure Cache voor Redis ondersteunen.

Informatie over aanbevelingen van Azure Security Center:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Gedeeld

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementatie van een oplossing voor het patchbeheer van geautomatiseerd besturingssysteem

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Geautomatiseerde softwarepatchbeheeroplossing van derden implementeren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Vergelijk back-to-back kwetsbaarheid scans

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een risicobeoordelingsproces om prioriteit te geven aan het herstel van ontdekte kwetsbaarheden

**Richtlijnen**: Microsoft voert kwetsbaarheidsbeheer uit op de onderliggende systemen die Azure Cache voor Redis ondersteunen.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Beveiligingsbeheer: Voorraadbeheer en Vermogensbeheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)voor meer informatie.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure Asset Discovery gebruiken

**Richtlijnen:** Gebruik Azure Resource Graph om alle bronnen (zoals compute, storage, netwerk, poorten en protocollen, enz.) binnen uw abonnement(en) op te vragen/te ontdekken.  Zorg voor de juiste (lees)machtigingen in uw tenant en opsommen van alle Azure-abonnementen en resources binnen uw abonnementen.

Hoewel klassieke Azure-bronnen kunnen worden ontdekt via Resource Graph, is het ten zeerste aan te raden om azure resource manager-resources in de toekomst te maken en te gebruiken.

Query's maken met Azure Resource Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weergeven:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC begrijpen:https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-bronnen met metagegevens om ze logisch te ordenen in een taxonomie.

Tags maken en gebruiken:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ongeautoriseerde Azure-bronnen verwijderen

**Richtlijnen**: Gebruik tagging, beheergroepen en afzonderlijke abonnementen, waar nodig, om Azure Cache voor Redis-exemplaren en gerelateerde bronnen te organiseren en bij te houden. Verzoen de voorraad regelmatig en zorg ervoor dat ongeautoriseerde bronnen tijdig uit het abonnement worden verwijderd.

Gebruik bovendien het Azure-beleid om beperkingen op te leggen aan het type resources dat kan worden gemaakt in klantabonnementen(en) met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane brontypen

- Toegestane brontypen

Extra Azure-abonnementen maken:https://docs.microsoft.com/azure/billing/billing-create-subscription

Managementgroepen maken:https://docs.microsoft.com/azure/governance/management-groups/create

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: Een inventaris bijhouden van goedgekeurde Azure-resources en softwaretitels

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources en Azure als geheel.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Monitor voor niet-goedgekeurde Azure-bronnen

**Richtlijnen:** Gebruik Azure-beleid om beperkingen op te leggen aan het type resources dat kan worden gemaakt in klantabonnementen(en) met behulp van de volgende ingebouwde beleidsdefinities:

Niet toegestane brontypen

Toegestane brontypen

Gebruik bovendien Azure Resource Graph om bronnen binnen het abonnement(en) op te vragen/te ontdekken.

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Query's maken met Azure Graph:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Monitor voor niet-goedgekeurde softwaretoepassingen binnen compute resources

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Niet-goedgekeurde Azure-bronnen en -softwaretoepassingen verwijderen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources en Azure als geheel.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="68-use-only-approved-applications"></a>6.8: Gebruik alleen goedgekeurde aanvragen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="69-use-only-approved-azure-services"></a>6.9: Alleen goedgekeurde Azure-services gebruiken

**Richtlijnen:** Gebruik Azure Policy om beperkingen op te leggen aan het type resources dat kan worden gemaakt in klantabonnementen(en) met behulp van de volgende ingebouwde beleidsdefinities:

Niet toegestane brontypen

Toegestane brontypen

Azure-beleid configureren en beheren:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek brontype weigeren met Azure-beleid:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6.10: Goedgekeurde aanvraaglijst implementeren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: De mogelijkheid van gebruikers beperken om via scripts met Azure Resources Manager te communiceren

**Richtlijnen**: Configureer voorwaardelijke toegang voor Azure om de interactie van gebruikers met Azure Resource Manager (ARM) te beperken door 'Bloktoegang' voor de Microsoft Azure Management-app te configureren.

Voorwaardelijke toegang configureren om toegang tot ARM te blokkeren:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Beperk de mogelijkheid van gebruikers om scripts uit te voeren binnen compute resources

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Toepassingen met een hoog risico fysiek of logisch scheiden

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="secure-configuration"></a>Veilige configuratie

*Zie [Beveiligingsbeheer: beveiligde configuratie](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)voor meer informatie.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Veilige configuraties instellen voor alle Azure-resources

**Richtlijnen:** standaardbeveiligingsconfiguraties voor uw Azure-cache voor Redis-exemplaren definiëren en implementeren met Azure Policy. Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.Cache' om aangepaste beleidsregels te maken om de configuratie van uw Azure-cache voor Redis-exemplaren te controleren of af te dwingen. U ook gebruik maken van ingebouwde beleidsdefinities met betrekking tot uw Azure-cache voor Redis-exemplaren, zoals:

Alleen beveiligde verbindingen met uw Redis-cache moeten worden ingeschakeld

Beschikbare Azure Policy-aliassen weergeven:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Veilige configuratie van het besturingssysteem instellen

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-bronconfiguraties behouden

**Richtlijnen**: Gebruik Azure-beleid [weigeren] en [implementeren als deze niet bestaan] om beveiligde instellingen voor uw Azure-bronnen af te dwingen.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Inzicht in Azure-beleidseffecten:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Veilige configuratie van het besturingssysteem behouden

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-bronnen veilig opslaan

**Richtlijnen:** Als u aangepaste Azure Policy-definities of Azure Resource Manager-sjablonen voor uw Azure-cache voor Redis-exemplaren en gerelateerde resources gebruikt, gebruikt u Azure Repos om uw code veilig op te slaan en te beheren.

Code opslaan in Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos-documentatie:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Afbeeldingen van aangepaste besturingssysteemen veilig opslaan

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Hulpprogramma's voor systeemconfiguratiebeheer implementeren

**Richtlijnen:** Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.Cache' om aangepaste beleidsregels te maken om systeemconfiguraties te waarschuwen, te controleren en af te dwingen. Bovendien ontwikkelt u een proces en pijplijn voor het beheren van beleidsuitzonderingen.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Systeemconfiguratiebeheertools implementeren voor besturingssystemen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-services

**Richtlijnen:** Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.Cache' om aangepaste beleidsregels te maken om systeemconfiguraties te waarschuwen, te controleren en af te dwingen. Gebruik Azure-beleid [audit], [weigeren] en [implementeren als deze niet bestaan] om configuraties voor uw Azure-cache voor Redis-exemplaren en gerelateerde bronnen automatisch af te dwingen.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking voor besturingssystemen implementeren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** Gebruik Managed Service Identity in combinatie met Azure Key Vault om Azure Cache voor beheer van Redis te vereenvoudigen en te beveiligen voor Azure App Service. Zorg ervoor dat Key Vault soft delete is ingeschakeld.

Hoe te integreren met Azure Managed Identities:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Een key vault maken:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

Key Vault-verificatie voorzien van een beheerde identiteit:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Gebruik Managed Service Identity in combinatie met Azure Key Vault om Azure Cache voor beheer van Redis te vereenvoudigen en te beveiligen voor Azure App Service. Zorg ervoor dat Key Vault Soft Delete is ingeschakeld.

Gebruik Beheerde identiteiten om Azure-services een automatisch beheerde identiteit te bieden in Azure Active Directory. Met Managed Identities u zich verifiëren voor elke service die AAD-verificatie ondersteunt, inclusief Azure Key Vault, zonder referenties in uw code.

Beheerde identiteiten configureren:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Hoe te integreren met Azure Managed Identities:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Onbedoelde blootstelling aan referenties elimineren

**Richtlijnen**: Implementeer credential scanner om referenties binnen code te identificeren. Credential Scanner stimuleert ook het verplaatsen van ontdekte referenties naar veiligere locaties zoals Azure Key Vault.

Credential Scanner instellen:https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="malware-defense"></a>Beveiliging tegen malware

*Zie [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense)voor meer informatie.*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Gebruik centraal beheerde anti-malwaresoftware

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

Microsoft-antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt (bijvoorbeeld Azure App Service), maar wordt niet uitgevoerd op inhoud van klanten.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: Vooraf scannen van bestanden die moeten worden geüpload naar niet-compute Azure-resources

**Richtlijnen**: Microsoft-antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt (bijvoorbeeld Azure Cache voor Redis), maar wordt niet uitgevoerd op inhoud van klanten.

Scan vooraf alle inhoud die wordt geüpload naar niet-compute Azure-bronnen, zoals App Service, Data Lake Storage, Blob Storage, Azure Database voor PostgreSQL, enz. Microsoft heeft in deze gevallen geen toegang tot uw gegevens.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Ervoor zorgen dat antivirussoftware en -handtekeningen worden bijgewerkt

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

Microsoft-antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt (bijvoorbeeld Azure Cache voor Redis), maar wordt niet uitgevoerd op inhoud van klanten.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Beveiligingsbeheer: Gegevensherstel](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zorg voor regelmatige geautomatiseerde back-ups

**Begeleiding**: Maak de persistentie van Redis mogelijk. Met de persistentie van Redis u doorgaan met gegevens die zijn opgeslagen in Redis. U ook snapshots maken en een back-up maken van de gegevens, die u laden in geval van een hardwarefout. Dit is een enorm voordeel ten opzichte van Basic of Standard laag waar alle gegevens worden opgeslagen in het geheugen en er kan potentieel verlies van gegevens in het geval van een storing waar Cache knooppunten zijn down.

U Azure Cache ook gebruiken voor Redis-export. Met exporteren u de gegevens die zijn opgeslagen in Azure Cache voor Redis exporteren naar RDB-bestanden die compatibel zijn met Redis. U deze functie gebruiken om gegevens van de ene Azure-cache voor Redis-instantie naar een andere of naar een andere Redis-server te verplaatsen. Tijdens het exportproces wordt een tijdelijk bestand gemaakt op de virtuele machine die de Azure-cache voor redis-serverinstantie host en wordt het bestand geüpload naar het aangewezen opslagaccount. Wanneer de exportbewerking is voltooid met een status van succes of fout, wordt het tijdelijke bestand verwijderd.

Hoe redis persistentie mogelijk te maken:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Azure-cache gebruiken voor Redis-exporteren:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Complete systeemback-ups uitvoeren en een back-up maken van beheerde sleutels van een klant

**Begeleiding**: Maak de persistentie van Redis mogelijk. Met de persistentie van Redis u doorgaan met gegevens die zijn opgeslagen in Redis. U ook snapshots maken en een back-up maken van de gegevens, die u laden in geval van een hardwarefout. Dit is een enorm voordeel ten opzichte van Basic of Standard laag waar alle gegevens worden opgeslagen in het geheugen en er kan potentieel verlies van gegevens in het geval van een storing waar Cache knooppunten zijn down.

U Azure Cache ook gebruiken voor Redis-export. Met exporteren u de gegevens die zijn opgeslagen in Azure Cache voor Redis exporteren naar RDB-bestanden die compatibel zijn met Redis. U deze functie gebruiken om gegevens van de ene Azure-cache voor Redis-instantie naar een andere of naar een andere Redis-server te verplaatsen. Tijdens het exportproces wordt een tijdelijk bestand gemaakt op de virtuele machine die de Azure-cache voor redis-serverinstantie host en wordt het bestand geüpload naar het aangewezen opslagaccount. Wanneer de exportbewerking is voltooid met een status van succes of fout, wordt het tijdelijke bestand verwijderd.

Als u Azure Key Vault gebruikt om referenties voor uw Azure-cache voor Redis-exemplaren op te slaan, moet u zorgen voor regelmatige geautomatiseerde back-ups van uw sleutels.

Hoe redis persistentie mogelijk te maken:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Azure-cache gebruiken voor Redis-exporteren:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Een back-up maken van Key Vault Keys:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen**: Gebruik Azure Cache voor Redis Import. Import kan worden gebruikt om Redis-compatibele RDB-bestanden van elke Redis-server te brengen die in elke cloud of omgeving wordt uitgevoerd, waaronder Redis die wordt uitgevoerd op Linux, Windows of een cloudprovider zoals Amazon Web Services en anderen. Het importeren van gegevens is een eenvoudige manier om een cache te maken met vooraf ingevulde gegevens. Tijdens het importproces laadt Azure Cache voor Redis de RDB-bestanden uit Azure-opslag in het geheugen en voegt u de sleutels vervolgens in de cache in.

Test regelmatig het herstel van gegevens van uw Azure Key Vault-geheimen.

Azure-cache gebruiken voor Importeren van Redis:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

Key Vault Secrets herstellen:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zorg voor bescherming van back-ups en door klanten beheerde sleutels

**Richtlijnen:** Azure Cache voor Redis-back-ups van Redis Export en Redis-persistentie worden opgeslagen in uw geselecteerde Azure Storage-account. Gegevens in Azure Storage worden versleuteld en transparant gedecodeerd met behulp van 256-bits AES-versleuteling, een van de sterkste blokcoderingen die beschikbaar zijn, en is FIPS 140-2-compatibel. Azure Storage-versleuteling kan niet worden uitgeschakeld. U vertrouwen op door Microsoft beheerde sleutels voor de versleuteling van uw opslagaccount, of u versleuteling beheren met uw eigen sleutels.

Versleuteling voor Azure Storage-accounts begrijpen:https://docs.microsoft.com/azure/storage/common/storage-service-encryption

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Microsoft

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Beveiligingscontrole: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een gids voor incidentrespons maken

**Richtlijnen**: Bouw een incidentresponseguide voor uw organisatie. Zorg ervoor dat er geschreven incidentresponseplannen zijn die alle rollen van personeel definiëren, evenals fasen van incidentafhandeling/beheer, van detectie tot beoordeling na incidenten.

Werkstroomautomatiseringen configureren binnen Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Richtlijnen voor het bouwen van uw eigen beveiligingsincidentresponsproces:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

De anatomie van een incident door het Microsoft Security Response Center:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

De klant kan ook gebruikmaken van NIST's Computer Security Incident Handling Guide om te helpen bij het maken van hun eigen incident response plan:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een incidentscore- en prioriteringsprocedure maken

**Richtlijnen**: Security Center wijst een ernst toe aan elke waarschuwing om u te helpen prioriteiten te stellen welke waarschuwingen eerst moeten worden onderzocht. De ernst is gebaseerd op hoe zeker Security Center is in de bevinding of de analytische gebruikt om de waarschuwing en het betrouwbaarheidsniveau dat er kwaadaardige bedoelingen achter de activiteit die leidde tot de waarschuwing.

Bovendien, duidelijk markeren abonnementen (voor bijvoorbeeld. productie, niet-prod) en maak een naamgevingssysteem om Azure-resources duidelijk te identificeren en te categoriseren.

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor beveiligingsrespons testen

**Richtlijnen**: Voer oefeningen uit om de incidentresponsmogelijkheden van uw systemen op een regelmatige cadans te testen. Identificeer zwakke punten en hiaten en herzie het plan indien nodig.

Raadpleeg de publicatie van NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens beveiligingsincidenten verstrekken en waarschuwingsmeldingen configureren voor beveiligingsincidenten

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) ontdekt dat de gegevens van de klant zijn geraadpleegd door een onwettige of onbevoegde partij.  Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost.

De beveiligingscontactpersoon azure security center instellen:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw incidentresponsesysteem

**Richtlijnen:** exporteer uw Azure Security Center-waarschuwingen en -aanbevelingen met de functie Continue exporteren. Met Continue export u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U de Azure Security Center-gegevensconnector gebruiken om de waarschuwingen sentinel te streamen.

Continue export configureren:

https://docs.microsoft.com/azure/security-center/continuous-export

Waarschuwingen streamen naar Azure Sentinel:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatiseer de reactie op beveiligingswaarschuwingen

**Richtlijnen:** Gebruik de functie Workflowautomatisering in Azure Security Center om automatisch reacties te activeren via 'Logic Apps' over beveiligingswaarschuwingen en aanbevelingen.

Workflowautomatisering en logische apps configureren:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Security Control: Penetration Tests en Red Team Exercises](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Voer regelmatig penetratietests uit van uw Azure-resources en zorg voor herstel van alle kritieke beveiligingsbevindingen binnen 60 dagen

**Richtlijnen:** Volg de Microsoft Rules of Engagement om ervoor te zorgen dat uw penetratietests niet in strijd zijn met het Microsoft-beleid:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

Meer informatie over de strategie en uitvoering van Red Teaming en live site penetratietesten van Microsoft met microsoft beheerde cloudinfrastructuur, services en toepassingen vindt u hier: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Gedeeld

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Meer informatie over [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
