---
title: Azure-beveiligingsbasislijn voor gebeurtenishubs
description: Azure-beveiligingsbasislijn voor gebeurtenishubs
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 10fc822f9f36512405dd4e3b5aba6270b53e163f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549035"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Azure-beveiligingsbasislijn voor gebeurtenishubs

De Azure Security Baseline for Event Hubs bevat aanbevelingen waarmee u de beveiligingshouding van uw implementatie verbeteren.

De basislijn voor deze service is afkomstig van de [Azure Security Benchmark versie 1.0](https://docs.microsoft.com/azure/security/benchmarks/overview), die aanbevelingen geeft over hoe u uw cloudoplossingen op Azure beveiligen met onze richtlijnen voor best practices.

Zie [overzicht azure security baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)voor meer informatie.

## <a name="network-security"></a>Netwerkbeveiliging

*Zie [Beveiligingsbeheer: netwerkbeveiliging](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)voor meer informatie.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Resources beveiligen met netwerkbeveiligingsgroepen of Azure Firewall in uw virtuele netwerk

**Richtlijnen:** De integratie van gebeurtenishubs met eindpunten voor virtuele netwerkservices maakt veilige toegang tot berichtenmogelijkheden mogelijk van workloads zoals virtuele machines die gebonden zijn aan virtuele netwerken, waarbij het netwerkverkeer pad aan beide uiteinden wordt beveiligd.

Eenmaal gebonden aan ten minste één eindpunt van de subnetservice voor virtuele netwerken, accepteert de respectievelijke naamruimte van gebeurtenishubs niet langer verkeer van overal, maar geautoriseerde subnetten in virtuele netwerken. Vanuit het perspectief van het virtuele netwerk configureert u de naamruimte van uw Gebeurtenishubs aan een serviceeindpunt en configureert u een geïsoleerde netwerktunnel van het virtuele netwerksubnet naar de berichtenservice. 

U ook een privéeindpunt maken, een netwerkinterface die u privé en veilig verbindt met de Azure Event Hubs-service met behulp van de Azure Private Link-service. Het privéeindpunt maakt gebruik van een privé-IP-adres van uw VNet, waardoor de service effectief in uw VNet wordt opgenomen. Al het verkeer naar de service kan worden doorgestuurd via het privéeindpunt, zodat er geen gateways, NAT-apparaten, ExpressRoute- of VPN-verbindingen of openbare IP-adressen nodig zijn. 

U ook de naamruimte van uw Azure Event Hubs beveiligen met behulp van firewalls. Azure Event Hubs ondersteunt IP-gebaseerde toegangsbesturingselementen voor inkomende firewallondersteuning. U firewallregels instellen met behulp van de Azure-portal, Azure Resource Manager-sjablonen of via Azure CLI of Azure PowerShell.

Eindpunten voor virtuele netwerkservices gebruiken met Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Zie Azure Event Hubs integreren met Azure https://docs.microsoft.com/azure/event-hubs/private-link-servicePrivate Link voor meer informatie: .

Integratie van virtuele netwerken en firewalls inschakelen op naamruimte van gebeurtenishubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Ip-firewallregels configureren voor naamruimten van Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: De configuratie en het verkeer van Vnets, Subnetten en NIC's controleren en registreren

**Richtlijnen:** Gebruik Azure Security Center en volg aanbevelingen voor netwerkbeveiliging om uw Event Hubs-bronnen in Azure te beveiligen. Als u azure-virtuele machines gebruikt om toegang te krijgen tot uw gebeurtenishubs, schakelt u netwerkbeveiligingsgroep (NSG)-stroomlogboeken in en verzendt u logboeken naar een opslagaccount voor verkeerscontrole.

NSG-stroomlogboeken inschakelen:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Inzicht in netwerkbeveiliging van Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="13-protect-critical-web-applications"></a>1.3: Kritieke webapplicaties beschermen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Communicatie met bekende kwaadaardige IP-adressen weigeren

**Richtlijnen:** Schakel De DDoS Protection Standard in op de virtuele netwerken die zijn gekoppeld aan uw gebeurtenishubs om te beschermen tegen ddos-aanvallen (distributed denial-of-service). Gebruik Azure Security Center Integrated Threat Intelligence om communicatie met bekende kwaadaardige of ongebruikte internetIP-adressen te weigeren.

DDoS-beveiliging configureren:[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](/azure/virtual-network/manage-ddos-protection)

Voor meer informatie over de geïntegreerde bedreigingsinformatie van azure security center:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Netwerkpakketten en stroomlogboeken opnemen

**Richtlijnen:** Als u virtuele Azure-machines gebruikt om toegang te krijgen tot uw gebeurtenishubs, schakelt u netwerkbeveiligingsgroep (NSG)-stroomlogboeken in en verzendt u logboeken naar een opslagaccount voor verkeerscontrole. U ook NSG-stroomlogboeken naar een log-analysewerkruimte verzenden en Traffic Analytics gebruiken om inzicht te geven in de verkeersstroom in uw Azure-cloud. Enkele voordelen van Traffic Analytics zijn de mogelijkheid om netwerkactiviteiten te visualiseren en hotspots te identificeren, beveiligingsbedreigingen te identificeren, verkeersstroompatronen te begrijpen en netwerkverkeerde configuraties te lokaliseren.

Schakel Network Watcher-pakketopname in indien nodig voor het onderzoeken van afwijkende activiteiten.

NSG-stroomlogboeken inschakelen:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Traffic Analytics inschakelen en gebruiken:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Zo schakel je Network Watcher in:https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Netwerkgebaseerde inbraakdetectie-/inbraakpreventiesystemen (IDS/IPS) implementeren

**Richtlijnen:** Als u azure-virtuele machines gebruikt om toegang te krijgen tot uw gebeurtenishubs, selecteert u een aanbieding in de Azure Marketplace die IDS/IPS-functionaliteit ondersteunt met payload-inspectiemogelijkheden. Als inbraakdetectie en/of preventie op basis van payload-inspectie niet vereist is voor uw organisatie, u de ingebouwde firewallfunctie van Azure Event Hubs gebruiken. U de naamruimte van uw Gebeurtenishubs beperken voor een beperkt aantal IP-adressen of een specifiek IP-adres met behulp van Firewall-regels.

Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Een firewallregel toevoegen in gebeurtenishubs voor een opgegeven IP-adres:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Azure Security Center-bewaking**: Nog niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Verkeer naar webtoepassingen beheren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: Minimaliseer de complexiteit en administratieve overhead van netwerkbeveiligingsregels

**Richtlijnen**: Deze aanbeveling is niet van toepassing en is bedoeld voor webtoepassingen die worden uitgevoerd op Azure App Service of rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Standaardbeveiligingsconfiguraties voor netwerkapparaten onderhouden

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor netwerkresources die zijn gekoppeld aan naamruimten van uw Azure Event Hubs met Azure Policy. Gebruik Azure Policy-aliassen in de naamruimten 'Microsoft.EventHub' en 'Microsoft.Network' om aangepaste beleidsregels te maken om de netwerkconfiguratie van de naamruimten van uw Gebeurtenishubs te controleren of af te dwingen. U ook gebruik maken van ingebouwde beleidsdefinities met betrekking tot Azure Event Hubs, zoals:

- Event Hub moet een eindpunt voor virtuele netwerkservice gebruiken.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Ingebouwd beleid voor naamruimte gebeurtenishubs:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Voorbeelden van Azure-beleid voor netwerken:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Een Azure Blueprint maken:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="110-document-traffic-configuration-rules"></a>1.10: Regels voor documentconfiguratie

**Richtlijnen:** Gebruik tags voor virtuele netwerken en andere bronnen die verband houden met netwerkbeveiliging en verkeersstroom die zijn gekoppeld aan uw gebeurtenishubs.

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Gebruik geautomatiseerde hulpprogramma's om netwerkbronconfiguraties te bewaken en wijzigingen te detecteren

**Richtlijnen:** Gebruik Azure Activity Log om netwerkbronconfiguraties te bewaken en wijzigingen te detecteren voor netwerkbronnen met betrekking tot Azure Event Hubs. Maak waarschuwingen in Azure Monitor die worden geactiveerd wanneer er wijzigingen plaatsvinden in kritieke netwerkbronnen.

Azure Activity Log-gebeurtenissen weergeven en ophalen:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Waarschuwingen maken in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

*Zie [Beveiligingscontrole: Logboekregistratie en -controle](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring)voor meer informatie.*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Gebruik goedgekeurde tijdsynchronisatiebronnen

**Richtsnoeren**: Niet van toepassing; Microsoft onderhoudt de tijdsbron die wordt gebruikt voor Azure-bronnen, zoals Azure Event Hubs, voor tijdstempels in de logboeken.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: Centraal beheer van beveiligingslogboeken configureren

**Richtlijnen:** Configureer binnen Azure Monitor logboeken met betrekking tot gebeurtenishubs binnen de diagnostische instellingen activiteitslogboek en gebeurtenishub om logboeken naar een Log Analytics-werkruimte te verzenden die moeten worden opgevraagd of naar een opslagaccount voor archiveringsopslag op lange termijn.

Diagnostische instellingen voor Azure-gebeurtenishubs configureren:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Informatie over Azure-activiteitenlogboek:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Controlelogboekregistratie inschakelen voor Azure-resources

**Richtlijnen:** Schakel diagnostische instellingen in voor de naamruimte van uw Azure Event Hubs. Er zijn drie categorieën diagnostische instellingen voor Azure Event Hubs: Archive Logs, Operational Logs en AutoScale Logs. Operationele logboeken inschakelen om informatie vast te leggen over wat er gebeurt tijdens gebeurtenishubsbewerkingen, met name het bewerkingstype, inclusief het maken van gebeurtenishubs, gebruikte resources en de status van de bewerking.

Bovendien u diagnostische instellingen voor Azure Activity Log inschakelen en deze verzenden naar een Azure Storage-account, gebeurtenishub of een Werkruimte logboekanalyse. Activiteitslogboeken geven inzicht in de bewerkingen die zijn uitgevoerd op uw Azure Event Hubs en andere resources. Met behulp van activiteitslogboeken u bepalen wat, wie en wanneer voor schrijfbewerkingen (PUT, POST, DELETE) die zijn genomen op de naamruimten van uw Azure Event Hubs.

Diagnostische instellingen voor Azure-gebeurtenishubs inschakelen:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Diagnostische instellingen voor Azure Activity Log inschakelen:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Beveiligingslogboeken verzamelen van besturingssystemen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="25-configure-security-log-storage-retention"></a>2.5: Opslagbehoud beveiligingslogboeken configureren

**Richtlijnen:** Stel in Azure Monitor de bewaartermijn van uw Log Analytics-werkruimte in volgens de nalevingsvoorschriften van uw organisatie om gebeurtenishubgerelateerde incidenten vast te leggen en te beoordelen.

Parameters voor logboekbehoud instellen voor Logboekanalyse-werkruimten:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="26-monitor-and-review-logs"></a>2.6: Logboeken controleren en controleren

**Richtlijnen:** Analyseer en monitor logboeken op afwijkend gedrag en bekijk regelmatig resultaten met betrekking tot uw gebeurtenishubs. Gebruik logboekanalyses van Azure Monitor om logboeken te controleren en query's uit te voeren op logboekgegevens. U ook gegevens aan boord inschakelen en aan boord maken voor Azure Sentinel of een SIEM van derden.
 

Voor meer informatie over de werkruimte Log Analytics:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Aangepaste query's uitvoeren in Azure Monitor:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Azure Sentinel aan boord:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Waarschuwingen inschakelen voor afwijkende activiteit

**Richtlijnen:** Configureer in Azure Monitor logboeken met betrekking tot Azure Event Hubs in het activiteitenlogboek en diagnostische instellingen voor gebeurtenishubs om logboeken naar een Log Analytics-werkruimte te verzenden die moeten worden opgevraagd of naar een opslagaccount voor archiveringsopslag op lange termijn. Gebruik de werkruimte Log Analytics om waarschuwingen te maken voor afwijkende activiteiten in beveiligingslogboeken en gebeurtenissen.

U ook gegevens aan Azure Sentinel inschakelen en aan boord maken. 

Inzicht in het Azure-activiteitenlogboek:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Diagnostische instellingen voor Azure-gebeurtenishubs configureren:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Waarschuwing op logboeklogboekgegevens van Log Analytics:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Azure Sentinel aan boord:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-bewaking**: Nog niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centraliseer anti-malware logging

**Richtsnoeren**: Niet van toepassing; Event Hub verwerkt geen anti-malware logging.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="29-enable-dns-query-logging"></a>2.9: DNS-querylogboekregistratie inschakelen

**Richtsnoeren**: Niet van toepassing; Event Hubs verwerkt of produceert geen DNS-gerelateerde logboeken.

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

**Richtlijnen:** Beheer de toegang tot gebeurtenishubs voor vliegtuigen via Azure Active Directory (AD). Azure AD heeft niet het concept van standaardwachtwoorden.

Toegang tot gebeurtenishubs wordt beheerd via Azure AD met beheerde identiteiten of app-registraties en handtekeningen voor gedeelde toegang. Gedeelde toegangshandtekeningen worden gebruikt door de clients die verbinding maken met uw gebeurtenishubs en kunnen op elk gewenst moment worden geregenereerd.

Lees de handtekeningen voor gedeelde toegang voor gebeurtenishubs:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

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

**Richtlijnen:** Microsoft Azure biedt geïntegreerd toegangscontrolebeheer voor resources en toepassingen op basis van Azure Active Directory (AD). Een belangrijk voordeel van het gebruik van Azure AD met Azure Event Hubs is dat u uw referenties niet meer in de code hoeft op te slaan. In plaats daarvan u een OAuth 2.0-toegangstoken aanvragen bij het Microsoft Identity-platform. De resourcenaam om een https://eventhubs.azure.net/token aan te vragen is . Azure AD verifieert de beveiligingsprincipal (een gebruiker, groep of serviceprincipal) waarop de toepassing wordt uitgevoerd. Als de verificatie slaagt, retourneert Azure AD een toegangstoken naar de toepassing en kan de toepassing vervolgens het toegangstoken gebruiken om het verzoek tot Azure Event Hubs-bronnen te autoriseren.

Een toepassing verifiëren met Azure AD om toegang te krijgen tot bronnen van Gebeurtenishubs:https://docs.microsoft.com/azure/event-hubs/authenticate-application

SSO begrijpen met Azure AD:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Multi-factor authenticatie gebruiken voor alle Azure Active Directory-gebaseerde toegang

**Richtlijnen:** Schakel MFA (Azure Active Directory Multi-Factor Authentication) in en volg aanbevelingen voor Azure Security Center Identiteit en toegangsbeheer om uw resources met eventhub te beschermen.

Mfa inschakelen in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Identiteit en toegang bewaken binnen Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedicated machines (Privileged Access Workstations) gebruiken voor alle administratieve taken

**Richtlijnen:** Gebruik paw-workstations (Privileged Access, met Multi-Factor Authentication (MFA) geconfigureerd om in te loggen en resources met gebeurtenishub te configureren.

Meer informatie over geprivilegieerde werkstations voor toegang:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Mfa inschakelen in Azure:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Inloggen en waarschuwen voor verdachte activiteiten vanuit administratieve accounts

**Richtlijnen:** Gebruik Azure Active Directory (AD) Privileged Identity Management (PIM) voor het genereren van logboeken en waarschuwingen wanneer verdachte of onveilige activiteiten in de omgeving plaatsvinden. Gebruik Azure AD-risicodetecties om waarschuwingen en rapporten over riskant gebruikersgedrag weer te geven. Stuur azure security center-risicodetectiewaarschuwingen voor extra logboekregistratie naar Azure Monitor en configureer aangepaste waarschuwingen/meldingen met behulp van actiegroepen.

Privileged Identity Management (PIM) implementeren:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Inzicht in Azure AD-risicodetecties:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Actiegroepen configureren voor aangepaste waarschuwingen en meldingen:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Azure-resources beheren vanaf alleen goedgekeurde locaties

**Richtlijnen**: Gebruik locaties met voorwaardelijke toegang om alleen specifieke logische groeperingen van IP-adresbereiken of landen/regio's toegang te verlenen.



Benoemde locaties configureren in Azure:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory gebruiken

**Richtlijnen:** Gebruik Azure Active Directory (AD) als het centrale verificatie- en autorisatiesysteem voor Azure-bronnen, zoals Gebeurtenishubs. Dit maakt rolgebaseerde toegangscontrole (RBAC) mogelijk voor administratieve gevoelige resources.

 Een Azure AD-exemplaar maken en configureren:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Zie Toegang tot Event Hubs-bronnen beheren met Azure Active Directory:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Regelmatig gebruikerstoegang controleren en afstemmen

**Richtlijnen:** Azure Active Directory (AD) biedt logboeken waarmee u verouderde accounts ontdekken. Gebruik daarnaast Azure Identity Access Reviews om groepslidmaatschappen, toegang tot bedrijfstoepassingen en roltoewijzingen efficiënt te beheren. Gebruikerstoegang kan regelmatig worden beoordeeld om ervoor te zorgen dat alleen de juiste gebruikers toegang hebben.

Rouleren de handtekeningen voor gedeelde toegangsvan je eventhubs regelmatig.

Inzicht in Azure AD-rapportage:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Azure Identity Access-recensies gebruiken:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Inzicht in gedeelde toegangshandtekeningen voor gebeurtenishubs:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Pogingen om toegang te krijgen tot gedeactiveerde accounts controleren

**Richtlijnen:** U hebt toegang tot aanmeldingsactiviteit, controle- en risicogebeurtenislogboekbronnen van Azure Active Directory (AD), waarmee u integreren met een SIEM/Monitoring-hulpprogramma.

U dit proces stroomlijnen door diagnostische instellingen voor Azure AD-gebruikersaccounts te maken en de controlelogboeken en aanmeldingslogboeken naar een werkruimte log-Analyse te verzenden. U de gewenste logwaarschuwingen configureren binnen Log Analytics.

Azure-activiteitslogboeken integreren in Azure Monitor:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Toegang tot bronnen voor gebeurtenishubs autoriseren met Azure Active Directory:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Waarschuwing over afwijking van het inloggedrag van het account

**Richtlijnen:** Gebruik de functies Voor identiteitsbescherming en risicodetectie van Azure Active Directory om geautomatiseerde antwoorden te configureren op gedetecteerde verdachte acties met betrekking tot de resources die uw gebeurtenishubs heeft ingeschakeld. U moet geautomatiseerde antwoorden via Azure Sentinel inschakelen om de beveiligingsreacties van uw organisatie te implementeren.

Azure AD-riskante aanmeldingen weergeven:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Hoe u risicobeleid voor identiteitsbescherming configureert en inschakelt:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Azure Sentinel aan boord:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: Microsoft toegang geven tot relevante klantgegevens tijdens ondersteuningsscenario's

**Richtlijnen**: Momenteel niet beschikbaar; Customer Lockbox wordt nog niet ondersteund voor Event Hubs.

Lijst met door klanten ondersteunde services:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Momenteel niet beschikbaar

## <a name="data-protection"></a>Gegevensbeveiliging

*Zie [Beveiligingsbeheer: Gegevensbescherming](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)voor meer informatie.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Een inventaris van gevoelige informatie bijhouden

**Richtlijnen:** Gebruik tags op resources die betrekking hebben op uw Gebeurtenishubs om te helpen bij het bijhouden van Azure-bronnen die gevoelige informatie opslaan of verwerken.

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Systemen isoleren die gevoelige informatie opslaan of verwerken

**Richtlijnen**: Implementeer afzonderlijke abonnementen en/of beheergroepen voor ontwikkeling, testen en productie. Naamruimten voor gebeurtenishubs moeten worden gescheiden door een virtueel netwerk met serviceeindpunten die op de juiste manier zijn ingeschakeld en getagd.

U uw Azure Event Hubs-naamruimte ook beveiligen met behulp van firewalls. Azure Event Hubs ondersteunt IP-gebaseerde toegangsbesturingselementen voor inkomende firewallondersteuning. U firewallregels instellen met behulp van de Azure-portal, Azure Resource Manager-sjablonen of via Azure CLI of Azure PowerShell.

Extra Azure-abonnementen maken:https://docs.microsoft.com/azure/billing/billing-create-subscription

Managementgroepen maken:https://docs.microsoft.com/azure/governance/management-groups/create

IP-firewallregels configureren voor naamruimten van Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Een virtueel netwerk maken:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Toezicht houden op en blokkeren van ongeoorloofde overdracht van gevoelige informatie

**Richtlijnen:** Wanneer u virtuele machines gebruikt om toegang te krijgen tot uw gebeurtenishubs, maakt u gebruik van virtuele netwerken, serviceeindpunten, firewall van Event Hubs, netwerkbeveiligingsgroepen en servicetags om de mogelijkheid van gegevensexfiltratie te beperken.

Microsoft beheert de onderliggende infrastructuur voor Azure Event Hubs en heeft strenge controles geïmplementeerd om verlies of blootstelling van klantgegevens te voorkomen.

IP-firewallregels configureren voor naamruimten van Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Informatie over eindpunten van virtual network service met Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Azure-gebeurtenishubs integreren met Azure Private Link:https://docs.microsoft.com/azure/event-hubs/private-link-service

Inzicht in netwerkbeveiligingsgroepen en servicetags:https://docs.microsoft.com/azure/virtual-network/security-overview

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Alle gevoelige informatie tijdens het transport versleutelen

**Richtlijnen:** Azure Event Hubs dwingt tls-versleutelde communicatie standaard af. TLS-versies 1.0, 1.1 en 1.2 worden momenteel ondersteund. TLS 1.0 en 1.1 zijn echter op weg naar afschaffing industriebreed, dus gebruik TLS 1.2 indien mogelijk.

Zie Netwerkbeveiliging voor meer informatie over beveiligingsfuncties van gebeurtenishubs:https://docs.microsoft.com/azure/event-hubs/network-security

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Gebruik een actief detectiehulpmiddel om gevoelige gegevens te identificeren

**Richtlijnen**: Functies voor gegevensidentificatie, classificatie en verliespreventie zijn nog niet beschikbaar voor Azure Event Hubs. Implementeer oplossingen van derden indien nodig voor nalevingsdoeleinden.

Voor het onderliggende platform dat wordt beheerd door Microsoft, Microsoft behandelt alle klantinhoud als gevoelig en gaat tot het uiterste om te waken tegen verlies van klantgegevens en blootstelling. Om ervoor te zorgen dat klantgegevens binnen Azure veilig blijven, heeft Microsoft een reeks robuuste besturingselementen en mogelijkheden voor gegevensbescherming geïmplementeerd en onderhouden.

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Gedeeld

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Azure RBAC gebruiken om de toegang tot bronnen te beheren

**Richtlijnen**: Azure Event Hubs ondersteunt het gebruik van Azure Active Directory (AD) om aanvragen voor gebeurtenishubs-bronnen te autoriseren. Met Azure AD u RBAC (Role-based access control) gebruiken om machtigingen toe te kennen aan een beveiligingsprincipal, die mogelijk een gebruiker of een principal van de toepassingsservice is.

Inzicht in Azure AD RBAC en beschikbare rollen voor Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Gebruik hostgebaseerde preventie van gegevensverlies om toegangscontrole af te dwingen

**Richtsnoeren**: Niet van toepassing; deze richtlijn is bedoeld voor rekenresources.

Microsoft beheert de onderliggende infrastructuur voor Event Hubs en heeft strenge controles geïmplementeerd om verlies of blootstelling van klantgegevens te voorkomen.

Inzicht in de bescherming van klantgegevens in Azure:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Versleutel gevoelige informatie in rust

**Richtlijnen:** Azure Event Hubs ondersteunt de mogelijkheid om gegevens in rust te versleutelen met door Microsoft beheerde sleutels of door de klant beheerde sleutels. Met deze functie u de toegang tot de door de klant beheerde sleutels maken, roteren, uitschakelen en intrekken die worden gebruikt voor het versleutelen van Azure Event Hubs-gegevens in rust.

Sleutels configureren voor het versleutelen van Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Logboeken en waarschuwing en waarschuwing over wijzigingen in kritieke Azure-resources

**Richtlijnen**: Gebruik Azure Monitor met het Azure Activity-logboek om waarschuwingen te maken voor wanneer er wijzigingen plaatsvinden in productie-exemplaren van Azure Event Hubs en andere kritieke of gerelateerde bronnen.

Waarschuwingen maken voor gebeurtenissen in Azure Activity Log:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="vulnerability-management"></a>Beheer van beveiligingsproblemen

*Zie [Security Control: Vulnerability Management voor](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)meer informatie.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Geautomatiseerde hulpprogramma's voor het scannen van kwetsbaarheden uitvoeren

**Richtsnoeren**: Niet van toepassing; Microsoft voert kwetsbaarheidsbeheer uit op de onderliggende systemen die Event Hubs ondersteunen.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implementatie van een oplossing voor het patchbeheer van geautomatiseerd besturingssysteem

**Richtsnoeren**: Niet van toepassing; Microsoft voert patchbeheer uit op de onderliggende systemen die Event Hubs ondersteunen.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Geautomatiseerde softwarepatchbeheeroplossing van derden implementeren

**Richtsnoeren**: Niet van toepassing; benchmark is bedoeld voor compute resources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Vergelijk back-to-back kwetsbaarheid scans

**Richtsnoeren**: Niet van toepassing; Microsoft voert kwetsbaarheidsbeheer uit op de onderliggende systemen die Event Hubs ondersteunen.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Gebruik een risicobeoordelingsproces om prioriteit te geven aan het herstel van ontdekte kwetsbaarheden

**Richtsnoeren**: Niet van toepassing; Microsoft voert kwetsbaarheidsbeheer uit op de onderliggende systemen die Event Hubs ondersteunen.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Microsoft

## <a name="inventory-and-asset-management"></a>Inventarisatie en Asset Management

*Zie [Beveiligingsbeheer: Voorraadbeheer en Vermogensbeheer](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management)voor meer informatie.*

### <a name="61-use-azure-asset-discovery"></a>6.1: Azure Asset Discovery gebruiken

**Richtlijnen:** Gebruik Azure Resource Graph om alle resources (inclusief naamruimten van Azure Event Hubs) in uw abonnement(en) op te vragen en te ontdekken. Zorg ervoor dat u over de juiste (lees)machtigingen in uw tenant beschikt en dat u alle Azure-abonnementen en resources binnen uw abonnementen opsommen.

Query's maken met Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Uw Azure-abonnementen weergeven:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Azure RBAC begrijpen:https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="62-maintain-asset-metadata"></a>6.2: Metagegevens van activa onderhouden

**Richtlijnen:** Tags toepassen op Azure-bronnen met metagegevens om ze logisch te ordenen in een taxonomie.

Tags maken en gebruiken:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Ongeautoriseerde Azure-bronnen verwijderen

**Richtlijnen**: Gebruik tagging, beheergroepen en afzonderlijke abonnementen, waar nodig, om naamruimten en gerelateerde bronnen van Azure Event Hubs te organiseren en bij te houden. Verzoen de voorraad regelmatig en zorg ervoor dat ongeautoriseerde bronnen tijdig uit het abonnement worden verwijderd.

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

- Niet toegestane brontypen

- Toegestane brontypen

Gebruik bovendien Azure Resource Graph om bronnen binnen het abonnement(en) op te vragen/te ontdekken.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Query's maken met Azure Graph:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

**Richtlijnen:** Gebruik Azure-beleid om beperkingen op te leggen aan het type resources dat kan worden gemaakt in klantabonnementen(en) met behulp van de volgende ingebouwde beleidsdefinities:

- Niet toegestane brontypen

- Toegestane brontypen

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Een specifiek brontype weigeren met Azure-beleid:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="610-implement-approved-application-list"></a>6.10: Goedgekeurde aanvraaglijst implementeren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>De mogelijkheid van gebruikers beperken om via scripts met Azure Resource Manager te communiceren</div>

**Richtlijnen**: Configureer voorwaardelijke toegang voor Azure om de interactie van gebruikers met Azure Resource Manager te beperken door 'Bloktoegang' voor de Microsoft Azure Management-app te configureren.

Voorwaardelijke toegang configureren om toegang tot Azure Resource Manager te blokkeren:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

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

**Richtlijnen:** standaardbeveiligingsconfiguraties definiëren en implementeren voor uw Azure Event Hubs-implementaties. Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.EventHub' om aangepaste beleidsregels te maken om configuraties te controleren of af te dwingen. U ook gebruik maken van ingebouwde beleidsdefinities voor Azure Event Hubs, zoals:

- Diagnostische logboeken in gebeurtenishub moeten zijn ingeschakeld

- Event Hub moet een eindpunt voor virtuele netwerkservice gebruiken

Azure Ingebouwd beleid voor naamruimte gebeurtenishubs:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Beschikbare Azure-beleidsaliassen weergeven:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Veilige configuratie van het besturingssysteem instellen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Veilige Azure-bronconfiguraties behouden

**Richtlijnen**: Gebruik Azure-beleid [weigeren] en [implementeren als deze niet bestaan] om beveiligde instellingen af te dwingen voor uw resources die zijn ingeschakeld voor gebeurtenishubs. 

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Voor meer informatie over de Azure-beleidseffecten:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Veilige configuratie van het besturingssysteem behouden

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Configuratie van Azure-bronnen veilig opslaan

**Richtlijnen:** Als u aangepaste Azure-beleidsdefinities gebruikt voor uw gebeurtenishubs of gerelateerde bronnen, gebruikt u Azure Repos om uw code veilig op te slaan en te beheren.

Code opslaan in Azure DevOps:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos-documentatie:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Afbeeldingen van aangepaste besturingssysteemen veilig opslaan

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Hulpprogramma's voor systeemconfiguratiebeheer implementeren

**Richtlijnen:** Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.EventHub' om aangepaste beleidsregels te maken om systeemconfiguraties te waarschuwen, te controleren en af te dwingen. Bovendien ontwikkelt u een proces en pijplijn voor het beheren van beleidsuitzonderingen.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Systeemconfiguratiebeheertools implementeren voor besturingssystemen

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Geautomatiseerde configuratiebewaking implementeren voor Azure-services

**Richtlijnen:** Gebruik Azure Policy-aliassen in de naamruimte 'Microsoft.EventHub' om aangepaste beleidsregels te maken om systeemconfiguraties te waarschuwen, te controleren en af te dwingen. Gebruik Azure-beleid [audit], [weigeren] en [implementeren als deze niet bestaan] om configuraties voor uw Azure Event Hubs-implementaties en gerelateerde resources automatisch af te dwingen.

Azure-beleid configureren en beheren:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Geautomatiseerde configuratiebewaking voor besturingssystemen implementeren

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

### <a name="711-manage-azure-secrets-securely"></a>7.11: Azure-geheimen veilig beheren

**Richtlijnen:** gebruik Managed Service Identity in combinatie met Azure Key Vault om het beheer van gedeelde toegangshandtekeningen voor uw Azure Event Hubs te vereenvoudigen en te beveiligen voor azure-beheer van de toegang. Zorg ervoor dat Key Vault soft-delete is ingeschakeld.

Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot bronnen van Event Hubs:https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Door de klant beheerde sleutels configureren voor gebeurtenishubs:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Hoe te integreren met Azure Managed Identities:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Een key vault maken:https://docs.microsoft.com/azure/key-vault/quick-create-portal

Key Vault-verificatie voorzien van een beheerde identiteit:https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: Identiteiten veilig en automatisch beheren

**Richtlijnen:** Voor Azure-virtuele machines of webtoepassingen die worden uitgevoerd op Azure App Service die worden gebruikt om toegang te krijgen tot uw gebeurtenishubs, gebruikt u Managed Service Identity in combinatie met Azure Key Vault om Azure Event Hubs te vereenvoudigen en te beveiligen. Zorg ervoor dat Key Vault soft-delete is ingeschakeld.

Beheerde identiteiten gebruiken om Azure-services een automatisch beheerde identiteit te bieden in Azure Active Directory (AD). Met Beheerde identiteiten u zich verifiëren voor elke service die Azure AD-verificatie ondersteunt, inclusief Azure Key Vault, zonder referenties in uw code.

Een beheerde identiteit verifiëren met Azure Active Directory om toegang te krijgen tot Gebeurtenishubsbronnen:https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Door de klant beheerde sleutels configureren voor gebeurtenishubs:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Beheerde identiteiten configureren:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Hoe te integreren met Azure Managed Identities:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

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

**Richtlijnen:** scan vooraf alle inhoud die wordt geüpload naar niet-compute Azure-bronnen, zoals Azure Event Hubs, App Service, Data Lake Storage, Blob Storage, Azure Database voor PostgreSQL, enz. Microsoft heeft in deze gevallen geen toegang tot uw gegevens.

Microsoft-antimalware is ingeschakeld op de onderliggende host die Azure-services ondersteunt (bijvoorbeeld Azure Cache voor Redis), maar wordt niet uitgevoerd op inhoud van klanten.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Ervoor zorgen dat antivirussoftware en -handtekeningen worden bijgewerkt

**Richtsnoeren**: Niet van toepassing; deze aanbeveling is bedoeld voor rekenresources.

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Niet van toepassing

## <a name="data-recovery"></a>Gegevensherstel

*Zie [Beveiligingsbeheer: Gegevensherstel](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery)voor meer informatie.*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Zorg voor regelmatige geautomatiseerde back-ups

**Richtlijnen**: Configureer herstel van georampen voor Azure Event Hubs. Wanneer hele Azure-regio's of datacenters (als er geen beschikbaarheidszones worden gebruikt) downtime ervaren, is het van cruciaal belang dat gegevensverwerking in een andere regio of datacenter blijft werken. Als zodanig zijn Geo-disaster recovery en Geo-replicatie belangrijke functies voor elke onderneming. Azure Event Hubs ondersteunt zowel geo-disaster recovery als geo-replicatie, op naamruimteniveau. 

Inzicht in geo-disaster recovery voor Azure Event Hubs:https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Azure Security Center-bewaking**: Momenteel niet beschikbaar

**Verantwoordelijkheid**: Klant

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Complete systeemback-ups uitvoeren en een back-up maken van beheerde sleutels van een klant

**Richtlijnen:** Azure Event Hubs biedt versleuteling van gegevens in rust met Azure Storage Service Encryption (Azure SSE). Event Hubs is afhankelijk van Azure Storage om de gegevens op te slaan en standaard worden alle gegevens die zijn opgeslagen met Azure Storage versleuteld met door Microsoft beheerde sleutels. Als u Azure Key Vault gebruikt voor het opslaan van door de klant beheerde sleutels, zorgt u voor regelmatige geautomatiseerde back-ups van uw sleutels.

Zorg voor regelmatige geautomatiseerde back-ups van uw Key Vault Secrets met de volgende PowerShell-opdracht: Backup-AzKeyVaultSecret

Sleutels configureren voor het versleutelen van Azure Event Hubs-gegevens in rust:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Een back-up maken van Key Vault Secrets:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Alle back-ups valideren, inclusief door de klant beheerde sleutels

**Richtlijnen**: Test de restauratie van back-up seinen van door de klant beheerde sleutels.

 

Sleutelkluissleutels herstellen in Azure:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: Zorg voor bescherming van back-ups en door klanten beheerde sleutels

**Richtlijnen:** Schakel soft-delete in Key Vault in om sleutels te beschermen tegen onbedoelde of kwaadwillige verwijdering. Azure Event Hubs vereist door de klant beheerde sleutels om Soft Delete en Do Not Purge te configureren.

Configureer soft delete voor Azure Storage-account dat wordt gebruikt voor het vastleggen van gebeurtenishubsgegevens. Houd er rekening mee dat deze functie nog niet wordt ondersteund voor Azure Data Lake Storage Gen 2.

Soft-delete inschakelen in Key Vault:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Stel een sleutelkluis in met sleutels:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Soft delete voor Azure Storage blobs:https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

## <a name="incident-response"></a>Reageren op incidenten

*Zie [Beveiligingscontrole: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response)voor meer informatie.*

### <a name="101-create-an-incident-response-guide"></a>10.1: Een gids voor incidentrespons maken

**Richtlijnen**: Zorg ervoor dat er schriftelijke incidentresponseplannen zijn die de rollen van het personeel en de fasen van incidentafhandeling/-beheer definieert.

Werkstroomautomatiseringen configureren binnen Azure Security Center:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Een incidentscore- en prioriteringsprocedure maken

**Richtlijnen**: Security Center kent een ernst toe aan waarschuwingen, zodat u prioriteit geven aan de volgorde waarin u elke waarschuwing bijwoont, zodat wanneer een resource wordt gecompromitteerd, u er meteen bij komen. De ernst is gebaseerd op hoe zeker Security Center is in de bevinding of de analytische gebruikt om de waarschuwing en het betrouwbaarheidsniveau dat er kwaadaardige bedoelingen achter de activiteit die leidde tot de waarschuwing.

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="103-test-security-response-procedures"></a>10.3: Procedures voor beveiligingsrespons testen

**Richtlijnen**: Voer oefeningen uit om de incidentresponsmogelijkheden van uw systemen op een regelmatige cadans te testen. Identificeer zwakke punten en hiaten en herzie het plan indien nodig.

Raadpleeg de publicatie van NIST: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Contactgegevens beveiligingsincidenten verstrekken en waarschuwingsmeldingen configureren voor beveiligingsincidenten

**Richtlijnen:** Contactgegevens voor beveiligingsincidenten worden door Microsoft gebruikt om contact met u op te nemen als het Microsoft Security Response Center (MSRC) ontdekt dat de gegevens van de klant zijn geraadpleegd door een onwettige of onbevoegde partij.  Bekijk incidenten na het feit om ervoor te zorgen dat problemen worden opgelost. 

De beveiligingscontactpersoon azure security center instellen:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Beveiligingswaarschuwingen opnemen in uw incidentresponsesysteem

**Richtlijnen:** exporteer uw Azure Security Center-waarschuwingen en -aanbevelingen met de functie Continue exporteren. Met Continue export u waarschuwingen en aanbevelingen handmatig of doorlopend exporteren. U de Azure Security Center-gegevensconnector gebruiken om de waarschuwingen sentinel te streamen.

Continue export configureren:https://docs.microsoft.com/azure/security-center/continuous-export

Waarschuwingen streamen naar Azure Sentinel:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatiseer de reactie op beveiligingswaarschuwingen

**Richtlijnen:** Gebruik de functie Workflowautomatisering in Azure Security Center om automatisch reacties te activeren via 'Logic Apps' over beveiligingswaarschuwingen en aanbevelingen.

Workflowautomatisering en logische apps configureren:https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center-bewaking:** niet van toepassing

**Verantwoordelijkheid**: Klant

## <a name="penetration-tests-and-red-team-exercises"></a>Penetratietests en Red Team-oefeningen

*Zie [Security Control: Penetration Tests en Red Team Exercises](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)voor meer informatie.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Voer regelmatig penetratietests uit van uw Azure-resources en zorg voor herstel van alle kritieke beveiligingsbevindingen binnen 60 dagen

**Richtlijnen:** Volg de Microsoft Rules of Engagement om ervoor te zorgen https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1dat uw penetratietests niet in strijd zijn met het Microsoft-beleid: .
Meer informatie over de strategie en uitvoering van Red Teaming en live site penetratietesten van microsoft op basis van door Microsoft beheerde cloudinfrastructuur, -services en -toepassingen vindt u hier:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center-bewaking:** Ja

**Verantwoordelijkheid**: Klant

## <a name="next-steps"></a>Volgende stappen

- Bekijk de [Azure Security Benchmark](https://docs.microsoft.com/azure/security/benchmarks/overview)
- Meer informatie over [Azure Security Baselines](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
